---
title: 'Http Cookie'
date: 2019-04-18
weight: 20
tags: ["web","http cookie","web security"]
---



### Secure和HttpOnly

httpOnly flag: 有設定時，Cookie只限被伺服端存取，無法在用戶端讀取。

secure flag:  有設定時，Cookie只能透過https的方式傳輸。

範例：

```http
HTTP/2.0 200 OK
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```



<https://www.ajoshow.com/2017/07/19/201707192223/>

https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#Secure_and_HttpOnly_cookies











