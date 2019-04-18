---
title: 'Http安全性Header'
date: 2019-04-18
weight: 20
tags: ["web","http header","web security"]
---



### X-Frame-Options

HTTP回應標頭 (header) 用來指示文件是否能夠載入`<frame>`, `<iframe>`以及`<object>`，網站可以利用 X-Frame-Options 來確保本身內容不會遭惡意嵌入道其他網站、避免 clickjacking 攻擊

範例：

```http
HTTP/2.0 200 OK
X-Frame-Options: DENY
```



<https://developer.mozilla.org/zh-TW/docs/Web/HTTP/X-Frame-Options>



### X-XSS-Protection

可在檢測到反映的跨站點腳本（[XSS](https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%B6%B2%E7%AB%99%E6%8C%87%E4%BB%A4%E7%A2%BC)）攻擊時阻止頁面加載

範例：

```http
HTTP/2.0 200 OK
X-XSS-Protection: 1; mode=block
```



<https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Headers/X-XSS-Protection>



### X-Content-Type

HTTP回應標頭 (header) 用來指示該標記MIME類型的Content-Type標題不應該被改變，並且被遵循

範例：

```http
HTTP/2.0 200 OK
X-Content-Type-Options: nosniff
```



<https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Headers/X-Content-Type-Options>