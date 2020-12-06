---
layout: post
title: "Java 自定义注解及使用场景"
# subtitle: ' ""'
date: 2020-12-06 21:37:00
author: "Shawn"
header-style: text
catalog: true
tags:
  - Spring
  - 标注
---

# [Java 自定义注解及使用场景](https://juejin.cn/post/6844903949233815566)

Java自定义注解一般使用场景为：自定义注解+拦截器或者AOP，使用自定义注解来自己设计框架，使得代码看起来非常优雅。 本文将先从自定义注解的基础概念说起，然后开始实战，写小段代码实现自定义注解+拦截器，自定义注解+AOP。

##### 一. 什么是注解(Annotation)

Java注解是什么，以下是引用自维基百科的内容

> Java注解又称Java标注，是JDK5.0版本开始支持加入源代码的特殊语法元数据。
> Java语言中的类、方法、变量、参数和包等都可以被标注。和Javadoc不同，Java标注可以通过反射获取标注内容。在编译器生成类文件时，标注可以被嵌入到字节码中。Java虚拟机可以保留标注内容，在运行时可以获取到标注内容。 当然它也支持自定义Java标注。

##### 二. 注解体系图

元注解：`java.lang.annotation`中提供了元注解，可以使用这些注解来定义自己的注解。主要使用的是Target和Retention注解

![元注解](https://user-gold-cdn.xitu.io/2019/9/20/16d4e06c9f2f3bd0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

注解处理类：既然上面定义了注解，那得有办法拿到我们定义的注解啊。`java.lang.reflect.AnnotationElement`接口则提供了该功能。注解的处理是通过java反射来处理的。如下，反射相关的类Class, Method, Field都实现了AnnotationElement接口。

![反射处理](https://user-gold-cdn.xitu.io/2019/9/20/16d4e06cdc6f4518?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![AnnotationElement接口方法](https://user-gold-cdn.xitu.io/2019/9/20/16d4e06cc9dc58ff?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

因此，只要我们通过反射拿到Class, Method, Field类，就能够通过getAnnotation(Class)拿到我们想要的注解并取值。



##### 三. 常用元注解

**Target**：描述了注解修饰的对象范围，取值在`java.lang.annotation.ElementType`定义，常用的包括：

- METHOD：用于描述方法
- PACKAGE：用于描述包
- PARAMETER：用于描述方法变量
- TYPE：用于描述类、接口或enum类型

**Retention**: 表示注解保留时间长短。取值在`java.lang.annotation.RetentionPolicy`中，取值为：

- SOURCE：在源文件中有效，编译过程中会被忽略
- CLASS：随源文件一起编译在class文件中，运行时忽略
- RUNTIME：在运行时有效

只有定义为`RetentionPolicy.RUNTIME`时，我们才能通过注解反射获取到注解。 所以，假设我们要自定义一个注解，它用在字段上，并且可以通过反射获取到，功能是用来描述字段的长度和作用。可以定义如下，代码见[我的GitHub](https://github.com/zebinh/annotation-demo) 。

```
@Target(ElementType.FIELD)  //  注解用于字段上
@Retention(RetentionPolicy.RUNTIME)  // 保留到运行时，可通过注解获取
public @interface MyField {
    String description();
    int length();
}
复制代码
```

##### 四. 示例-反射获取注解

先定义一个注解：

```
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyField {
    String description();
    int length();
}
复制代码
```

通过反射获取注解

```
public class MyFieldTest {

    //使用我们的自定义注解
    @MyField(description = "用户名", length = 12)
    private String username;

    @Test
    public void testMyField(){

        // 获取类模板
        Class c = MyFieldTest.class;

        // 获取所有字段
        for(Field f : c.getDeclaredFields()){
            // 判断这个字段是否有MyField注解
            if(f.isAnnotationPresent(MyField.class)){
                MyField annotation = f.getAnnotation(MyField.class);
                System.out.println("字段:[" + f.getName() + "], 描述:[" + annotation.description() + "], 长度:[" + annotation.length() +"]");
            }
        }

    }
}
复制代码
```

运行结果

![运行结果](data:image/svg+xml;utf8,<?xml version="1.0"?><svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="535" height="149"></svg>)



##### 应用场景一：自定义注解+拦截器 实现登录校验

接下来，我们使用springboot拦截器实现这样一个功能，如果方法上加了@LoginRequired，则提示用户该接口需要登录才能访问，否则不需要登录。 首先定义一个LoginRequired注解

```
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LoginRequired {
    
}
复制代码
```

然后写两个简单的接口，访问sourceA，sourceB资源

```
@RestController
public class IndexController {

    @GetMapping("/sourceA")
    public String sourceA(){
        return "你正在访问sourceA资源";
    }

    @GetMapping("/sourceB")
    public String sourceB(){
        return "你正在访问sourceB资源";
    }

}
复制代码
```

没添加拦截器之前成功访问

![sourceB](data:image/svg+xml;utf8,<?xml version="1.0"?><svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="550" height="257"></svg>)

实现spring的HandlerInterceptor 类先实现拦截器，但不拦截，只是简单打印日志，如下：



```
public class SourceAccessInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("进入拦截器了");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }
}
复制代码
```

实现spring类WebMvcConfigurer，创建配置类把拦截器添加到拦截器链中

```
@Configuration
public class InterceptorTrainConfigurer implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new SourceAccessInterceptor()).addPathPatterns("/**");
    }
}
复制代码
```

拦截成功如下

![拦截了](data:image/svg+xml;utf8,<?xml version="1.0"?><svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="474" height="137"></svg>)

在sourceB方法上添加我们的登录注解@LoginRequired



```
@RestController
public class IndexController {

    @GetMapping("/sourceA")
    public String sourceA(){
        return "你正在访问sourceA资源";
    }

    @LoginRequired
    @GetMapping("/sourceB")
    public String sourceB(){
        return "你正在访问sourceB资源";
    }

}
复制代码
```

简单实现登录拦截逻辑

```
@Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("进入拦截器了");

        // 反射获取方法上的LoginRequred注解
        HandlerMethod handlerMethod = (HandlerMethod)handler;
        LoginRequired loginRequired = handlerMethod.getMethod().getAnnotation(LoginRequired.class);
        if(loginRequired == null){
            return true;
        }

        // 有LoginRequired注解说明需要登录，提示用户登录
        response.setContentType("application/json; charset=utf-8");
        response.getWriter().print("你访问的资源需要登录");
        return false;
    }
复制代码
```

运行成功，访问sourceB时需要登录了，访问sourceA则不用登录，完整代码见[我的GitHub](https://github.com/zebinh/annotation-demo)

![sourceA](data:image/svg+xml;utf8,<?xml version="1.0"?><svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="491" height="193"></svg>)

![sourceB](data:image/svg+xml;utf8,<?xml version="1.0"?><svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="521" height="224"></svg>)



##### 应用场景二：自定义注解+AOP 实现日志打印

先导入切面需要的依赖包

```
<dependency>
      <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
复制代码
```

定义一个注解@MyLog

```
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyLog {
    
}
复制代码
```

定义一个切面类，见如下代码注释理解：

```
@Aspect // 1.表明这是一个切面类
@Component
public class MyLogAspect {

    // 2. PointCut表示这是一个切点，@annotation表示这个切点切到一个注解上，后面带该注解的全类名
    // 切面最主要的就是切点，所有的故事都围绕切点发生
    // logPointCut()代表切点名称
    @Pointcut("@annotation(me.zebin.demo.annotationdemo.aoplog.MyLog)")
    public void logPointCut(){};

    // 3. 环绕通知
    @Around("logPointCut()")
    public void logAround(ProceedingJoinPoint joinPoint){
        // 获取方法名称
        String methodName = joinPoint.getSignature().getName();
        // 获取入参
        Object[] param = joinPoint.getArgs();

        StringBuilder sb = new StringBuilder();
        for(Object o : param){
            sb.append(o + "; ");
        }
        System.out.println("进入[" + methodName + "]方法,参数为:" + sb.toString());

        // 继续执行方法
        try {
            joinPoint.proceed();
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
        System.out.println(methodName + "方法执行结束");

    }
}
复制代码
```

在步骤二中的IndexController写一个sourceC进行测试，加上我们的自定义注解：

```
    @MyLog
    @GetMapping("/sourceC/{source_name}")
    public String sourceC(@PathVariable("source_name") String sourceName){
        return "你正在访问sourceC资源";
    }
复制代码
```

启动springboot web项目，输入访问地址

![访问项目](https://user-gold-cdn.xitu.io/2019/9/20/16d4e06d315dee2a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![切面成功](https://user-gold-cdn.xitu.io/2019/9/20/16d4e06d4c45ca3b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)