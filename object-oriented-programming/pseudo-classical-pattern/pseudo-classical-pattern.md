# Pseudo-Classical Pattern
## Pseudo-class declaration
在pseudo-classical pattern中，物件是由「建構子」(constructor)這個函式所建立，並把method放到建構子的prototype中。

	function Animal(name){
		this.name = name;
	};

	Animal.prototype = {
	  canWalk: true,
	  sit: function(){
	  	this.canWalk = false;
	  	console.log(this.name + ' sits down.');
	  }
	};

	var animal = new Animal('Pet'); // (1)
	console.log(animal.canWalk); // true
	animal.sit(); // (2)
	console.log(animal.canWalk); //false

1. 當「new Animal(name)」被呼叫的時候，新物件收到 參考到Animal.prototype的 `__proto__`。
2. `animal.sit` 改變 `animal.canWalk` 的值，所以這個animal物件的canWalk為false，但其餘的仍為true。

### 備註
- method和預設的屬性會放在prototype中。
- prototype中的method，若使用this，指的是目前的物件。所以animal.sit()指的是這一個animal。

## Inheritance
使用 `Rabbit.prototype.__proto__ == Animal.prototype;` 來讓Rabbit繼承Animal。我們使用[inherit](http://javascript.info/tutorial/inheritance#inherit)這個function來做設定。

	function inherit(proto) {
	  function F() {};
	  F.prototype = proto;
	  return new F;
	};

	function Animal(name){
		this.name = name;
	};

	Animal.prototype = {
	  canWalk: true,
	  sit: function(){
	  	this.canWalk = false;
	  	console.log(this.name + ' sits down.');
	  }
	};

	function Rabbit(name){
		this.name = name;
	};

	Rabbit.prototype = inherit(Animal.prototype);

	Rabbit.prototype.jump = function(){
		this.canWalk = true;
		console.log(this.name + ' jumps!');
	};
	 
	var rabbit = new Rabbit('John');
	console.log(rabbit.canWalk); //true
	rabbit.sit(); //John sits down. 
	console.log(rabbit.canWalk); //false
	rabbit.jump(); //John jumps!

<!--
## Calling superclass constructor
「superclass」這個建構子不是被自動呼叫的，我們使用「apply」將Animal這個函式套用到目前的物件上。這樣就能將Animal建構子的內文狀態設給目前使用的物件。

	function Rabbit(name) {
	  Animal.apply(this, arguments);
	};
-->

## Overriding a method (polymorphism)
當method被覆寫，我們仍可能會希望能呼叫之前尚未被覆寫的method。

### Calling a parent method after overriding
我們可以直接呼叫parent prototype。

	function inherit(proto) {
	  function F() {};
	  F.prototype = proto;
	  return new F;
	};

	function Animal(name){
		this.name = name;
	};

	Animal.prototype = {
	  canWalk: true,
	  sit: function(){
	  	this.canWalk = false;
	  	console.log(this.name + ' sits down.');
	  }
	};

	function Rabbit(name){
		this.name = name;
	};

	Rabbit.prototype = inherit(Animal.prototype);

	var rabbit = new Rabbit('John');

	Rabbit.prototype.sit = function() {
	  console.log(this.name + ' sits in a rabbity way.');
	}

	rabbit.sit(); //使用覆寫後的method

	Rabbit.prototype.sit = function() {
	  console.log('calling superclass sit:');
	  Animal.prototype.sit.apply(this, arguments); //使用尚未被覆寫的method
	}

	rabbit.sit();

備註：All parent methods are called with apply/call to pass current object as this. A simple call Animal.prototype.sit() would use Animal.prototype as this.	

我們也可以直接在rabbit這個物件上做覆寫的動作。

	rabbit.sit = function() {
	  alert('A special sit of this very rabbit ' + this.name);
	}

### Sugar: removing direct reference to parent
因為在重構的時候可能修改了parent，所以必須移除對parent constructor的參考。

	function extend(Child, Parent){
	  Child.prototype = inherit(Parent.prototype);
	  Child.prototype.constructor = Child;
	  Child.parent = Parent.prototype;
	};

	function inherit(proto) {
	  function F() {};
	  F.prototype = proto;
	  return new F;
	};

	function Animal(name){
		this.name = name;
	};

	Animal.prototype = {
	  canWalk: true,
	  sit: function(){
	  	this.canWalk = false;
	  	console.log(this.name + ' sits down.');
	  },
	  run: function(){
	  	this.canWalk = false;
	  	console.log(this.name + ' run fast.');  	
	  }
	};

	function Rabbit(name) {
		this.name = name;
	  Rabbit.parent.constructor.apply(this, arguments); //super constructor
	};
	 
	extend(Rabbit, Animal);
	 
	Rabbit.prototype.run = function() {
	    Rabbit.parent.run.apply(this, arguments); //parent method
	};

	var rabbit = new Rabbit('John');
	console.log(rabbit.canWalk); //true
	rabbit.sit(); //John sits down. 
	console.log(rabbit.canWalk); //false
	rabbit.run(); //John run fast.

因此，之後對Animal的修改都只需要回頭修正Animal和extend即可。

## Private/protected methods (encapsulation)
JavaScript中的private/protected method並沒有實質上的支援，可參考[Private/protected methods (encapsulation)](http://javascript.info/tutorial/pseudo-classical-pattern#private-protected-methods-encapsulation)。

## Static methods and properties
static property/method是被直接設定給建構子的。
  
	function Animal() {
	  Animal.count++;
	};

	Animal.count = 0;
	 
	new Animal();
	new Animal();
	 
	console.log(Animal.count); //2

## Summary
總結以上，我們得到完整的Pseudo-Classical Pattern程式碼。

	function extend(Child, Parent){
	  Child.prototype = inherit(Parent.prototype);
	  Child.prototype.constructor = Child;
	  Child.parent = Parent.prototype;
	};

	function inherit(proto) {
	  function F() {};
	  F.prototype = proto;
	  return new F;
	};

	// --------- the base object ------------
	function Animal(name) {
	  this.name = name;
	};
	 
	// methods
	Animal.prototype.run = function() {
	  console.log(this + " is running!");
	};
	 
	Animal.prototype.toString = function() {
	  return this.name;
	};
	  
	// --------- the child object -----------
	function Rabbit(name) {
	  Rabbit.parent.constructor.apply(this, arguments);
	};
	 
	// inherit
	extend(Rabbit, Animal);
	 
	// override
	Rabbit.prototype.run = function() {
	  Rabbit.parent.run.apply(this);
	  console.log(this + " bounces high into the sky!");
	}
	 
	var rabbit = new Rabbit('Jumper');
	rabbit.run();
	
---
### References
- [Pseudo-classical pattern](http://javascript.info/tutorial/pseudo-classical-pattern)
- [Function.prototype.apply()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)