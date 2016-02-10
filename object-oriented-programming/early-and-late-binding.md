# Early and Late Binding
JavaScript在呼叫的時候設定this的值，而這個this的值有可能不是我們預期的結果。下面有幾個範例。

## this的值並非預期的狀況
以下都會由Menu這個建構子作為範例。

    function Menu(elem){
     //...
    };
    
	var menu = new Menu(document.createElement('div'));

### setTimeout
this是參考到Window，而非menu。這是因為setTimeout永遠是在window context下執行。

    function Menu(elem){
      setTimeout(function(){
        console.log(this); //Window
      }, 500);
    };
    
    var menu = new Menu(document.createElement('div'));

### onclick
event handler永遠是將this設定給傳入的物件(例如：在此是DOM Element「elem」 )，而非menu。但menu才是我們預期的結果。

    function Menu(elem){
      elem.onclick = function(){
        console.log(this); //elem, not Menu
      };
    };
    
    var menu = new Menu(document.createElement('div'));

### Private Method / Local Function
private method 或 local function內的this若沒有特別指定，則值是Window。

    function Menu(elem){
      function privateMethod(){
        console.log(this);
      };
      privateMethod(); //Window
    };
    
    var menu = new Menu(document.createElement('div'));

我們需要一些解法來解決這個問題 - 能在function內存取到menu物件。解法有三種 - 使用 `var self = this` 做綁定、Early binding和Late binding。

## Binding with var self = this
在進入setTimeout之前，我們先將正確的this儲存在區域變數self裡面，避免this進入setTimeout後變成Window。然後我們便可在任何地方使用self了。除了setTimeout，event handle和private method/local function也可以使用此解法。

    function Menu(elem){
      var self = this;
      setTimeout(function(){
        console.log(self); //Menu
      });
    };
    
    new Menu(document.createElement('div'));

## Early Binding
我們可以使用bind這樣的helper function - bind接受參數(函式func和this)並回傳一個閉包 - 將this替換成此func的內容狀態。這等於是強制替換this的值。

    function bind(func, fixThis){
      return function(){
        return func.apply(fixThis, arguments);
      }
    };
    
    function Menu(elem){
      elem.onclick = bind(function(){
        console.log(this); //Menu
      }, this);
    };
    
    new Menu(document.createElement('div'));

### Function.prototype.bind
其實我們不用那麼辛苦 - 自己寫一個bind這樣的helper function，直接使用原生的[Function.prototype.bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)即可！如果舊瀏覽器不支援，我們也可以像以下這樣擴充它。

    Function.prototype.bind = Function.prototype.bind || function(fixThis){
      var func = this;
      return function(){
        return func.apply(fixThis, arguments);
      }
    };

因此，setTimeout、event handle和private method/local function也可以使用此解法了。

#### setTimeout    

    function Menu(elem){
      setTimeout(function(){
         console.log(this); //Menu
      }.bind(this), 1000);
    };
    
    new Menu(document.createElement('div'));

#### onclick

    function Menu(elem) {
      elem.onclick = function() {
        console.log(this); //Menu
      }.bind(this);
    };
    new Menu(document.createElement('div'));

#### Private Method / Local Function

    function Menu(elen){
      var privateMethod = function (){
        console.log(this);
      }.bind(this);
    
      privateMethod();
    };

new Menu(document.createElement('div'));

### 比較 bind 和 `var self = this` 的差異
- bind不需要包在closure(閉包)之內。bind綁定是即時且永久的。bind勝！
- bind必須要在每個要使用的funciton加上去。`var self = this` 勝！
- `var self = this`必須額外加上去。bind勝！

## Late Binding
呼叫的時候才做綁定的動作。

### Early Binding的問題

    function bind(func, fixThis) { // using custom bind for simplicity
      return function() {
        return func.apply(fixThis, arguments);
      }
    }
     
    function Menu(elem) {
      this.sayHi = function() { console.log('Menu'); };
      elem.onclick = bind(this.sayHi, this);
    }
     
    function SuperMenu(elem) {
      Menu.apply(this, arguments);
      this.sayHi = function() { console.log('SuperMenu'); };
    }
     
    new SuperMenu(document.body);

底下的程式碼預期印出 「SuperMenu」，但其實印出的是「Menu」。這是因為onclicke這個event handler使用的 `this.sayHi` 的this是指Menu，而非SuperMenu。

### 使用Late Binding
修正一下即可。

    function bindLate(funcName, fixThis) { //instead of bind
      return function() {
        return fixThis[funcName].apply(fixThis, arguments);
      }
    };
     
    function Menu(elem) {
      this.sayHi = function() { console.log('Menu'); };
      elem.onclick = bindLate('sayHi', this);
    };
     
    function SuperMenu(elem) {
      Menu.apply(this, arguments);
      this.sayHi = function() { console.log('SuperMenu'); };
    };
     
    new SuperMenu(document.body);
    
## 總結三種binding方法
- 將this存在區域變數中(`var self = this`)。
- 使用bind helper funciton或原生的Function.prototype.bind。
- 使用late binding。

---
####References
- [Early and Late Binding](http://javascript.info/tutorial/binding)
- [Function.prototype.bind()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
