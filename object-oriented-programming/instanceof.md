# The "instanceof" operator
## The instanceof operator
`instanceof` 允許檢查物件是否為給定的constructor所產生的。

    function Rabbit(){};
    var rabbit = new Rabbit();
    console.log(rabbit instanceof Rabbit); //ture

rabbit的constructor的確是Rabbit。

## When instanceof lies
當值是來自另外一個frame或iframe，`instanceof` 這個operator所取得的直是不準確的。例如，一個來自另外一個iframe的array。每一個frame或iframe有自己的window物件和其繼承關係。解法可參考[[[Class]] to differ between native objects](http://javascript.info/tutorial/type-detection#class-to-differ-between-native-objects)。

## Summary
- `instanceof` 允許檢查物件是否為給定的constructor所產生的。檢查方式是走訪整個 `__proto__` 鍊，因此繼承是成立的。

        var arr = []
        alert(arr instanceof Array) // true
        alert(arr instanceof Object) // true

- 當值是來自另外一個frame或iframe，`instanceof` 這個operator所取得的直是不準確的。例如，一個來自另外一個iframe的array。每一個frame或iframe有自己的window物件和其繼承關係。解法可參考[[[Class]] to differ between native objects](http://javascript.info/tutorial/type-detection#class-to-differ-between-native-objects)。

---
#### Reference
- [The "instanceof" operator](http://javascript.info/tutorial/instanceof)
