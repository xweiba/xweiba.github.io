---
title: ffmpeg常用命令
date: 2022-12-23 14:35:29
tags:
 - ffmpeg
 - 常用命令
categories:
 - ffmpeg
---

合并视频和声音

`ffmpeg -hwaccel qsv -i test.mp4 -i test.mp3 -c:v h264_qsv -c:v copy -c:a aac -strict experimental -map 0:v:0 -map 1:a:0 hecheng.mp4`
使用qsv硬件解码
```cmd
ffmpeg -i test.mp4 -i test.mp3 -c:v copy -c:a aac -strict experimental -map 0:v:0 -map 1:a:0 hecheng.mp4
```


合并视频或声音
```cmd
ffmpeg -i "concat:123.mp3|124.mp3" -acodec copy output.mp3
```

使用qsv硬件解码
```cmd
ffmpeg -hwaccel qsv -i "concat:123.mp3|124.mp3" -c:v h264_qsv -acodec copy output.mp3
```