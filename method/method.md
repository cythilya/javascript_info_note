#方法 (Method)
JavaScript是物件導向的語言，意即物件可以定義自己的方法(method)。  
例如：要排序一個陣列a，並不是把a傳給函式sort()，而是使用a的方法sort()。

	var a = [ 9, 20, 99, 80, 7, 0, 1000, 55, 3];
	a.sort = function (){
		var arr = this;
		for (i = 0; i <= arr.length-1; i++){
	    	if (arr[i] > arr[i+1]){
	        	temp = arr[i+1];
	        	arr[i+1] = arr[i];
	        	arr[i] = temp;
	        	i = i-2;
	      	}
	  	}
	};
	
	a.sort(); //execute sort() method
	console.log(a); //[0, 3, 7, 9, 20, 55, 80, 99, 1000]

技術上來說，只有物件才能有方法，但其實數字、字串和Boolean值的行為就像是它們也有方法一樣。注意，只有null和undefined不能使用方法。