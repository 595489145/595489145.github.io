---
layout: post
title: "Java（一）EasyCode配置"
catalog: true
header-style: text
tag: 
    - Java
    - MyBatis
    - Easycode
---
# EasyCode插件下载
easycode可以自动生成数据库entity、mapper等文件
从idea的Setting->Plugins里寻找easycode,下载


# 编辑EasyCode模板
## 框架结构
首先需要确定自己框架的结构，以便确定包的位置
```linux
blog
├─guabby-blog
│  ├─src
│  │  ├─main
│  │  │  ├─java
│  │  │  │  └─com
│  │  │  │      └─guabby
│  │  │  │          └─controller
└─guabby-framework
    ├─src
    │  ├─main
    │  │  ├─java
    │  │  │  └─com
    │  │  │      └─guabby
    │  │  │          ├─domain
    │  │  │          │  ├─entity
    │  │  │          ├─mapper
    │  │  │          ├─service
    │  │  │          │  └─Impl
```
我这里是controller层单独放到一个模块，entity,mapper和service放到通用模块中，生成时只生成通用模块的代码。
## 模板
从Setting侧边栏找EasyCode->Template

**1.entity.java.vm**
```
##导入宏定义
$!{define.vm}

##保存文件（宏定义）
#save("/entity", ".java")

##包路径（宏定义）
#if($tableInfo.savePackageName)package $!{tableInfo.savePackageName}.#{end}domain.entity;

##自动导入包（全局变量）
$!{autoImport.vm}
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import java.io.Serializable;
import com.baomidou.mybatisplus.annotation.TableName;


##表注释（宏定义）
#tableComment("表实体类")
## 表原始名
@TableName("$!{tableInfo.obj.name}")
@Data
@AllArgsConstructor
@NoArgsConstructor
public class $!{tableInfo.name} implements Serializable {
#foreach($column in $tableInfo.fullColumn)
    #if(${column.comment})//${column.comment}#end

    private $!{tool.getClsNameByFullName($column.type)} $!{column.name};
#end

#foreach($column in $tableInfo.fullColumn)
#end
}

##设置文件储存路径，默认使用选中路径
$!callback.setSavePath($tool.append($tableInfo.savePath, "/domain/entity"))
```

**2.mapper.java.vm**
```
##导入宏定义
$!{define.vm}

## 不设置package,从模板定死
#set($MyPackagePath= "com.guabby")


##设置表后缀（宏定义）
#setTableSuffix("Mapper")

##保存文件（宏定义）
#save("/mapper", "Mapper.java")

##包路径（宏定义）
#if($tableInfo.savePackageName)package $!{tableInfo.savePackageName}.#{end}mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.guabby.domain.entity.$!tableInfo.name;
import org.springframework.stereotype.Repository;

##表注释（宏定义）
#tableComment("表数据库访问层")
@Repository
public interface $!{tableName} extends BaseMapper<$!tableInfo.name> {

}

##设置文件储存路径，默认使用选中路径
$!callback.setSavePath($tool.append($tableInfo.savePath, "/mapper"))
```
3.service.java.vm
```
##导入宏定义
$!{define.vm}

## 不设置package,从模板定死
#set($MyPackagePath= "com.guabby")


##设置表后缀（宏定义）
#setTableSuffix("Service")

##保存文件（宏定义）
#save("/service", "Service.java")

##包路径（宏定义）
#if($tableInfo.savePackageName)package $!{tableInfo.savePackageName}.#{end}service;

import com.baomidou.mybatisplus.extension.service.IService;
import $!{tableInfo.savePackageName}.domain.entity.$!tableInfo.name;

##表注释（宏定义）
#tableComment("表服务接口")
public interface $!{tableName} extends IService<$!tableInfo.name> {

}

##设置文件储存路径，默认使用选中路径
$!callback.setSavePath($tool.append($tableInfo.savePath, "/service"))

```

**4.serviceImpl.java.vm**

```
##导入宏定义
$!{define.vm}

## 不设置package,从模板定死
#set($MyPackagePath= "com.guabby")

##设置表后缀（宏定义）
#setTableSuffix("ServiceImpl")

##保存文件（宏定义）
#save("/service/Impl", "ServiceImpl.java")

##包路径（宏定义）
#setPackageSuffix($tool.append($MyPackagePath,".service.Impl"))

import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import $!{tableInfo.savePackageName}.mapper.$!{tableInfo.name}Mapper;
import $!{tableInfo.savePackageName}.domain.entity.$!{tableInfo.name};
import $!{tableInfo.savePackageName}.service.$!{tableInfo.name}Service;
import org.springframework.stereotype.Service;

##表注释（宏定义）
#tableComment("表服务实现类")
@Service("$!tool.firstLowerCase($tableInfo.name)Service")
public class $!{tableName} extends ServiceImpl<$!{tableInfo.name}Mapper, $!{tableInfo.name}> implements $!{tableInfo.name}Service {

}

##设置文件储存路径，默认使用选中路径
$!callback.setSavePath($tool.append($tableInfo.savePath, "/service/Impl"))

```

# 根据数据库生成
## 导入数据库驱动
```xml
<!--mysql数据库驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.25</version>
        </dependency>
```
## 链接数据库
从idea的侧导航栏查找Database，输入账号密码、使用的表等信息进行连接

## 生成代码
![easycode位置](https://www.vcg.com/creative/811557570)
右键表名->EasyCode->Generate Code
![easycode注释](https://img-blog.csdnimg.cn/007b08c8a56b4f0ea8f928f1a29d66e3.png)
结束。


