---
layout: post
title: "TODO-Road to Spring 1"
# subtitle: ' ""'
date: 2020-12-07 20:07:00
author: "Shawn"
header-style: text
catalog: true
tags:
  - Spring
  - 开发记录
---

@SpringBootApplication：之前用户使用的是3个注解注解他们的main类。分别是@Configuration,@EnableAutoConfiguration,@ComponentScan。由于这些注解一般都是一起使用，spring boot提供了一个统一的注解@SpringBootApplication。

### package、folder、source folder之间的区别

​	首先我们说明最简单的folder，与我们操作系统下的文件夹一样，不起到任何作用，只是存放文件而已，存档到folder中的任何文件都只当作普通文件夹，不管其中文件是否正确，因为IDE不会对其进行任何检查。

​	package就是Java开发过程中的包，其路径就是每一个类的包路径，其必须存放在一个source folder下。

​	source folder使用来存放Java源代码的，其下的所有Java源文件都会被实时编译成class文件。对于JavaSE的项目会被编译到项目目录下的bin目录下，对于JavaEE项目会被便宜到相应的/WEB-INF/classes文件夹中，无论是哪种项目bin文件和classes文件夹都是不会在IDE中显示的，并且上面说到的都是默认的便宜路径。

### @RestController

当我们使用这个注解来修改我们的Controller类而不是使用@Controller这个注解，@RestController中也包含了@Controller，同时包含@ResponseBody(ResponseBody既然修饰在类上，就表示这个类中所有的方法都是@ResponseBody，当我们使用字符串返回的时候前台我们会以文本格式展示，如果是对象它会自动转换成json格式返回)

- mapper : 在接口上添加了这个注解表示这个接口是基于注解实现的CRUD。
- Results: 返回的map结果集，property 表示User类的字段，column 表示对应数据库的字段。
- Param:sql条件的字段。
- Insert、Select、Update、Delete:对应数据库的增、查、改、删



