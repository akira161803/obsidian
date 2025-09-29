
Date: 2025-09-26
Tags: 
Links:  [[WSA]], [[OS Command Injection]]

***
## Detecting blind OS command injection using time delays

You can use an injected command to trigger a time delay, enabling you to confirm that the command was executed based on the time that the application takes to respond. The `ping` command is a good way to do this, because lets you specify the number of ICMP packets to send. This enables you to control the time taken for the command to run:

`& ping -c 10 127.0.0.1 &`

This command causes the application to ping its loopback network adapter for 10 seconds.

## Lab:

フィードバックのメール欄を書き換え。
`email=x||ping+-c+10+127.0.0.1||`

***
# References

https://portswigger.net/web-security/os-command-injection/lab-blind-time-delays