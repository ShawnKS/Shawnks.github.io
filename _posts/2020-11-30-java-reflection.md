---
layout: post
title: "java反射总结"
# subtitle: ' ""'
date: 2020-11-30 15:35:00
author: "Shawn"
# header-img: "img/post-bg-2020-11.jpg"
catalog: true
tags:
  - java
---

## 关于 java 中的反射(reflection)

- JVM 为每个加载的`class`及`interface`创建了对应的`Class`实例来保存`class`及`interface`的所有信息；获取一个`class`对应的`Class`实例后，就可以获取该`class`的所有信息；通过 Class 实例获取`class`信息的方法称为反射（Reflection）；JVM 总是动态加载`class`，可以在运行期根据条件来控制加载 class。
- Java 的反射 API 提供的`Field`类封装了字段的所有信息：通过`Class`实例的方法可以获取`Field`实例：`getField()`，`getFields()`，`getDeclaredField()`，`getDeclaredFields()`；通过 Field 实例可以获取字段信息：`getName()`，`getType()`，`getModifiers()`；通过 Field 实例可以读取或设置某个对象的字段，如果存在访问限制，要首先调用`setAccessible(true)`来访问非`public`字段。通过反射读写字段是一种非常规方法，它会破坏对象的封装。

> 有童鞋会问：如果使用反射可以获取`private`字段的值，那么类的封装还有什么意义？
>
> 答案是正常情况下，我们总是通过`p.name`来访问`Person`的`name`字段，编译器会根据`public`、`protected`和`private`决定是否允许访问字段，这样就达到了数据封装的目的。
>
> 而反射是一种非常规的用法，使用反射，首先代码非常繁琐，其次，它更多地是给工具或者底层框架来使用，目的是在不知道目标实例任何信息的情况下，获取特定字段的值。
>
> 此外，`setAccessible(true)`可能会失败。如果 JVM 运行期存在`SecurityManager`，那么它会根据规则进行检查，有可能阻止`setAccessible(true)`。例如，某个`SecurityManager`可能不允许对`java`和`javax`开头的`package`的类调用`setAccessible(true)`，这样可以保证 JVM 核心库的安全。

- Java 的反射 API 提供的 Method 对象封装了方法的所有信息：通过`Class`实例的方法可以获取`Method`实例：`getMethod()`，`getMethods()`，`getDeclaredMethod()`，`getDeclaredMethods()`；通过`Method`实例可以获取方法信息：`getName()`，`getReturnType()`，`getParameterTypes()`，`getModifiers()`；通过`Method`实例可以调用某个对象的方法：`Object invoke(Object instance, Object... parameters)`；通过设置`setAccessible(true)`来访问非`public`方法；通过反射调用方法时，仍然遵循多态原则。

- `Constructor`对象封装了构造方法的所有信息；通过`Class`实例的方法可以获取`Constructor`实例：`getConstructor()`，`getConstructors()`，`getDeclaredConstructor()`，`getDeclaredConstructors()`；通过`Constructor`实例可以创建一个实例对象：`newInstance(Object... parameters)`； 通过设置`setAccessible(true)`来访问非`public`构造方法。

- 通过`Class`对象可以获取继承关系：

  - `Class getSuperclass()`：获取父类类型；
  - `Class[] getInterfaces()`：获取当前类实现的所有接口。

  通过`Class`对象的`isAssignableFrom()`方法可以判断一个向上转型是否可以实现。

- 在运行期动态创建一个`interface`实例的方法如下：

  1. 定义一个`InvocationHandler`实例，它负责实现接口的方法调用；

  2. 通过

     ```
     Proxy.newProxyInstance()
     ```

     创建

     ```
     interface
     ```

     实例，它需要 3 个参数：

     1. 使用的`ClassLoader`，通常就是接口类的`ClassLoader`；
     2. 需要实现的接口数组，至少需要传入一个接口进去；
     3. 用来处理接口方法调用的`InvocationHandler`实例。

  3. 将返回的`Object`强制转型为接口。

  4. Java 标准库提供了动态代理功能，允许在运行期动态创建一个接口的实例；

     动态代理是通过`Proxy`创建代理对象，然后将接口方法“代理”给`InvocationHandler`完成的。

我们来考察这样一种情况：一个`Person`类定义了`hello()`方法，并且它的子类`Student`也覆写了`hello()`方法，那么，从`Person.class`获取的`Method`，作用于`Student`实例时，调用的方法到底是哪个？

```java
public class Main {
    public static void main(String[] args) throws Exception {
        // 获取Person的hello方法:
        Method h = Person.class.getMethod("hello");
        // 对Student实例调用hello方法:
        h.invoke(new Student());
    }
}

class Person {
    public void hello() {
        System.out.println("Person:hello");
    }
}

class Student extends Person {
    public void hello() {
        System.out.println("Student:hello");
    }
}
```

Output:

```
Student:hello
```

运行上述代码，发现打印出的是`Student:hello`，因此，使用反射调用方法时，仍然遵循多态原则：即总是调用实际类型的覆写方法（如果存在）。上述的反射代码：

```java
Method m = Person.class.getMethod("hello");
m.invoke(new Student());
```

实际上相当于：

```java
Person p = new Student();
p.hello();
```
