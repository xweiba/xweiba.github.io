---
title: 解决CentOS6停止更新支持后yum源失效的问题
date: 2021-01-07 13:11:31
tags:
 - CentOS6
categories:
 - Linux
---

现在测试比较正常的国内源: sohu

CentOS-Base.repo:
```
# CentOS-Base.repo
#
# This file uses a new mirrorlist system developed by Lance Davis for CentOS.
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client. You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS-$releasever – Base
baseurl=http://mirrors.sohu.com/centos/6.10/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.sohu.com/centos/RPM-GPG-KEY-CentOS-6

#released updates
[updates]
name=CentOS-$releasever – Updates
baseurl=http://mirrors.sohu.com/centos/6.10/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.sohu.com/centos/RPM-GPG-KEY-CentOS-6

#packages used/produced in the build but not released
#[addons]
#name=CentOS-$releasever – Addons
#baseurl=http://mirrors.sohu.com/centos/$releasever/addons/$basearch/
#gpgcheck=1
#gpgkey=http://mirrors.sohu.com/centos/RPM-GPG-KEY-CentOS-6
#additional packages that may be useful
[extras]
name=CentOS-$releasever – Extras
baseurl=http://mirrors.sohu.com/centos/6.10/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.sohu.com/centos/RPM-GPG-KEY-CentOS-6
#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever – Plus
baseurl=http://mirrors.sohu.com/centos/6.10/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.sohu.com/centos/RPM-GPG-KEY-CentOS-6
```

epel.repo:
```
[epel]
name=Extra Packages for Enterprise Linux 6 - $basearch
#baseurl=http://mirrors.aliyuncs.com/epel/6/$basearch
baseurl=http://mirrors.sohu.com/centos/6/os/$basearch/
#http://mirrors.sohu.com/epel/6/$basearch
http://mirrors.sohu.com/centos/6/os/$basearch/ 
failovermethod=priority
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
 
[epel-debuginfo]
name=Extra Packages for Enterprise Linux 6 - $basearch - Debug
baseurl=http://mirrors.aliyuncs.com/epel/6/$basearch/debug
http://mirrors.sohu.com/epel/6/$basearch/debug
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
gpgcheck=0
 
[epel-source]
name=Extra Packages for Enterprise Linux 6 - $basearch - Source
baseurl=http://mirrors.aliyuncs.com/epel/6/SRPMS
http://mirrors.sohu.com/epel/6/SRPMS
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
gpgcheck=0

```

更新yum:
```
yum clean all
yum makecache
```