---
title: Idea Live Templates 配置
date: 2019-03-22 12:20:03
tags:
 - Idea
 - template
 - 设置
categories:
 - Idea
---

# 自定义实时模板
> Idea 有很多如 `psvm` `iter` 的实时模板, 我们也可以自定义一些自己常用的.
> 设置路径: Setting-Editor-Live Templates

首先点击右边的 `+` 号创建一个`Template Group`分组, 然后再新建的分组中添加对应的实时捷模板

## 自动生成方法说明注释
- Abbreviation: mdoc  设置指令, 在方法上方输入`/mdoc`会调用该模板, 注意不输入`/`无法取到参数与返回值
- Template Text: 模板
    ```
    /**
     * $description$
     * @date $date$ $time$
     * @since xxx@qq.com
    $params$
    $return$
     */
    ```
    Edit variables(编辑模板中的变量):
- description: 留空, 生成模板时留空的值会让你手动输入
- date: date() 
- time: time()
- params:
    ```
    groovyScript("if(\"${_1}\".length() == 2) {return '';} else {def result=''; def params=\"${_1}\".replaceAll('[\\\\[|\\\\]|\\\\s]', '').split(',').toList();for(i = 0; i < params.size(); i++) {if(i<(params.size()-1)){result+=' * @param ' + params[i] + ' : ' + '\\n	'}else{result+=' * @param ' + params[i] + ' : '}}; return result;}", methodParameters()); 
    ```
- return: 
    ```
    groovyScript("def returnType = \"${_1}\"; def result = ' * @return ' + returnType; return result;", methodReturnType());
    ```
## 生成RequestMapping方法
- Abbreviation: mrqm  设置指令, 在代码中输入`mrqm`会生成生成`RequestMapping`方法
- Template Text: 模板, ***$END$*** 设置光标最后的位置
    ```
    /mdo$END$
    @RequestMapping("/$RequestMapping$")
    public String $method$($Params$) {
        
        return "$returnObject$";
    }
    ```
## 生成ResponseBody方法
- Abbreviation: mrsb 设置指令, 在代码中输入`mrsb`会生成生成`ResponseBody`方法
- Template Text: 模板, ***$END$*** 设置光标最后的位置
    ```
    /mdo$END$
    @RequestMapping("/$RequestMapping$")
    @ResponseBody
    public $returnType$ $method$($Params$) {
        
        return $returnObject$;
    }
    ```
## 生成私有方法
- Abbreviation: mpr 设置指令, 在代码中输入`mpr`会生成私有方法
- Template Text: 模板, ***$END$*** 设置光标最后的位置
    ```
    /mdo$END$
    private $returnType$ $method$($Params$) {
        
        return $returnObject$;
    }
    ```
## 生成无返回值的私有方法
- Abbreviation: mprv 设置指令, 在代码中输入`mprv`会生成无返回值的私有方法
- Template Text: 模板, ***$END$*** 设置光标最后的位置
    ```
    /mdo$END$
    private void $method$($Params$) {
    
    }
    ```
## 生成公共方法
- Abbreviation: mpu 设置指令, 在代码中输入`mpu`会生成公共方法
- Template Text: 模板, ***$END$*** 设置光标最后的位置
    ```
    /mdo$END$
    public $returnType$ $method$($Params$) {
        
        return $returnObject$;
    }
    ```
## 生成无返回值的公共方法
- Abbreviation: mpuv 设置指令, 在代码中输入`mpuv`会生成无返回值的公共方法
- Template Text: 模板, ***$END$*** 设置光标最后的位置
    ```
    /mdo$END$
    public void $method$($Params$) {
        
    }
    ```
    
    
# 文件模板
> Idea 支持为所有文件创建/修改模板.   
> 设置路径: Setting-Editor-File and Code Templates

***注意占位符需开启 Enable Live Templates 功能***

## Class 文件模板
根据文件名 `VO, BO, DTO, Controller, Restful, Service` 自动生成相关注解
```
#if (${PACKAGE_NAME} && ${PACKAGE_NAME} != "")package ${PACKAGE_NAME};#end
#parse("File Header.java")

#if (${NAME.endsWith("VO")} || ${NAME.endsWith("BO")} || ${NAME.endsWith("DTO")})
import lombok.*;
import lombok.experimental.Accessors;
#end
#if (${NAME.endsWith("VO")})
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
#end
#if (${NAME.endsWith("Controller")})
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import com.enableets.edu.teachingassistant.manager.core.Constants;
#end
#if (${NAME.endsWith("Service")})
import org.springframework.stereotype.Service;
#end
#if (${NAME.endsWith("Restful")})
import io.swagger.annotations.Api;
import org.springframework.web.bind.annotation.*;
#end

/**
*
* ${description}
*
* @author wb
*
* @date ${DATE}
**/

#if (${NAME.endsWith("VO")} || ${NAME.endsWith("BO")} || ${NAME.endsWith("DTO")})
@Accessors(chain = true)
@AllArgsConstructor
@NoArgsConstructor
@Data
#end
#if (${NAME.endsWith("VO")})
@ApiModel(value = "#[[$value$]]#", description = "#[[$description$]]#")
#end
#if (${NAME.endsWith("Controller")})
@Controller
@RequestMapping(value = Constants.CONTEXT_PATH + "/#[[$REQUESTION$]]#")
#end
#if (${NAME.endsWith("Restful")})
@Api(tags="#[[$tags$]]#", description="#[[$description$]]#")
@RestController
@RequestMapping(value = "#[[$RequestMapping$]]#")
@ResponseResult
#end
#if (${NAME.endsWith("Service")})
@Service
#end
public class ${NAME} {
    #[[$END$]]#
}
```

## Interface 模板
```
#if (${PACKAGE_NAME} && ${PACKAGE_NAME} != "")package ${PACKAGE_NAME};#end
#parse("File Header.java")

#if (${NAME.endsWith("DAO")})
import org.springframework.stereotype.Component;
import com.enableets.edu.framework.core.dao.BaseDao;
#end
#if (${NAME.endsWith("Client")})
import org.springframework.cloud.openfeign.FeignClient;
#end

/**
*
* ${description}
*
* @author wb
*
* @date ${DATE}
**/
#if (${NAME.endsWith("DAO")})
@Component
#end
#if (${NAME.endsWith("Client")})
@FeignClient(name = "${#[[$microservice$]]#}")
#end
public interface ${NAME} #if (${NAME.endsWith("DAO")})extends BaseDao<#[[$DAOPO$]]#> #end{
    #[[$END$]]#
}

```