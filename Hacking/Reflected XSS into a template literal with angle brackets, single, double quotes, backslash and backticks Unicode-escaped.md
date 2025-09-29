
Date: 2025-09-24
Tags: 
Links: [[XSS]], [[WSA]]

***

テンプレートリテラルは**backtick**によって囲まれる。
埋め込み式は${...}で表される。
テンプレートリテラルの内部であれば引用符を閉じことなく、埋め込み式を実行できる。

## Lab


```javascript
<script>
    var message = `0 search results for '(\u0027)(\u0022)'`; //(')(")
    document.getElementById('searchMessage').innerText = message;
</script>
```

```javascript
<script>
    var message = `0 search results for '${alert()}'`;
    document.getElementById('searchMessage').innerText = message;
</script>
```
**引用符の中からでも実行される。**

***
# References