
Date: 2025-09-22
Tags: 
Links: [[XSS]], [[WSA]]

***
AngularJSの脆弱性を利用する。
一般性がなさそうなので後回し。

bodyタグに`ng-app`という属性がある。→AngularJS
フォームに`{{$on.constructor('alert(1)')()}}`を入力する。

解説動画の人の主張が印象に残った。
ハッカーは様々なフレームワークに精通している必要がある。
***
# References

https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-angularjs-expression