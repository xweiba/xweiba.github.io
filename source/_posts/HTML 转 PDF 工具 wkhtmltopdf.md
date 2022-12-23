---
title: HTML 转 PDF 工具 wkhtmltopdf
date: 2019-04-23 13:44:31
tags:
 - wkhtmltopdf
 - WEB
 - PDF
categories:
 - wkhtmltopdf
---

# 使用方法
## HTML转换pdf
`wkhtmltopdf --window-status completed  --debug-javascript "http://localhost:9137/manager/assessment/report/v2/class?testId=791078347730882560&classId=hpjrblnxkdv2qx9u941qfm3vwplpmhpr&printPdf=true" -`

- `window-status completed`: 读取网页参数, 当页面 `window.status =  "completed"` 时, 才算页面渲染完成, 解决异步`js`页面无法正常渲染的问题.
- `debug-javascript`: 开启`js debug`

# 特定页面样式
- chart 图表必须设置高宽, 不然不显示.
- 不希望被切分的`div` 如图表, 添加样式: `page-break-inside:avoid;`
- 将表格分页切分页后加头
    ```css
    table tr {
        word-break: break-all;
        page-break-before: always;
        page-break-after: always;
        page-break-inside: avoid;
    }
    ```

# 错误解决方法
## 已知问题
- `wkhtmltopdf` 不支持`ES6`语法
## :47 SyntaxError: Parse error
定位到页面第 `47` 行位置, 可能有`wkhtmltopdf`无法解析的属性, 如 `let`. 

## wkhtmltopdf 转换字体导致样式异常

保证环境语言字体正常

安装字体, 设置语言:
```
yum install wqy-zenhei-fonts
localectl set-locale  LANG=zh_CN.utf8
```