
Date: 2025-09-22
Tags: 
Links: [[XSS]], [[WSA]]

***

1. 脆弱なエスケープ
```javascript
function escapeHTML(html) {
    return html.replace('<', '&lt;').replace('>', '&gt;');
}
```
これだと最初にヒットしたものしか置換しない。

```javascript
return html.replace(/</g, '&lt;').replace(/>/g, '&gt;');

return html.replaceAll('<', '&lt;').replaceAll('>', '&gt;');
```
とかにしないといけない。

2. `<><img src=1 onerror=alert()>`を投稿。

***
# References