---
title: SpringBoot 代码中使用国际化配置文件
date: 2018-06-21 12:30:40
tags:
 - SpringBoot
 - 国际化
categories:
 - SpringBoot
---

# 国际化文件
```
resources
    │  application.yml
    │  logback.xml
    │  mybatis-config.xml
    │
    └─i18n
            lang.properties
            lang_en_US.properties
            lang_zh_CN.properties
            lang_zh_TW.properties
```
# yml 配置文件
```yml
i18n: 
    locale: zh_CN
spring:
     messages:
          basename: i18n/lang
          encoding: UTF-8
```

# 启动类注入国际化配置
```java
@Value("${i18n.locale}")
private String locale;


@Bean
public LocaleResolver localeResolver() {
	Locale locale = I18nUtils.localeFromString(this.locale, (Locale)null);
	return new FixedLocaleResolver(locale);
}
```

# 使用工具类获取国际化配置

这里应该使用 `Aware` 接口.

```java
public class I18nToolUtils {

    /**
     * 国际化信息服务
     */
    private final static MessageSource messageSource = SpringBeanUtils.getBean(MessageSource.class);


    public static String getI18String(String code) {
        if (StringUtils.isBlank(code)) return null;
        return messageSource.getMessage(code, null, LocaleContextHolder.getLocale());
    }
}
```