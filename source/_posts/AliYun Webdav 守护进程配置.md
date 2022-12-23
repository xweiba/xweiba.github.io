---
title: AliYun Webdav 守护进程配置
date: 2022-12-23 14:44:10
tags:
 - Webdav
 - Aliyun
categories:
 - Webdav
---

1. wget 下载 webdav-aliyundriver jar 包，获取 yourefreshtoken 文档也在说明页，其他参数可以自己加载脚本后面
2. 安装 jre 环境 apt install default-jre
3. 创建启动和停止脚本，文件目录用自己的

vi stop.sh
```bash
#!/bin/bash

echo "webdav-aliyundriver will be stoped....."
pid=$(ps aux|grep -v grep|grep webdav-aliyundriver*.jar|awk '{print $2}');
if [[ $pid -gt 1 ]]; then
    kill -9 $pid
fi
```

vi start.sh
```bash
#!/bin/bash

/mnt/nas/data/dev_tools/webdav/aliyun/stop.sh

echo "webdav-aliyundriver will be start....."
java -jar /mnt/nas/data/dev_tools/webdav/aliyun/webdav-aliyundriver-2.4.2.jar --aliyundrive.refresh-token="yourefreshtoken" > /dev/null &
```

加权限 chmod +x start.sh stop.sh

4. 创建守护线程服务
vi /etc/systemd/system/webdav-ali.service
```
[Unit]
Description=Webdav Aliyun
# 在什么服务启动之后再执行本程序
After=network.target
[Service]
Type=forking
# 程序执行的目录
WorkingDirectory=/mnt/nas/data/dev_tools/webdav/aliyun/
# 启动的脚本命令
ExecStart=/mnt/nas/data/dev_tools/webdav/aliyun/start.sh
ExecReload=/mnt/nas/data/dev_tools/webdav/aliyun/start.sh
ExecStop=/mnt/nas/data/dev_tools/webdav/aliyun/stop.sh
# 重启条件
Restart=always
# 几秒后重启
RestartSec=5
[Install]
WantedBy=multi-user.target
```

开启服务
1. 重载 systemctl daemon-reload
2. 开机自启 systemctl enable webdav-ali
3. 启动 systemctl start webdav-ali
4. 查看状态 systemctl start webdav-ali