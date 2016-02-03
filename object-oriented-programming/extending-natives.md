# Extending Natives
Native JavaScript物件將method存在prototype中。例如：當一個新的物件被建立，內容為空，但為何可以使用toString這個method？(參考下面的程式碼)

    var obj = { };
    console.log( obj.toString() );

這是因為 `var obj = {};` ( 即 `var obj = new Object();`)。而Object是Native JavaScript的建構式，obj由Object所產生，因此 `obj.__proto__ == Object.prototype;`。所以，所有在Object.prototype中的屬性，obj皆可使用(包含`Object.prototype.toString`)。這就是為什麼obj可以使用 toString 這個method了。對於Array、Function和其他物件也是相同的道理，而它們的method即在Array.prototype、Function.prototype等。

當物件的屬性被存取時，interpreter會依照下列的順序找尋屬性的值：

- 物件本身
- 物件的 `__proto__`
- 物件的 `__proto__` 的 `__proto__`

interpreter會尋找這個值直到找到或下一個 `__proto__` 的值為null。Object.prototype是唯一一個物件的 `__proto__` 值為null，因此一定會終止在Object.prototype。換句話說，所有的物件皆繼承自Object。

當基礎型別使用method會隱性的轉為物件，而其結果也會是基礎型別。例如：宣告一個變數i等於數字5，並使用toString這個method (參考下面的程式碼)。

    var i = 5;
    console.log(i.toString()); //5

## 修改原生的 Prototypes
Native Prototypes可以被修改，也可以新增新的method到prototype中。例如，我們可以新增一個新的method「each」，將物件的屬性列印出來。

    Object.prototype.each = function(f){
        for(var prop in this) {
            if(Object.prototype.hasOwnProperty(prop)) continue; //跳過後面的指令，進行下一次迴圈的執行。在此用於過濾protoytpe的屬性
            var value = this[prop];
            f.call(value, prop, value);
        }
    };
    
    var obj = { name: 'John', age: 25};
    obj.each(function(prop, val){
        console.log(prop); //name -> age
    });

修改內建的prototype是不好的，但如果是要將ECMA-262 5th的method實作進舊瀏覽器是可以的。例如，假設舊瀏覽器沒有 `Object.create`這個method，那我們就新增它。

    if(!Object.create){
        Object.create = function(proto){
            function F(){};
            F.prototype = proto;
            return new F;
        }
    }

## 繼承原生物件
原生物件(Native Objects)是可以被繼承的。例如：`Array.prototype`保留了所有的method給新的Array的實例(instance)。假設我們希望能在myArray當中使用Array.prototype的method，那麼就將Array.prototype的值設定給`myArray.__proto__`即可。 

    function MyArray(){
        //將陣列中的每個元素用逗點合併，使其成為一字串
        this.stringify = function(){
            return this.join(', ');
        };
    };
    
    MyArray.prototype = Array.prototype;
    
    var myArr = new MyArray();
    myArr.push('hello');
    myArr.push('jack');
    console.log(myArr.stringify()); //"hello, jack"
    console.log(myArr); //["hello", "jack"]
    console.log(myArr.length); //2

## 借用 Method
如果只是想要使用Array的部份功能，其實不需要繼承它 - 借用它的method即可。

    var join = Array.prototype.join,
        join = [].join;
    	
    var obj = {
      0: 'first',
      1: 'second',
      length: 2
    };
    
    console.log([].join.call(obj, ', ')); //first, second

Array的method常被借用來處理類似Array的物件。

## Summary
- Native JavaScript物件將method存在prototype中。
- Native prototypes可以被繼承或擴充。
- 修改頂端的Object.prototype會破壞for..in loops，因此不建議這麼做。但可實作一些先進的method而讓舊瀏覽器支援這些好用的方法，例如：Object.create、Object.keys、 Function.prototype.bind等。

---
#### References
- [Extending Natives](http://javascript.info/tutorial/native-prototypes)
