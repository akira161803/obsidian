
Date: 2025-09-26
Tags: 
Links: [[WSA]], [[OS Command Injection]]

***

## Injecting OS commands

In this example, a shopping application lets the user view whether an item is in stock in a particular store. This information is accessed via a URL:

`https://insecure-website.com/stockStatus?productID=381&storeID=29`

To provide the stock information, the application must query various legacy systems. For historical reasons, the functionality is implemented by calling out to a shell command with the product and store IDs as arguments:

`stockreport.pl 381 29`

This command outputs the stock status for the specified item, which is returned to the user.

The application implements no defenses against OS command injection, so an attacker can submit the following input to execute an arbitrary command:

`& echo aiwefwlguh &`

If this input is submitted in the `productID` parameter, the command executed by the application is:

`stockreport.pl & echo aiwefwlguh & 29`

The `echo` command causes the supplied string to be echoed in the output. This is a useful way to test for some types of OS command injection. The `&` character is a shell command separator. In this example, it causes three separate commands to execute, one after another. The output returned to the user is:

`Error - productID was not provided aiwefwlguh 29: command not found`

The three lines of output demonstrate that:

- The original `stockreport.pl` command was executed without its expected arguments, and so returned an error message.
- The injected `echo` command was executed, and the supplied string was echoed in the output.
- The original argument `29` was executed as a command, which caused an error.

Placing the additional command separator `&` after the injected command is useful because it separates the injected command from whatever follows the injection point. This reduces the chance that what follows will prevent the injected command from executing.

***

## Useful commands

After you identify an OS command injection vulnerability, it's useful to execute some initial commands to obtain information about the system. Below is a summary of some commands that are useful on Linux and Windows platforms:

| Purpose of command    | Linux         | Windows         |
| --------------------- | ------------- | --------------- |
| Name of current user  | `whoami`      | `whoami`        |
| Operating system      | `uname -a`    | `ver`           |
| Network configuration | `ifconfig`    | `ipconfig /all` |
| Network connections   | `netstat -an` | `netstat -an`   |
| Running processes     | `ps -ef`      | `tasklist`      |

***

-  |（単一パイプ）
    - 左のコマンドの標準出力を右のコマンドの標準入力につなぐ。例：echo hi | wc -c。
- ||（論理 OR、「または」）
    - **左側のコマンドが失敗（終了ステータス非ゼロ）した場合にのみ**右側のコマンドを実行する。
    - 連続して書くと（a || b || c）左から順に、あるコマンドが失敗したら次に進む仕組み。
- &&（論理 AND、「かつ」）
    - **左側が成功（終了ステータスゼロ）した場合にのみ**右側を実行する。
- &（アンパサンド、バックグラウンド実行）
    - そのコマンドを**バックグラウンドで実行**してシェルを直ちに返す（例：sleep 10 & はすぐにプロンプトが返ってくる）。
    - ただしサーバー側プログラムが子プロセスの終了を待つ／接続を切る処理をしない等の挙動によっては応答タイミングに影響することもある。
    - `command1 & command2`複数のコマンドを同時に実行できる。
- - ;（セミコロン）：順番に実行する
  → command1 が終わった後に command2 を実行する。バックグラウンド化はしない。


***
# References