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

首先執行try區塊內的程式碼，如果沒有出錯，則catch區塊會被忽略不執行。如果有錯誤產生，則exception會被設定值，catch區塊會被執行。無論如何，最後，finally區塊都會被執行。

###try..catch..finally and return
在try區塊執行return statments，控制權會在執行完finally區塊後才交回給原呼叫的function。所以如下程式碼所示，會先alert 「done」，再alert 「2」。

	function inc(a) {
	  try {
	    return a+1;
	  } 
	  catch(e) {
	    //catch statements...
	  } 
	  finally {
	    alert('done'); //1. alert "done"
	  }
	}
	alert( inc(1) ); //2. alert "2"

##The throw statement
基本上，錯誤可以分為兩種：「Programmatic errors」和「Execution flow errors」。

- Programmatic errors：程式撰寫錯誤，例如：錯字。
- Execution flow errors：執行時的錯誤，例如：使用者輸入不符合預定格式的資料，程式提示格式有誤並重新輸入。

因此，使用try...catch與throw於Execution flow errors是一個不錯的選擇，範例如下。

	try {
	  throw 5;
	} 
	catch(e) {
	  alert("Caught: " + e);
	}

###A validator example
假設我們需要一個年齡的驗證工具，幫助我們檢查使用者輸入的年齡是否合法。如果沒有輸入任何東西，則跳出；如果輸入非文字，則丟出錯誤；如果是數字，則顯示可接受的訊息。

	function validatorAge(age){
		if(age === ''){
			return; //no age to valid
		}
		age = +age;
	
		if(isNaN(age)){
			throw {
				name: 'BadAge',
				message: 'Age out of range'
			}
		}
	}
	
	try {
		var age = prompt('Enter your age');
		validatorAge(age);
		alert('The age is accepted');
	}
	catch(e){
		alert('Error: ' + e.message);
	}

###Changes in the usage pattern
除了try...catch的用法，當然我們也可以使用error-checking的方法。

	function validatorAge(age){
		if(age === ''){
			return; //no age to valid
		}
		age = +age;
	
		if(isNaN(age)){
			return false;
		}
		else{
			return true;
		}
	}

	var value = prompt('Enter your age'),
	    error = validatorAge(value);
	
	if(!error){
		//process error
		alert('Invalid error!');
	}
	else{
		//success
		alert('The age is accepted');
	}

###Comparison
- try..catch較簡潔，可讀性較高。
- error-checking無法檢查所有的錯誤，但try..catch確可捕捉所有錯誤，所以try..catch是唯一萬無一失的方法。尤其在檢查瀏覽器相容的錯誤的時候。

##Exception analysis and rethrow
有時候，程式碼可能會產生不同種類的錯誤。因此，我們使用「if」來選擇適當的動作。結構類似如下：

	try {
	  // 1. do smth
	} 
	catch(e) {
		if (e instanceof ValidationError) {
	    	// 2.1 process e
		} 
		else if (e instanceof PermissionError) {
	    	// 2.2 process e
		} 
		else {
	    	// 3. we don't know how to deal with e
			throw e
	  	}
	}

在try區塊可能丟出了某些錯誤，有些錯誤是我們已知的，例如：ValidationError和PermissionError，然後去處理這些已知的e。但有些我們並不確定，因此直接丟出e。這裡丟出的e，需要由外層的try..catch來處理。

##Summary
- try..catch..finally將多種檢查合併在一個try區塊內執行，並將error-handlin分散在catch區塊來做錯誤的處理。
- try..catch..finally能捕捉和處理所有的錯誤。丟出的錯誤可使用JavaScript-generated或自行定義的。
- 錯誤建議繼承基本錯誤物件。因此，可用instanceof來檢查錯誤的類型，如上範例檢查ValidationError和PermissionError。

---
##Reference
- [Exceptions | JavaScript Tutorial](http://javascript.info/tutorial/exceptions)
- [Object Oriented Programming | JavaScript Tutorial](http://javascript.info/tutorial/object-oriented-programming)