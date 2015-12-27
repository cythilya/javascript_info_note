#Plugin的撰寫
以下使用[jQuery Newsticker Plugin](https://github.com/cythilya/jquery-newsticker)作為說明範例。

##Step 1：使用閉包
避免汙染全域變數，關於閉包可參考這篇[JavaScript Scoping and Hoisting](http://www.adequatelygood.com/JavaScript-Scoping-and-Hoisting.html)，清楚說明Scope、Hoisting、Function的相關觀念。

	(function($){
		...
	})(jQuery);

##Step 2：決定使用者可以傳入的參數和使用的method
以[jQuery Newsticker Plugin](https://github.com/cythilya/jquery-newsticker)為例，我們允許使用者根據需求調整(意即覆寫預設值)height、speed、start、interval和move。其中move是開放讓使用者客製化的function，如果使用者不滿意目前newsticker內容由下往上移動方式，則可自行撰寫，其預設值是null。

    var config = $.extend({}, {
        height: 30,
        speed: 800,
        start: 8,
        interval: 3000,
        move: null
    }, opts);

##Step 3：撰寫內部程式
接下來便是撰寫plugin的功能。
###功能分割
這個newsticker預定會有幾種功能：

- 初始化：初始設定，例如：設定目前要展示的項目、其他function的初始化。
- 移動：newsticker內容由下往上移動。
- 暫停：當滑鼠移到內容上方時，newsticker會停止移動，讓使用者順利閱讀；滑鼠移開再繼續移動。

因此，程式區塊就看到了三個主要的function，後續便針對這三個function補滿應有的內容。

	function init(){
		//初始化
		...
	}:
		
	function suspend(){
		//暫停	
		...
	};
	
	function move(){
		//移動
		....
	};

###執行使用者定義的method
由於我們開放`config.move`讓使用者客製newsticker的移動方式，因此必須先判斷使用者是否輸入自己寫好的function，如果沒有，則執行預設的移動方式。這裡使用一個小技巧 `$.isFunction` 來幫忙做判斷。

    if($.isFunction(config.move)){
        config.move.call(this);
    }
    else{
        //執行預設的移動方式
    }

##Step 4：初始化這個Plugin

    // initialize every element
    this.each(function() {
        init($(this));
    });
    return this;

##Step 5：使用這個Plugin
在這裡也可以代入要覆寫的參數，例如：speed。

    $(function() {
        $('.newsticker').newsticker({
			speed: 1000
		});
    });

##除了撰寫Plugin，還有其他擴充方法嗎？
###擴充為jQuery的核心
如果是常用的功能，我們也可以使用`jQuery.fn.extend()` 或 `$.fn.extend()` 擴充為jQuery的核心，當成一般jQuery method使用。假設我們擴充prompt這個method來做提示功用。

$.fn.extend({
    prompt: function() {
        return this.each(function() {
            console.log('this is a prompt.');
        });
    }
});

這樣就可以使用了...前後都可以加上其他method使之成為chain。

	obj.show().prompt().hide();

###擴充原有的method，使其有新的功能
擴充$.trim，使其具有把字串中所有空白都移除的功能。我們將原本的$.trim存到變數_trim中，假設在使用method時傳入「clear為true」則使用擴充的用法 - 將字串中所有空白的移除。

	(function($) {
	    var _trim = $.trim;
	    $.extend({
	        trim: function(text, clear) {
	            if (clear) {
	                return text.replace(/\s+/g, '');
	            }
	            return _trim.call(this, text);
	        }
	    });
	})(jQuery);

傳入不同參數有不同的結果...

####直接prompt原始字串
	console.log(' aa aa a '); // aa aa a 

####原本的功能，只去除前後空白
	console.log($.trim(' aa aa a ')); //aa aa a

####擴充的功能，將字串中所有空白的移除
	console.log($.trim(' bb bb b ', true)); //bbbbb

---
##Reference
- [jQuery: Novice to Ninja](http://www.amazon.com/jQuery-Novice-Ninja-Earle-Castledine/dp/0987153013)
- [JavaScript Scoping and Hoisting](http://www.adequatelygood.com/JavaScript-Scoping-and-Hoisting.html)
- [jQuery Newsticker Plugin](https://github.com/cythilya/jquery-newsticker)
- [網誌版](http://cythilya.blogspot.tw/2015/12/plugin.html)