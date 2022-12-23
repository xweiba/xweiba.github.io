---
title: 使用 jsonp 读取本地文件并解决跨域问题
date: 2022-12-23 13:41:50
tags:
 - jsonp
 - 跨域
categories:
 - Web
---

```javascript
<script type="text/javascript"> 
    var knowledgeData = ''; 
   //JS代码就一个方法 function train(result) { knowledgeData = result; } 
</script> 
<script src="local.json?callback=train"></script>
```

