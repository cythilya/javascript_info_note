# Prototypal Inheritance
對大多數的語言來說，它們擁有「Class」和「Object」，而Class繼承其它的Class。對於JavaScript來說，繼承是使用prototype為基礎的，意即沒有Class，而是物件繼承其它的物件。

## Inheritance, the __proto__

	var animal = { eats: true },
		rabbit = { jumps: true };
	 
	rabbit.__proto__ = animal; //inherit
	console.log(rabbit.eats); //true

當存取rabbit的property「eats」時，由於無法在rabbit中找到，因此往繼承的父物件尋找，終於在animal找到，並且值是true。

如果存取rabbit的property可在自身找到，就不需要往繼承的父物件尋找。
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




## Crossbrowser Object.create(proto)
## hasOwnProperty
## Looping with/without inherited properties
## Summary

---
####參考資料
- [Prototypal inheritance | JavaScript Tutorial](http://javascript.info/tutorial/inheritance)