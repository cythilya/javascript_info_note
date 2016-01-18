# Prototypal Inheritance
對大多數的語言來說，它們擁有「Class」和「Object」，而Class繼承其它的Class。對於JavaScript來說，繼承是使用prototype來實作的，意即沒有Class，而是由物件繼承其它的物件來達成繼承。

## Inheritance, the __proto__

	var animal = { eats: true },
		rabbit = { jumps: true };
	 
	rabbit.__proto__ = animal; //inherit
	console.log(rabbit.eats); //true

當存取rabbit的property「eats」時，由於無法在rabbit中找到，因此往父物件尋找，終於在animal找到，並且值是true。

如果存取rabbit的property可在自身找到，就不需要往父物件尋找。
例如，假設存取rabbit的property「jumps」，由於可在rabbit找到，並且值是true因此就不往父物件找了。

「 `__proto__`」是一個連接子物件和父物件的link，讓interpreter在子物件找不到屬性時，可以依循這條link往父物件繼續尋找。

由以上的範例可知，我們可以在animal放置一個method，然後子物件rabbit也可以使用這個method。

	var animal = { 
		eat: function(){
			console.log("I'm full");
			this.full = true;
		}
	},
	rabbit = { 
		jump: function(){
			//do something
		} 
	};
	 
	rabbit.__proto__ = animal;
	rabbit.eat(); //I'm full

rabbit.eat() 這個method經由兩個步驟被執行：

- Step 1：Interpreter在rabbit物件中尋找eat() method，但沒有找到，因此循著`rabbit.__proto__`往父物件animal尋找，並且找到了。
- Step 2：此時的eat() method中的this是指rabbit物件。

我們得到以下結論：

- 物件呼叫其父物件的method，其中的this會被設定給這個物件。這就是繼承的意思。
- 對於物件來說，`__proto__`被稱為prototype(原型)。因此，animal是rabbit的prototype。

## Object.create, Object.getPrototypeOf
「`__proto__`」並非標準的屬性，只有Chrome和Firefox支援，其它瀏覽器則是隱藏此屬性於內部。所有目前流行的瀏覽器(除了Opera，IE則是版本9以上)，對此屬性支援兩個標準method - Object.create和Object.getPrototypeOf。  

### Object.create(proto[, props])
使用animal `__proto__` 建立一個空物件rabbit，而且可在rabbit中建立自己的屬性jumps。

	var animal = { eats: true },
		rabbit = Object.create(animal);
	
	rabbit.jumps = true;
	
	console.log(rabbit.eats); //true
	console.log(rabbit.jumps); //true

### Object.getPrototypeOf(obj)
回傳 `obj.__proto__` 的值。這個method是在標準規格裡面的，所以不支援 `__proto__` 的瀏覽器仍可使用此method。

	var animal = { eats: true },
		rabbit = Object.create(animal);
	
	console.log(Object.getPrototypeOf(rabbit) === animal); //true

因此，幾乎所有主要瀏覽器都允許「讀取」`__proto__`的值，但不允許修改它。

## The prototype
有一個較好且跨瀏覽器的方法來設定 `__proto__`，而這個方法需要建構式的幫忙來設定 `__proto__` 的值。

	var animal = { eats: true };
	
	function Rabbit(name){
		this.name = name;
	};
	
	Rabbit.prototype = animal; //set __proto__ = animal for all objects created by new Rabbit
	
	var rabbit = new Rabbit('John');
	
	console.log(rabbit.eats); //true

## Crossbrowser Object.create(proto)
Object.create(proto)允許直接繼承給予的物件，可以使用我們自己撰寫的method - inherit 來模仿它而達到一樣的功能。因此使用 inherit(animal)的效果等同於使用 Object.create(animal) - 建立一個新的空物件，且 `object.__proto__ = animal` 。

	function inherit(proto){
		function F(){}; //(1)
		F.prototype = proto; //(2)
		return new F; //(3)
	};
	
	var animal = { eats: true },
		rabbit = inherit(animal);
	
	console.log(rabbit.eats);

解說如下：

1. 建立新函式 F，新函式 F由於沒有被設定任何值，因此F會建立一個空的物件。
2. F.prototype被設定值為proto。
3. 回傳由F建立的空物件。空物件的prototype為proto，意即 `object.__proto__ = proto` 。

## hasOwnProperty
hasOwnProperty method 允許檢查其屬性是否屬於某個物件或其prototype。

	function Rabbit(name){
		this.name = name;
	};
	
	Rabbit.prototype = { eats: true };
	
	var rabbit = new Rabbit('John');
	
	console.log(rabbit.hasOwnProperty('eats')); //false, in prototype
	console.log(rabbit.hasOwnProperty('name')); //true, in object

## Looping with/without inherited properties
將所有的屬性利用for loop顯示出來。

	function Rabbit(name) {
	  this.name = name;
	};
	 
	Rabbit.prototype = { eats: true };
	 
	var rabbit = new Rabbit('John');
	 
	for(var p in rabbit) {
	  console.log (p + " = " + rabbit[p]); // outputs both "name" and "eats"
	}


利用method hasOwnProperty來篩選只有物件本身擁有的屬性，而非在prototype中。

	function Rabbit(name) {
	  this.name = name;
	};
	 
	Rabbit.prototype = { eats: true };
	 
	var rabbit = new Rabbit('John');
	 
	for(var p in rabbit) {
	  if (!rabbit.hasOwnProperty(p)) continue; // filter out "eats"
	  console.log (p + " = " + rabbit[p]); // outputs only "name"
	}

## Summary
- 使用屬性 `__proto__` 來實作繼承
	- 如果在物件中找不到此屬性，則會沿著 `__proto__` 到父物件尋找此屬性的值。
	- this的值會被設定給物件，而非prototype。
	- 屬性設定：obj.prop = val；屬性刪除：delete obj.prop。 
- 管理 `__proto__`
	- Firefox/Chrome可以直接存取 `__proto__` ，而大多數現行的瀏覽器只提供讀取(利用Object.getPrototypeOf(obj))而沒有修改的權限。
	- 給予特定prototype的空物件可使用Object.create(proto)來達到繼承的目的，或使用自行實作的function。
- 其它method
	- for..in loop 可列出所有此object自己和prototype的屬性。
	- obj.hasOwnProperty(prop) 在屬性屬於此物件本身(而非屬於prototype)時會回傳true。

---
####參考資料
- [Prototypal inheritance | JavaScript Tutorial](http://javascript.info/tutorial/inheritance)
- [JavaScript - Code Reuse Patterns](http://cythilya.blogspot.tw/2015/06/javascript-code-reuse-patterns.html)
