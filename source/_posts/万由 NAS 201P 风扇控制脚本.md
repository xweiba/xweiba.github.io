---
title: 万由 NAS 201P 风扇控制脚本
date: 2022-12-23 14:46:50
tags:
 - NAS
 - 201P
 - Shell
categories:
 - NAS
---

```shell
#!/bin/bash


modprobe i2c-dev

tcpu=20
thdd=20

tcpu=$(sensors | tail -n +7 | head -n +1 | cut -b 17,18)
thdd=$(hddtemp /dev/sda | cut -b 36,37)

sum=$(( $tcpu + $thdd  - 10 ))      

echo "$sum is fan speed" > /root/fan.txt
echo "$tcpu is CPU temp " >> /root/fan.txt
echo "$thdd is HDD temp " >> /root/fan.txt
date >> /root/fan.txt

i2cset -y 0 0x54 0xf0 $sum

```

