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



## The prototype
## Crossbrowser Object.create(proto)
## hasOwnProperty
## Looping with/without inherited properties
## Summary

---
####參考資料
- [Prototypal inheritance | JavaScript Tutorial](http://javascript.info/tutorial/inheritance)