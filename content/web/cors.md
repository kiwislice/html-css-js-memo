---
title: '跨來源資源共用(CORS)'
date: 2019-04-18
weight: 10
tags: ["web","cors"]
---



跨來源資源共用（Cross-Origin Resource Sharing）是一種使用額外 [HTTP](https://developer.mozilla.org/en-US/docs/Glossary/HTTP) 標頭令目前瀏覽網站的[使用者代理](https://developer.mozilla.org/en-US/docs/Glossary/user_agent)取得存取其他來源（網域）伺服器特定資源權限的機制。當使用者代理請求一個不是目前文件來源——例如來自於不同網域（domain）、通訊協定（protocol）或通訊埠（port）的資源時，會建立一個**跨來源 HTTP 請求（cross-origin HTTP request）**。

舉個跨來源請求的例子：`http://domain-a.com`HTML 頁面裡面一個`<img>`標籤的`src`屬性載入來自 `http://domain-b.com/image.jpg` 的圖片。現今網路上許多頁面所載入的資源，如 CSS 樣式表、圖片影像、以及指令碼（script）都來自與所在位置分離的網域，如內容傳遞網路（content delivery networks, CDN）。

基於安全性考量，程式碼所發出的跨來源 HTTP 請求會受到限制。例如，[`XMLHttpRequest`](https://developer.mozilla.org/zh-TW/docs/Web/API/XMLHttpRequest) 及 [`Fetch`](https://developer.mozilla.org/zh-TW/docs/Web/API/Fetch_API) 都遵守[同源政策（same-origin policy）](https://developer.mozilla.org/zh-TW/docs/Web/Security/Same-origin_policy)。這代表網路應用程式所使用的 API 除非使用 CORS 標頭，否則只能請求與應用程式相同網域的 HTTP 資源。

<https://developer.mozilla.org/zh-TW/docs/Web/HTTP/CORS>

