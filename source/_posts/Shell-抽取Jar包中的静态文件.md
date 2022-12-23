---
title: Shell-抽取Jar包中的静态文件
date: 2019/11/06 15:56:14
tags:
 - Shell
categories:
 - Shell
---

# 抽取`Jar`包中的静态文件
```shell
#!/bin/bash
#######################
# created by code on 2019/11/06 15:56:14
#######################
function setVars(){
       basepath=/home/icampus3.0
       jdkHome=${basepath}/jdk8
       servHome=${basepath}/pkgs/paper-manager

       start=${servHome}/start.sh
       log=${basepath}/logs/paper-manager.log
       java=${jdkHome}/bin/java
       servFile=${servHome}/paper-manager.jar
       option="-server -Xms180m -Xmx200m -Dfile.encoding=utf-8 -Dspring.config.location=bootstrap.yml -Darchaius.configurationSource.additionalUrls=file:///home/icampus3.0/pkgs/config/conf_prod/globalconfig.properties -jar ${servFile} "

       # manager unzip config
       servName=paper
       jarPath=BOOT-INF/classes
       # 静态文件配置
       servJsPath=${jarPath}/static/custom/${servName}
       JsHome=${basepath}/icampus-static/custom/${servName}

       # templates 文件配置
       servHtmlPath=${jarPath}/templates
       HtmlHome=${servHome}/templates

}
function start(){
       cd $servHome
       sh stop.sh
       echo -e "  Start to start up paper-manager : "

       $java $option > $log 2>&1 &

       echo $! > pid

       if [[ -z $(cat pid) ]]; then
           echo -e "  Failed to start up paper-manager"

           return 1
       else
           echo -e "  Successful to start up paper-manager"

           return 0
       fi
}

# $1:处理类型 $2:jar包中的相对路径 $3:部署的绝对路径, 会自动在部署路径添加_bak后缀生成上一次的备份文件夹
function unzipStaticFile() {
      echo -e "  "
      echo -e "  ---------UnzipStaticFile start---------"
      cd $servHome
      if [ ! -n "$1" -o ! -n "$2" -o ! -n "$3" ]
         then
           echo -e "  Unzip params is Not Full!"
           echo -e "  Params: typeName:$1, unzipTempPath:$2, dstPath:$3"
           exit 1
      fi
      echo -e "  Start unZip paper-manager $1 file..."
      echo -e "  Params: typeName:$1, unzipTempPath:$2, dstPath:$3"
      echo "  Clean unzip temp path: ${servHome}/temp/$2"
      rm -rf ${servHome}/temp/$2  >/dev/null 2>&1
      echo "  Unzip ..."
      unzip ${servFile} $2/* -d ${servHome}/temp >/dev/null 2>&1
      # wait 1s unzip Successful
      sleep 1
      if [ $? -ne 0 ]
        then
            echo -e "  paper-manager unzip Failed!"
            exit 1
      fi
      echo -e "  Unzip Successful!\n"

      echo -e "  Start Deploy paper-manager $1 file..."
      # 判断备份文件夹是否存在
      if [ -d "$3_bak" ]
        then
        echo "  Clean paper-manager $1 file old backup path: $3_bak"
        rm -rf "$3_bak" >/dev/null 2>&1
      fi

      # 备份一份
      echo "  Backup now paper-manager $1 file path: $3_bak"
      mv $3 "$3_bak" >/dev/null 2>&1
      # 将新的静态文件移动过来
      echo "  Move new paper-manager $1 file path:$3"
      mv ${servHome}/temp/$2 $3 >/dev/null 2>&1
      echo -e "  Deploy paper-manager $1 file Successful!"
      echo -e "  ---------UnzipStaticFile end---------"
      echo -e "  "
}

setVars
start
unzipStaticFile JS ${servJsPath} ${JsHome}

# html 可选部署
#if [ $1 -a $1 = 1 ]
# then
# unzipStaticFile Html ${servHtmlPath} ${HtmlHome}
#fi

```