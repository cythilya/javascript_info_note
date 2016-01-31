# Factory Constructor Pattern
不使用new來宣告新物件。

## Declaration

	function Animal(name){
	  return {
	    run: function(){
	      console.log(name + ' is running!');
	    }
	  }
	}

	var animal = Animal('fox');
	animal.run();

## Inheritance

	function Rabbit(name){
	  var rabbit = Animal(name);
	  rabbit.bounce = function(){
	    this.run();
	    console.log(name + ' bounces to the skies!');
	  };

	  return rabbit;
	};

	var rabbit = Rabbit('rab');
	rabbit.bounce();

## Private/protected methods (encapsulation)

	function Bird(name){
	  var speed = 100; //private property
	  function openWings(){ //private method
	    console.log('open wings.');
	  };

	  function doFly(){
	    openWings();
	  };

	   var self = {
	      //public methods
	      fly: function(){
	        openWings();
	        this.move();
	      },
	      move: function(){
	        console.log('move.');
	      }
	    };

	  return self;
	};

var bird = Bird('Jack');
bird.fly(); //open wings. move.
bird.move(); //move.

<!--
## Summary
### Comparison with All-in-one constructor
-->