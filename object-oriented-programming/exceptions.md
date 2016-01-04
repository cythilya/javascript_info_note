#Exceptions
「例外(Exceptions)」是一個特別且重要的處理錯誤的方法。
##Check-first error handling
假設我們要執行func這個method，首先會檢查這個method是否存在。

	if (window.func) {
	  func();
	}

但檢查了是否存在，不代表型別正確，因此我們必須再檢查這個method的型別是否為function。
	
	if (typeof(func) == 'function') {
	  func();
	}

但意外或錯誤是永遠檢查不完的，例如，這個method的內部可能有些問題。因此，我們可以改用「Try..Catch」來處理。

##The try..catch construct
上一個例子是預先把可能發生的問題都檢查一次，例如：存在與否、型別是否正確，如果通過檢查再執行。而「Try..Catch」從另外一個角度來處理 - 先執行，如果有錯再處理。範例如下。

	try {
	  func();
	} 
	catch(e) {
	  alert(e);
	}

這個「e」會告訴我們到底發生什麼問題。

	try {
	  var a = 5, res = func(a);
	
	  if (res > 0) {
	    doA();
	  }
	  else {
	    doB();
	  }
	} 
	catch(e) {
	  console.log("name: " + e.name); //name: ReferenceError
	  console.log("message: " + e.message); //message: doB is not defined
	}

由錯誤訊息可知，doB這個function沒有被定義，因此無法執行而出錯。

##The full form of try..catch..finally

	try {
	   //try statemenets ..
	} 
	catch(exception) {
	   //catch statements ..
	} 
	finally {
	   //finally statements ..
	}

<!--
###try..catch..finally and return
##The throw statement
###A validator example
###Changes in the usage pattern
###Comparison
##Exception analysis and rethrow
##Summary
-->

##未完代續...

---
##Reference
- [Object Oriented Programming | JavaScript Tutorial](http://javascript.info/tutorial/object-oriented-programming)
- [Exceptions | JavaScript Tutorial](http://javascript.info/tutorial/exceptions)

<!--
##Agenda
- Check-first error handling
- The try..catch construct
	- Obtaining the stack
- The full form of try..catch..finally
	- try..catch..finally and return
- The throw statement
	- A validator example
	- Changes in the usage pattern
	- Comparison
- Exception analysis and rethrow
- Summary
-->