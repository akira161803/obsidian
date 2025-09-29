
Date: 2025-09-28
Tags: 
Links: [WSA](WSA.md)

***

## Step 3: Add a custom match and replace rule

To add a custom match and replace rule:
1. Go to **Proxy > Match and replace**.
2. Under **HTTP match and replace rules**, click **Add**. The **Add match/replace rule** dialog opens.
3. Under **Type**, make sure that **Request header** is selected.
4. Leave the **Match** field empty. This ensures that Burp appends a new header to requests rather than replacing an existing one.
5. In the **Replace** field, enter the following:
    
    `X-Custom-IP-Authorization: 127.0.0.1`
6. Click **Test**.
7. Under **Auto-modified request**, notice that Burp has added the `X-Custom-IP-Authorization` header to the modified request.
8. Click **OK**.
Burp Proxy will now add this header to every request you make in Burp's browser.




***
# References