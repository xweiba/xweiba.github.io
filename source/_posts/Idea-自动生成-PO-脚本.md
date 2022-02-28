---
title: Idea 自动生成 PO 脚本
date: 2020-09-30 13:28:48
tags: 
 - Idea
 - Java
categories:
 - Idea
---

> 使用 `Groovy` 脚本生成对应的`PO`, 非 `Mybatis`逆向工程生成, 
>
> 来自: [IDEA -- 自动生成POJO](https://juejin.im/post/6844903846452396039)

## Groovy脚本记录

```groovy
import com.intellij.database.model.DasTable
import com.intellij.database.model.ObjectKind
import com.intellij.database.util.Case
import com.intellij.database.util.DasUtil

import java.text.DateFormat
import java.text.SimpleDateFormat

/*
 * Available context bindings:
 *   SELECTION   Iterable<DasObject>
 *   PROJECT     project
 *   FILES       files helper
 */
packageName = "com.xxx.po;"  //这里要换成自己项目 实体的包路径
typeMapping = [
        (~/(?i)int/)                      : "Integer",  //数据库类型和Jave类型映射关系
        (~/(?i)float|double|decimal|real/): "Double",
        (~/(?i)bool|boolean/)             : "Boolean",
        (~/(?i)datetime|timestamp/)       : "java.util.Date",
        (~/(?i)date/)                     : "java.sql.Date",
        (~/(?i)time/)                     : "java.sql.Time",
        (~/(?i)/)                         : "String"
]

FILES.chooseDirectoryAndSave("Choose directory", "Choose where to store generated files") { dir ->
    SELECTION.filter { it instanceof DasTable && it.getKind() == ObjectKind.TABLE }.each { generate(it, dir) }
}

def generate(table, dir) {
    def className = javaName(table.getName(), true) + "PO"
    def fields = calcFields(table)
    new File(dir, className + ".java").withPrintWriter("utf-8") { out -> generate(out, table, className, fields) }
}

def generate(out, table, className, fields) {
    def tableName = table.getName()
    out.println "package $packageName"
    out.println ""
    out.println "import lombok.*;"
    out.println ""
    out.println "import javax.persistence.*;"
    out.println ""
    out.println "/**"
    out.println " * @author xxx"
    out.println " **/"
    out.println "@Data"
    out.println "@Entity"
    out.println "@Table(name = \"$tableName\")"
    out.println "public class $className {"
    out.println ""
    // 这里如果需要生成id字段需要打开
    if ((tableName + "_id").equalsIgnoreCase(fields[0].colum) || "id".equalsIgnoreCase(fields[0].colum)) {
        out.println "    @Id"
        out.println "    @GeneratedValue(strategy=GenerationType.IDENTITY)"
    }
    fields.each() {
        if (it.comment != "" && it.comment != null) {
            out.println "    /**"
            out.println "      * ${it.comment}"
            out.println "      **/"
        }
        if (it.annos != "") out.println "  ${it.annos}"
        if (it.colum != it.name) {
            out.println "    @Column(name = \"${it.colum}\")"
        }

        out.println "    private ${it.type} ${it.name};"
        out.println ""
    }
    out.println "}"
}

def calcFields(table) {
    DasUtil.getColumns(table).reduce([]) { fields, col ->
        def spec = Case.LOWER.apply(col.getDataType().getSpecification())
        def typeStr = typeMapping.find { p, t -> p.matcher(spec).find() }.value
        fields += [[
                           name   : javaName(col.getName(), false),
                           colum  : col.getName(),
                           type   : typeStr,
                           comment: col.getComment(),
                           annos  : ""]]
    }
}

def javaName(str, capitalize) {
    def s = str.split(/(?<=[^\p{IsLetter}])/).collect { Case.LOWER.apply(it).capitalize() }
            .join("").replaceAll(/[^\p{javaJavaIdentifierPart}]/, "_").replaceAll(/_/, "")
    capitalize || s.length() == 1 ? s : Case.LOWER.apply(s[0]) + s[1..-1]
}
```

