---
title: '登記Windows服務'
date: 2019-04-15
weight: 1
---



#### 使用工具

[winsw](https://github.com/kohsuke/winsw)



#### 流程

1. 到[winsw/releases](https://github.com/kohsuke/winsw/releases)下載適用的版本(由環境的.NET版本決定)
2. 從[winsw/examples](https://github.com/kohsuke/winsw/tree/master/examples)下載xml設定檔
3. 準備好服務啟動時要執行的目標(EX:.JAR檔)
4. 將1., 2., 3.放在同資料夾並給相同檔名
   ![1555314930686](https://raw.githubusercontent.com/kiwislice/html-css-js-memo/master/content/tools/1555314930686.png)
5. 開啟命令提示字元(看狀況可用系統管理員身分執行)，進入該資料夾，執行`xxx.exe install`即可(xxx自行替換成該exe檔名)



#### DEBUG

如果啟動過程出錯，可至事件檢視器查看

