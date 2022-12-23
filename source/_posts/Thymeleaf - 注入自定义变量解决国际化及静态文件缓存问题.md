---
title: Thymeleaf - 注入自定义变量解决国际化及静态文件缓存问题(添加版本号)
date: 2019-08-08 12:55:49
tags:
 - Thymeleaf
 - 国际化
 - web缓存
categories:
 - Thymeleaf
---

# 后端

spring boot 添加配置: 
```
# 国际化
i18n:
  locale: zh_TW
# 静态资源版本
build:
  version: 010-0.0.2 #项目版本-静态资源版本
```
启动类中使用增强器为每个`Controller`注入变量
```java
@Value("${i18n.locale}")
protected String locale;

@ControllerAdvice
public static class GlobalVariableController {
    @Value("${build.version:0.0.1}")
    private String buildVersion;

    public GlobalVariableController() {
    }

    @ModelAttribute
    public void getLocale(Model model) {
        Locale _locale = ((LocaleResolver)SpringBeanUtils.getBean(LocaleResolver.class)).resolveLocale((HttpServletRequest)null);
        model.addAttribute("_locale", _locale.toString());
        model.addAttribute("_v", this.buildVersion);
    }
}
```

# 前端
thymeleaf: 
```html
<script th:src="@{/js/jQuery/jquery-3.0.0.min.js(v=${_v})}"></script>
<!-- 加载国际化js静态文件 -->
<script type="text/javascript" th:src="@{|/js/publicFile_${_locale}.js|(v=${_v})}"></script>
```