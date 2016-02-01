# Early and Late Binding
JavaScript在呼叫的時候設定this的值，但有時this的值並非預期。
## this的值並非預期的狀況

    function Menu(elem){
     //...
    };
    
    var menu = new Menu(document.createElement('div'));

### setTimeout

    function Menu(elem){
      setTimeout(function(){
        console.log(this); //Window
      }, 500);
    };
    
    var menu = new Menu(document.createElement('div'));

this是參考到Window，這是因為setTimeout永遠是在window context下執行。

### onclick

    function Menu(elem){
      elem.onclick = function(){
        console.log(this); //elem, not Menu
      };
    };
    
    var menu = new Menu(document.createElement('div'));

event handler會將this設給傳入的元素本身。

### Private method / local function

    function Menu(elem){
      function privateMethod(){
        console.log(this);
      };
      privateMethod(); //Window
    };
    
    var menu = new Menu(document.createElement('div'));

解法有三種 - Binding with `var self = this`、Early binding和Late binding。

## Binding with var self = this
我們將參考到menu的this存在變數self裡面，避免進入setTimeout後遺失。

    function Menu(elem){
      var self = this;
      setTimeout(function(){
        console.log(self); //Menu
      });
    };
    
    new Menu(document.createElement('div'));

## Early binding

    function bind(func, fixThis){
      return function(){
        return func.apply(fixThis, arguments);
      }
    };
    
    function Menu(elem){
      elem.onclick = bind(function(){
        console.log(this); //Menu
      });
    };
    
    new Menu(document.createElement('div'));

### Function.prototype.bind

    Function.prototype.bind = Function.prototype.bind || function(fixThis){
      var func = this;
      return function(){
        return func.apply(fixThis, arguments);
      }
    };

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

#### Private method / local function

    function Menu(elen){
      var privateMethod = function (){
        console.log(this);
      }.bind(this);
    
      privateMethod();
    };

new Menu(document.createElement('div'));

### Comparison
比較 bind 和 `var self = this` 的差異。
- bind不需要包在closure(閉包)之內。bind綁定是即時且永久的。
- bind必須要在每個要使用的funciton加上去。
- `var self = this`必須額外加上去。

## Late binding
呼叫的時候才做綁定的動作。

### Problems of early binding

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

預期印出 「SuperMenu」，但其實印出的是「Menu」。

### The late binding in action

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
    
## Summary
三種binding方法：
- 將this存在區域變數中(`var self = this`)，並在funciton內使用。
- 建立一個bind funciton或修改Function.prototype.bind。
- 使用method呼叫late binding。

---
####References
- [Early and Late Binding](http://javascript.info/tutorial/binding)
- [Function.prototype.bind()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
