---
title: '跨站請求偽造(CSRF)'
date: 2019-04-18
weight: 10
tags: ["web","csrf","web security"]
---



**跨站請求偽造**（英語：Cross-site request forgery），也被稱為 **one-click attack** 或者 **session riding**，通常縮寫為 **CSRF** 或者 **XSRF**， 是一種挾制用戶在目前已登入的Web應用程式上執行非本意的操作的攻擊方法。跟[跨網站指令碼](https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%B6%B2%E7%AB%99%E6%8C%87%E4%BB%A4%E7%A2%BC)（XSS）相比，**XSS** 利用的是用戶對指定網站的信任，CSRF 利用的是網站對用戶網頁瀏覽器的信任。



### 手法

假如一家銀行用以執行轉帳操作的URL位址如下： 

> http://www.examplebank.com/withdraw?account=AccoutName&amount=1000&for=PayeeName

那麼，一個惡意攻擊者可以在另一個網站上放置如下程式碼：

> <img src="<http://www.examplebank.com/withdraw?account=Alice&amount=1000&for=Badman>">

如果有帳戶名為Alice的用戶存取了惡意站點，而她之前剛存取過銀行不久，登入資訊尚未過期，那麼她就會損失1000資金。

透過例子能夠看出，攻擊者並不能通過CSRF攻擊來直接獲取用戶的帳戶控制權，也不能直接竊取用戶的任何資訊。他們能做到的，是**欺騙用戶瀏覽器，讓其以用戶的名義執行操作**。



[CSRF wiki](https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%AB%99%E8%AF%B7%E6%B1%82%E4%BC%AA%E9%80%A0)

[CSRF cookie與httpOnly](https://security.stackexchange.com/questions/175536/does-a-csrf-cookie-need-to-be-httponly)