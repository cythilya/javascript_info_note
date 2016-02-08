# Factory Constructor Pattern
「Factory Constructor Pattern」不使用new來宣告新物件，新物件用function call來建立，如下：

var animal = Animal('fox');
var rabbit = Rabbit('rab');

<!-- more -->

## 物件宣告
建構子使用function定義，並回傳一個新物件。

	function Animal(name) {
	  return {
	    run: function() {
	      console.log(name + " is running!")
	    }
	  }
	};

然後我們這樣使用這個建構子來建立新物件，並沒有使用到new。
	
	var animal = Animal('fox'); //fox is running
	animal.run();

## 繼承
「Rabbit」這個物件是繼承「Animal」所建立的建構子，然後我們來做些變化吧。

	function Rabbit(name) {
	  var rabbit = Animal(name); //make animal
	 
	  rabbit.bounce = function() { //mutate
	    this.run(); //rab is running!
	    console.log(name + ' bounces to the skies! :)'); //rab bounces to the skies! :)
	  }
	  return rabbit //return the result
	}
	 
	var rabbit = Rabbit('rab');
	rabbit.bounce()

## 封裝：Private/protected methods
public method可以被外部呼叫，例如；`this.move()`，但不能被private method呼叫。private method，例如；`openWings`，不能參考this - 因此 local function在此無法參考到這個新建立的物件。(但其實我的測試結果是沒有這個問題，如果有大大可以告訴我是發生什麼事情的話，會非常感激的！)

	function Bird(name) {
	  var speed = 100; //private property
	  function openWings() { //private method
	  	console.log('open wings');
	  };
	  return {
	    fly: function() {
	      openWings();
	      this.move();
	    },
	    move: function() {
	    	console.log('move');
	    } 
	  }
	};
	
	var bird = Bird('Jack');
	bird.fly();
	bird.move();


其中一個解法是使用一個local變數儲存物件來回傳。

	function Bird(name) {
	  var speed = 100; //private property
	  
	  function openWings() { //private method
	  	console.log('open wings');
	  };	
	  
	  function doFly() { //private method
	    openWings();
	    self.move();
	  };
	 
	  var self ={
	    fly: function() { 
	    	doFly(); 
	    },
	    move: function() {
	    	console.log('move');
	    }   
	  }
	  return self
	};
	
	var bird = Bird('Jack');
	bird.fly();
	bird.move();

## Summary
- Factory Constructor Pattern不使用new來宣告新物件，新物件用function call來建立
- 繼承：建立父物件，知後修改它即可。
- 區域method和method是private的，物件必須先在閉包中儲存再回傳，這樣才能在公開的method中使用。

### Comparison with [All-in-one constructor](https://github.com/cythilya/javascript-info/blob/master/object-oriented-programming/all-in-one-constructor-pattern.md)

「Factory Constructor Pattern」和「All-in-one constructor」其實結果是相同的，只是寫法上稍為有些不同，選一個你喜歡的即可。

---
####References
- [Factory constructor pattern | JavaScript Tutorial](http://javascript.info/tutorial/factory-constructor-pattern)
<!-- - [JavaScript Object Oriented Programming - Factory Constructor Pattern 筆記](https://github.com/cythilya/javascript-info/blob/master/object-oriented-programming/factory-constructor-pattern.md)：持續更新中... -->