# All-in-one Constructor Pattern
所有的method和property都放在consctructor中，而不使用prototype。

## Declaration

	function Animal(name){
	  this.name = name;
	  this.run = function(){
	    console.log('running '+ this.name);
	  }
	};

	var animal = new Animal('Foxie');
	animal.run();

## Inheritance
To create a Rabbit, inheriting from Animal:

- First apply Animal constructor to this. We’ve got an Animal
- Modify this, add more methods to get a Rabbit.

	function Rabbit(name){
	  Animal.apply(this, arguments);
	  this.bounce = function(){
	    console.log('bouncing ' + this.name);
	  };
	};

	var rabbit = new Rabbit('Rab');
	rabbit.bounce();
	rabbit.run();

## Overriding (polymorphism)

	function Rabbit(name){
	  Animal.apply(this, arguments);
	  var parentRun = this.run; //keep parent method
	  this.run = function(){
	    console.log('bouncing ' + this.name);
	    parentRun.apply(this); //call parent method
	  };
	};

	var rabbit = new Rabbit('Rab');
	rabbit.run(); //inherited method

## Private/protected methods (encapsulation)
在建構子之內的變數和函數視為private，設定給this的視為public。

	function Animal(name) {
	  this.name = name;
	  this.run = function() {
	    console.log('running ' + this.name);
	  }
	}

	function Rabbit(name){
	  Animal.call(this, 'Mr.' + name.toUpperCase());
	  var created = new Date(); //private
	  function sayHi(){ //private
	    console.log("I'm talking rabbit " + name);
	  }
	  this.report = function(){
	    sayHi.apply(this);
	    console.log('Created at ' + created);
	  };
	};

	var rabbit = new Rabbit('Rab');
	rabbit.report();

protected method並沒有實質上的支援，可參考[Private/protected methods (encapsulation)](http://javascript.info/tutorial/pseudo-classical-pattern#private-protected-methods-encapsulation)。

## Summary
- 在建構子完整描述物件。
- 在目前物件呼叫父建構子後完成繼承。
- 在建構子之內的變數和函數視為private，設定給this的視為public。
- protected method並沒有實質上的支援，撰寫規則是使用底線「_」。
- 使用在this中覆寫屬性，而也會保留原先的property來重複使用。

### Comparison with pseudo-classical pattern
- 在All-in-one Constructor Pattern無法使用 `rabbit instanceof Animal` ，因為沒有做設定prototype的動作。
- 速度較慢且需要較多記憶體。由於每一個物件都擁有自己的method，而非使用prototype共享。
- 實作private method和property，是較為安全和快速的。