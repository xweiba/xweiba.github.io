---
title: Spring RMI 远程函数调用
date: 2018-06-08 12:44:05
tags:
 - Spring 
 - RMI
categories:
 - Spring
---

1.需要分离出来的类
- 实体类
- dao层, 直接在server端实现. 
- 

OSS 封装为接口 阿里云 与 七牛云 
阿里云方法:
- OSSClient getOssClient();
- boolean createBucket();
- boolean deleteBucket();
- String updateFile();
- InputStream getFile();
- boolean deleteFile();

七牛云
- Auth getAuth();
- Configuration getCfg():
- UploadManager getUploadManager();
- BucketManager getBucketManager();
- boolean delete(String keyFile)
- boolean updateFile(Integer id, MultipartFile multipartFile)
- BucketManager.FileListIterator getObjectList

暂时需要实现接口:
上传
文件列表