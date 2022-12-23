---
title: 豆瓣API接口
date: 2022-12-23 14:32:27
tags:
 - API
 - 豆瓣
categories:
 - 公共API
---

# 豆瓣API接口

## 搜索接口

- [GET] `https://m.douban.com/j/search/?q=%E5%96%9C%E6%AC%A2%E4%BD%A0&t=1002&p=1`

| 参数 | 说明   |
| ---- | ------ |
| t    | 类型id |
| p    | 分页   |

| t id | 说明 |
| ---- | ---- |
| 1001 | 书籍 |
| 1002 | 电影 |
| 1003 | 音乐 |
| 1019 | 小组 |

## 原图下载

`wget --referer="https://img2.doubanio.com" https://img2.doubanio.com/view/photo/raw/public/p2455926001.jpg` 
或
`wget --referer="https://m.douban.com" https://img2.doubanio.com/view/photo/raw/public/p2455926001.jpg`