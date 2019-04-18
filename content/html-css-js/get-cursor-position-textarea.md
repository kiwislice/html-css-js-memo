---
title: '取得Textarea游標位置'
date: 2019-04-17
weight: 1
tags: ["javascript","textarea"]
---





透過JS可取得textarea的`selectionStart`和`selectionEnd`屬性

```javascript
function(textareaId) {
    var ta = document.getElementById(textareaClientId);
    return ta.selectionStart;
}
```

#### 參考
- https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XUL/Property/selectionStart


