# The "constructor" property
物件有內建的屬性「constructor」，意即「參考建立此物件的function」。
## 範例
我們來建立一個簡單的constructor，然後看看新的物件是否有正確的constructor的值。

    function Rabbit(){};
    var rabbit = new Rabbit();
    console.log(rabbit.constructor == Rabbit); //true

## The origins of constructor
當我們宣告一個函數，如下：

    function Rabbit() { 
        //... 
    };

interpreter建立一個新的函數物件，而此物件的prototype屬性也被建立和給值。prototype的預設值與其constructor相同，在此範例中為Rabbit。意即：

    Rabbit.prototype = { constructor: Rabbit };

所以，當由constructor練立一個新的物件時(例如：使用Rabbit建立rabbit)，`Rabbit.prototype`成為這個被建立物件的`__proto__`的值，且constructor成為物件可被存取的屬性。如下所示：

    rabbit.__proto__ == { constructor: Rabbit };

我們來做一個簡單的檢查吧！

    function Rabbit(){};
    
    var rabbit = new Rabbit();
    console.log(rabbit.hasOwnProperty('constructor')); //false
    console.log(rabbit.__proto__.hasOwnProperty('constructor')); //true
    console.log(Rabbit.prototype.hasOwnProperty('constructor')); //true
    
## Keeping constructor up to date
呼叫 `rabbit.constructor` 事實上是回傳 `rabbit.__proto__.constructor` 。這會有一個副作用，參考以下範例。

    function Rabbit(){}; //(1)
    Rabbit.prototype = {}; //(2)
    
    var rabbit = new Rabbit();
    console.log(rabbit.constructor == Object); //true

原先在(1)rabbit的constructor是Rabbit，但由於在(2)中將 Rabbit.prototype 的值設為空物件，因此 `rabbit.__proto__` 在 `Rabbit.prototype` 找不到 `constructor` 這個屬性的值，只好繼續往上找，最後在 Object.prototype 找到 constructor 的值為 Object。

## Summary
- 物件有內建的屬性「constructor」，意即參考「建立此物件的function」。
- 當prototype被修改，其constructor也會丟失。
- 如果我們希望在繼承後擁有正確的constructor，就必須自行設定，如下：

		function Rabbit(){};
		Rabbit.prototype = { constructor: Rabbit } 
		var rabbit = new Rabbit();
		console.log(rabbit.constructor == Rabbit); //true

---
#### 參考資料
- [The "constructor" property](http://javascript.info/tutorial/constructor)
