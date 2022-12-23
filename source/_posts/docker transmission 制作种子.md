---
title: docker transmission 制作种子
date: 2022-12-23 14:45:19
tags:
 - transmission
 - 制作种子
categories:
 - docker
---

# docker transmission 制作种子
```
docker exec -it tran bash
cd /user/bin
transmission-create -p -o /mnt/nas/data/资料/种子/Cold.Detective.2022.WEB-DL.2160p.X264.torrent -t https://www.pttime.org/announce.php  -s 2048 /mnt/nas/data/资料/种子/Cold.Detective.2022.WEB-DL.2160p.X264/
```