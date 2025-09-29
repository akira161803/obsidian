
Date: 2025-09-27
Tags: 
Links:  [[WSA]], [[Business Logic Vulnerabilities]]

***

When probing for logic flaws, you should try removing each parameter in turn and observing what effect this has on the response. You should make sure to:

- Only remove one parameter at a time to ensure all relevant code paths are reached.
- Try deleting the name of the parameter as well as the value. The server will typically handle both cases differently.
- Follow multi-stage processes through to completion. Sometimes tampering with a parameter in one step will have an effect on another step further along in the workflow.

This applies to both URL and `POST` parameters, but don't forget to check the cookies too. This simple process can reveal some bizarre application behavior that may be exploitable.

**色々値とかパラメータ消して挙動を調べる。**
## Lab : 
1. wienerのアカウントのパスワード変更のリクエストで、`username=administratorにして, `current-password=aaa`を全部消すと、administratorのパスワードを変更できる。


***
# References