
Date: 2025-09-23
Tags: 
Links: [[XSS]], [[WSA]]

***

 検索フォームがそのままhtmlに埋め込まれる。
 しかし標準のタグは受け入れられない。
 **カスタムタグ**を使用する。

`onmouseover=alert()`などでも発火するが、自動化したい。そこで以下のようにする。

```javascript
<script> 
	location = 'https://YOUR-LAB-ID.web-security-academy.net/?search=<xss id=x onfocus=alert(document.cookie) tabindex=1>#x'; 
</script>
```

 URL の末尾に`#x`があるので、ブラウザはそのページ上の id="x" 要素にジャンプ（scrollIntoView）し、フォーカスを与える／到達させる（アクセシビリティやブラウザ実装によりフォーカスされる場合がある）。
 　　　
ただし元素自体がフォーカス可能でないとフォーカスされないため、tabindex="1" を付けて**強制的にフォーカス可能**にしているのが肝。


要素にフォーカスが当たると onfocus 属性で指定したコードが実行される → alert(document.cookie) が実行される。

***
# References

https://portswigger.net/web-security/cross-site-scripting/contexts/lab-html-context-with-all-standard-tags-blocked

**tabindexについて**
https://qiita.com/happa-creator/items/06de61aac7d3e0f1b03a