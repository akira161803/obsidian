
Date: 2025-09-23
Tags: 
Links: [[XSS]], [[WSA]]

***

検索フォームでタグや属性がブロックされる。

https://portswigger.net/web-security/cross-site-scripting/cheat-sheet

1. intruderでタグ名を総当たり
2. bodyタグでバイパスできることが判明。
3. 次に属性を総当たり。
4. 色々ヒットするが、発火させやすそうな`onresize`を選択。
5. ペイロードを作成。
```javascript
<iframe src='https://0a6c004e033a5fb5805417bf00ee00a1.web-security-academy.net/?search=<body onresize=print()>' onload=this.style.width='100px'>
```
- this.style.widthではiframeの枠の大きさが変わるだけで内部のbodyのサイズは変わらないが、なぜかこれで`onresize `が発火する。
- `='100px'`のクオーテーションは必須。


***
# References

https://portswigger.net/web-security/cross-site-scripting/contexts/lab-html-context-with-most-tags-and-attributes-blocked