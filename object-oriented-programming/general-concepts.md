# General Concepts
OOP的特色為...

## 繼承(Inheritance)
繼承「Menu」的功能來建立一個子物件「TreeMenu」是可能的。子物件能存取父物件的方法和屬性，並且也會擁有屬於自己的方法和屬性。因此，允許重覆使用通用的Menu的程式碼於TreeMenu、SlideMenu和其他menu類型上。

## 封裝 (Encapsulation)
三種封裝的層級：private、protected和public。

### private
只能被自己的class存取，繼承的物件不能存取。

### protected
class自己本身和繼承的物件都可以存取。

### public
任何人都可以存取。

## 多型 (Type polymorphism)
不用任何修改即可將Menu物件的程式碼可以轉換為TreeMenu物件或SlideMenu物件。

---
####References
- [General concepts](http://javascript.info/tutorial/oop-concepts)

