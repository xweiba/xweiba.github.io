---
title: PVE LXC 常用配置
date: 2022-12-23 14:39:40
tags:
 - PVE 
 - LXC
categories:
 - PVE 
---

# LXC配置

1.Ubuntu开启硬件直通

```
arch: amd64
cores: 4
features: nesting=1
hostname: Ububtu-20.04
memory: 7680
mp1: /mnt/nas,mp=/mnt/nas
mp2: /mnt/aliyun,mp=/mnt/aliyun
onboot: 0
ostype: ubuntu
rootfs: local:101/vm-101-disk-0.raw,size=10G
swap: 0
lxc.net.0.type: phys
lxc.net.0.link: enp3s0
lxc.net.0.ipv4.address: 192.168.186.230/24
lxc.net.0.ipv4.gateway: 192.168.186.1
lxc.net.0.flags: up
lxc.apparmor.profile: unconfined
lxc.cgroup.devices.allow: a
lxc.cap.drop: 
lxc.cgroup2.devices.allow: c 226:0 rwm
lxc.cgroup2.devices.allow: c 226:128 rwm
lxc.mount.entry: /dev/dri/card0 dev/dri/card0 none bind,optional,create=file
lxc.mount.entry: /dev/dri/renderD128 dev/dri/renderD128 none bind,optional,create=file
lxc.mount.entry: /dev/fuse dev/fuse none bind,create=file
```

2.OpenWrt Clash

```
arch: amd64
cores: 2
hookscript: local:snippets/hookscript.pl
hostname: Lede-OpenWRT
memory: 512
net0: name=eth0,bridge=vmbr0,hwaddr=D6:DC:B4:4F:77:EF,type=veth
onboot: 1
ostype: unmanaged
rootfs: local:100/vm-100-disk-0.raw,size=204M
swap: 0
```

3.openwrt.common.conf

```
# Default console settings
lxc.tty.dir = lxc
lxc.tty.max = 4
lxc.pty.max = 1024

# Default capabilities
lxc.cap.drop = mac_admin
lxc.cap.drop = mac_override
lxc.cap.drop = sys_admin
lxc.cap.drop = sys_module
lxc.cap.drop = sys_nice
lxc.cap.drop = sys_pacct
#lxc.cap.drop = sys_ptrace
lxc.cap.drop = sys_rawio
#lxc.cap.drop = sys_resource
lxc.cap.drop = sys_time
lxc.cap.drop = sys_tty_config
lxc.cap.drop = syslog
lxc.cap.drop = wake_alarm

# Default cgroups - all denied except those whitelisted
lxc.cgroup.devices.deny = a
## /dev/null and zero
lxc.cgroup.devices.allow = c 1:3 rwm
lxc.cgroup.devices.allow = c 1:5 rwm
## consoles
lxc.cgroup.devices.allow = c 5:0 rwm
lxc.cgroup.devices.allow = c 5:1 rwm
## /dev/{,u}random
lxc.cgroup.devices.allow = c 1:8 rwm
lxc.cgroup.devices.allow = c 1:9 rwm
## /dev/pts/*
lxc.cgroup.devices.allow = c 5:2 rwm
lxc.cgroup.devices.allow = c 136:* rwm
## rtc
lxc.cgroup.devices.allow = c 254:0 rm
## tun
lxc.cgroup.devices.allow = c 10:200 rwm
## dev/tty0
lxc.cgroup.devices.allow = c 4:0 rwm
## dev/tty1
lxc.cgroup.devices.allow = c 4:1 rwm

## To use loop devices, copy the following line to the container's
## configuration file (uncommented).
#lxc.cgroup.devices.allow = b 7:* rwm

# Blacklist some syscalls which are not safe in privileged
# containers
lxc.seccomp.profile = /usr/share/lxc/config/common.seccomp
```

