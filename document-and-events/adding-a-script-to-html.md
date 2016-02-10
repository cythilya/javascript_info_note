# Adding a script to HTML
script可以被放在頁面的任何地方，最常見的地方有：

- `<head>` 標籤內
- `<body>` 結束之前

但通常script可以被放在任何地方。

當瀏覽器執行HTML頁面並看到 `<script>` 標籤時，就會轉成JavaScript模式並執行程式碼。執行完畢後，瀏覽器才繼續執行頁面其餘的部份。
 
## Page rendering and SCRIPT
[打開這個頁面](http://javascript.info/files/u261/rabbits.html)來觀察browser如何在JavaScript模式間切換。

	<html>
	<body>
	  <h1>Counting rabbits</h1>
	 
	  <script>
	    for(var i=1; i<=3; i++) {
	      alert('Rabbit ' + i + ' out of the hat!');
	    }
	  </script>
	 
	  <h1>...Finished counting</h1>
	</body>
	</html>

1. 當頁面開始執行，只顯示文件的開端。
2. browser遇到 `<script>` 標籤，轉成JavaScript模式並執行程式碼，並alert 3次。
3. 執行完畢穿插在HTML間的Script後，瀏覽器才繼續執行頁面其餘的部份，顯示最後的HTML。

## 將script放在 `<head>` 標籤內
如果HTML檔案非常大，並且希望script在頁面顯示前能早點執行，那麼就放在`<head>` 標籤內，但對於講究效能的網站往往不這麼做 - 將script放在`<body>` 結束之前。

	<html>
	<head>
	  <script>
	    for(var i=1; i<=3; i++) {
	      alert('Rabbit ' + i + ' out of the hat!');
	    }
	  </script>
	</head>
	<body>
	  <h1>Counting rabbits</h1>
	  <h1>...Finished counting</h1>
	</body>
	</html>

## 將script放在`<body>` 結束之前
頁面載完後才開始執行script，這會讓使用者在畫面載完、且執行完script之後才能與頁面互動。
附註：CSS檔案放在`<head>` 標籤內，只有script可放在任何地方。

## 外部檔案
通常JavaScript程式碼會放在外部檔案，再連結到HTML檔案。連結可用相對或是絕對路徑。

<script src="/path/to/script.js"></script>

將 `<script>` 標籤放在 `<head>` 標籤內，依然會有效能問題。

---
###References
- [Adding a script to HTML | JavaScript Tutorial](http://javascript.info/tutorial/adding-script-html)