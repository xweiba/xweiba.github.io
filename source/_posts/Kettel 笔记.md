---
title: Kettel 笔记
date: 2021-01-26 12:57:33
tags:
 - Kettel
categories:
 - Kettel
---

# 命令行执行 `kettel` 文件

`Kitchen.bat /file D:\resouces\code\java\kettle\真题迁移\真题转换增量处理.kjb >nul 2>nul`

`"D:\Program Files\pdi-ce\data-integration\Kitchen.bat" "/file" "D:\resouces\code\java\kettle\真题迁移\真题转换增量处理.kjb"`

隐藏输出结果:  
`"D:\Program Files\pdi-ce\data-integration\Kitchen.bat" "/file" "D:\resouces\code\java\kettle\真题迁移\真题转换增量处理.kjb" >nul 2>nul`


`ktr` 文件:  
打开 `cmd` 命令行窗口，转到 `Pan.bat` 所在的目录，如 `D:\Program Files\pdi-ce\data-integration`, 然后执行文件的命令为：`pan /file D:\etltest\EtltestTrans.ktr`

`kjb` 文件:  
打开 `cmd` 命令行窗口，转到 `Pan.bat` 所在的目录，如 `D:\Program Files\pdi-ce\data-integration` ,然后执行文件的命令为：`kitchen /file D:\etltest\jobOK.kjb`

```
"C:\Program Files\Java\jdk1.8.0_121\bin\java.exe"  "-Xms512m" "-Xmx512m" "-XX:MaxPermSize=512m" "-Dhttps.protocols=TLSv1,TLSv1.1,TLSv1.2" "-Djava.library.path=libswt\win64" "-DKETTLE_HOME=" "-DKETTLE_REPOSITORY=" "-DKETTLE_USER=" "-DKETTLE_PASSWORD=" "-DKETTLE_PLUGIN_PACKAGES=" "-DKETTLE_LOG_SIZE_LIMIT=" "-DKETTLE_JNDI_ROOT=" -jar launcher\launcher.jar -lib ..\libswt\win64  -main org.pentaho.di.kitchen.Kitchen -initialDir "D:\Program Files\pdi-ce\data-integration"\ /file D:\resouces\code\java\kettle\真题迁移\真题转换增量处理.kjb
```

# Kettel 中使用java脚本

> 小坑:
> - logBasic(gradeIdName.getId()); 打印日志
> - Map, List等泛型无法自动推导, 需要强制类型转换.
> - lambda 表达式无法使用, 如foreach等之类的. (不同JDK限制不同)

## Maven 依赖
```
<dependency>
    <groupId>pentaho-kettle</groupId>
    <artifactId>kettle-core</artifactId>
    <version>8.2.0.0-342</version>
</dependency>
<dependency>
    <groupId>pentaho-kettle</groupId>
    <artifactId>kettle-dbdialog</artifactId>
    <version>8.2.0.0-342</version>
</dependency>
<dependency>
    <groupId>pentaho-kettle</groupId>
    <artifactId>kettle-engine</artifactId>
    <version>8.2.0.0-342</version>
</dependency>
<dependency>
    <groupId>pentaho</groupId>
    <artifactId>pentaho-metadata</artifactId>
    <version>8.2.0.0-342</version>
</dependency>
<dependency>
    <groupId>pentaho</groupId>
    <artifactId>pentaho-metaverse</artifactId>
    <version>8.2.0.0-342</version>
</dependency>
```

实例代码
```
package com.enableets.edu.kettle.question.service;

import com.enableets.edu.framework.core.util.token.CustomTokenGenerator;
import com.enableets.edu.framework.core.util.token.ITokenGenerator;
import org.pentaho.di.core.exception.KettleException;
import org.pentaho.di.core.exception.KettleStepException;
import org.pentaho.di.trans.step.StepDataInterface;
import org.pentaho.di.trans.step.StepMetaInterface;
import org.pentaho.di.trans.steps.userdefinedjavaclass.TransformClassBase;
import org.pentaho.di.trans.steps.userdefinedjavaclass.UserDefinedJavaClass;
import org.pentaho.di.trans.steps.userdefinedjavaclass.UserDefinedJavaClassData;
import org.pentaho.di.trans.steps.userdefinedjavaclass.UserDefinedJavaClassMeta;

import java.util.*;

/**
 * @author caleb_liu@enable-ets.com
 * @since 2020/09/16 17:37
 **/

public class buildKnowledgeService extends TransformClassBase {

    public buildKnowledgeService(UserDefinedJavaClass parent, UserDefinedJavaClassMeta meta, UserDefinedJavaClassData data) throws KettleStepException {
        super(parent, meta, data);
    }

    private Map knowledgeMap = new HashMap();

    public boolean processRow(StepMetaInterface stepMetaInterface, StepDataInterface stepDataInterface) throws KettleException {
        if (first) {
            first = false;
        }

        Object[] r = getRow();

        if (r == null) {
            logBasic("knowledgeMapSize:" + knowledgeMap.size());
            output();
            setOutputDone();
            return false;
        }
        r = createOutputRow(r, data.outputRowMeta.size());


        KnowledgeBO knowledge = new KnowledgeBO();
        knowledge.setKnowledgeId(get(Fields.In, "knowledge_id").getString(r));
        knowledge.setKnowledgeName(get(Fields.In, "knowledge_name").getString(r));
        knowledge.setParentId(get(Fields.In, "parent_id").getString(r));
        knowledge.setSubjectId(get(Fields.In, "subject_id").getLong(r));
        knowledge.setGradeId(get(Fields.In, "grade_id").getLong(r));
        knowledge.setCreateTime(get(Fields.In, "CREATE_TIME").getDate(r));
        knowledge.setUpdateTime(get(Fields.In, "UPDATE_TIME").getDate(r));
        knowledge.setSearchCode(get(Fields.In, "knowledge_id").getString(r));
        knowledgeMap.put(knowledge.getKnowledgeId(), knowledge);
        return true;
    }

    private void output() throws KettleException {
        tranKnowledge();
        ITokenGenerator tokenGenerator = new CustomTokenGenerator(Long.valueOf(1), Long.valueOf(100));

        List list = new ArrayList(knowledgeMap.values());
        for (int i = 0; i < list.size(); i++) {
            KnowledgeBO knowledge = (KnowledgeBO) list.get(i);
            int index = 0;
            Object[] r = new Object[16];
            r[index ++] = knowledge.getKnowledgeId();
            r[index ++] = knowledge.getKnowledgeName();
            r[index ++] = knowledge.getParentId();
            r[index ++] = knowledge.getSubjectId();
            r[index ++] = knowledge.getGradeId();
            r[index ++] = knowledge.getCreateTime();
            r[index ++] = knowledge.getUpdateTime();
            /* 新增字段 */
            r[index ++] = knowledge.getKnowledgeNo();
            r[index ++] = knowledge.getKnowledgeOrder();
            r[index ++] = knowledge.getMaterialVersionId();
            r[index ++] = knowledge.getDelStatus();
            r[index ++] = knowledge.getKnowledgeType();
            r[index ++] = knowledge.getType();
            r[index ++] = knowledge.getIsDisplay();
            r[index ++] = knowledge.getSearchCode();
            r[index ++] = tokenGenerator.getToken().toString();
            putRow(data.outputRowMeta, r);
        }
    }

    public void tranKnowledge() {
        logBasic("----tranKnowledge----");
        List knowledgeListTemp = new ArrayList();
        List list = new ArrayList(knowledgeMap.values()); // 转换后会导致无序, 需要先把根节点生成出来
        logBasic("tranKnowledge listSize:" + list.size());
        for (int i = 0; i < list.size(); i++) {
            KnowledgeBO knowledge = (KnowledgeBO) list.get(i);
            KnowledgeBO parentKnowledge = (KnowledgeBO)knowledgeMap.get(knowledge.getParentId());
            if (parentKnowledge != null) {
                if (parentKnowledge.getChildKnowledgeList() == null) parentKnowledge.setChildKnowledgeList(new ArrayList());
                parentKnowledge.getChildKnowledgeList().add(knowledge);
            } else {
                knowledgeListTemp.add(knowledge);
            }
        }
        logBasic("----tranKnowledge end----");
        tranChildKnowledgeList(knowledgeListTemp, "");
    }

    public void tranChildKnowledgeList(List childKnowledgeList, String parentSearchCode) {
        logBasic("----tranChildKnowledgeList----");
        logBasic("childKnowledgeList Size:" + childKnowledgeList.size() + "  parentSearchCode:" + parentSearchCode);
        for (int i = 0; i < childKnowledgeList.size(); i++) {
            StringBuilder parentSearchCodeBuilder = new StringBuilder(parentSearchCode);
            KnowledgeBO knowledge = (KnowledgeBO) childKnowledgeList.get(i);
            if (!parentSearchCodeBuilder.toString().equals("")) {
                parentSearchCodeBuilder.append("-");
            }
            String searchCode = parentSearchCodeBuilder + knowledge.getKnowledgeId();
            knowledge.setSearchCode(searchCode);
            knowledge.setKnowledgeOrder(i+1+"");
            knowledgeMap.put(knowledge.getKnowledgeId(), knowledge);
            tranChildKnowledgeList(knowledge.getChildKnowledgeList(), searchCode);
        }
        logBasic("----tranChildKnowledgeList end----");
    }

    public class KnowledgeBO {
        private String knowledgeId;

        private String knowledgeNo;

        private String knowledgeName;

        private String knowledgeGrade;

        private String knowledgeOrder = "1";

        private String parentId;

        private Long gradeId;

        private Long subjectId;

        private String materialVersionId = "VER401";

        private String delStatus = "0";

        private String knowledgeType = "0";

        private String description;

        private String creator;

        private Date createTime;

        private String updator;

        private Date updateTime;

        private String outLineId;

        private String type = "2";

        private String isDisplay = "0";

        private String searchCode;

        private List childKnowledgeList = new ArrayList();

        public String getKnowledgeId() {
            return knowledgeId;
        }

        public void setKnowledgeId(String knowledgeId) {
            this.knowledgeId = knowledgeId;
        }

        public String getKnowledgeNo() {
            return knowledgeId;
        }

        public String getKnowledgeName() {
            return knowledgeName;
        }

        public void setKnowledgeName(String knowledgeName) {
            this.knowledgeName = knowledgeName;
        }

        public String getKnowledgeGrade() {
            return knowledgeGrade;
        }

        public void setKnowledgeGrade(String knowledgeGrade) {
            this.knowledgeGrade = knowledgeGrade;
        }

        public String getKnowledgeOrder() {
            return knowledgeOrder;
        }

        public void setKnowledgeOrder(String knowledgeOrder) {
            this.knowledgeOrder = knowledgeOrder;
        }

        public String getParentId() {
            return parentId;
        }

        public void setParentId(String parentId) {
            this.parentId = parentId;
        }

        public Long getGradeId() {
            return gradeId;
        }

        public void setGradeId(Long gradeId) {
            this.gradeId = gradeId;
        }

        public Long getSubjectId() {
            return subjectId;
        }

        public void setSubjectId(Long subjectId) {
            this.subjectId = subjectId;
        }

        public String getMaterialVersionId() {
            return materialVersionId;
        }

        public void setMaterialVersionId(String materialVersionId) {
            this.materialVersionId = materialVersionId;
        }

        public String getDelStatus() {
            return delStatus;
        }

        public void setDelStatus(String delStatus) {
            this.delStatus = delStatus;
        }

        public String getKnowledgeType() {
            return knowledgeType;
        }

        public void setKnowledgeType(String knowledgeType) {
            this.knowledgeType = knowledgeType;
        }

        public String getDescription() {
            return description;
        }

        public void setDescription(String description) {
            this.description = description;
        }

        public String getCreator() {
            return creator;
        }

        public void setCreator(String creator) {
            this.creator = creator;
        }

        public Date getCreateTime() {
            return createTime;
        }

        public void setCreateTime(Date createTime) {
            this.createTime = createTime;
        }

        public String getUpdator() {
            return updator;
        }

        public void setUpdator(String updator) {
            this.updator = updator;
        }

        public Date getUpdateTime() {
            return updateTime;
        }

        public void setUpdateTime(Date updateTime) {
            this.updateTime = updateTime;
        }

        public String getOutLineId() {
            return outLineId;
        }

        public void setOutLineId(String outLineId) {
            this.outLineId = outLineId;
        }

        public String getType() {
            return type;
        }

        public void setType(String type) {
            this.type = type;
        }

        public String getIsDisplay() {
            return isDisplay;
        }

        public void setIsDisplay(String isDisplay) {
            this.isDisplay = isDisplay;
        }

        public String getSearchCode() {
            return searchCode;
        }

        public void setSearchCode(String searchCode) {
            this.searchCode = searchCode;
        }

        public List getChildKnowledgeList() {
            return childKnowledgeList;
        }

        public void setChildKnowledgeList(List childKnowledgeList) {
            this.childKnowledgeList = childKnowledgeList;
        }
    }
}
```

# Linux 脚本

vim start.sh
```bash
#!/bin/bash

cd /home/icampus3.0/kettle/pdi-ce/data-integration
./kitchen.sh -file /home/icampus3.0/kettle/pdi-ce/work/真题迁移/真题转换增量处理.kjb > /home/icampus3.0/kettle/pdi-ce/work/真题迁移/真题转换增量处理.log & sleep 2
```
chmod 755 start.sh

vim stop.sh
```bash
#!/bin/bash
#created by code

echo "真题转换 stoped....."
pid=$(ps aux|grep -v grep|grep "java"| grep "真题" | awk '{print $2}');
if [[ $pid -gt 1 ]]; then
    kill -9 $pid
fi
```
chmod 755 stop.sh


### 注意:
**自定义的输出字段需要在Kettel的java面板下方的字段中按顺序添加对应属性名称, 注意只能添加字段, 原字段是不可修改和改变位置的.**