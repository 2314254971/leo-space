## JVM

![img](https://static001.geekbang.org/infoq/c6/c602f57ea9297f50bbc265f1821d6263.png)

参考：[大白话带你认识 JVM | JavaGuide](https://javaguide.cn/java/jvm/jvm-intro.html#二、类加载器的介绍)

详细信息：[Java JVM 运行机制及基本原理 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/25713880)

本地方法栈是在 程序调用 或 JVM调用 **本地方法接口（Native）**时候启用。

本地方法都不是使用Java语言编写的，它们可能由C或其他语言编写，本地方法也不由JVM去运行，所以本地方法的运行不受JVM管理。





创建对象五种方式：new、newinstance、clone、反序列化、反射

## 反射与注解

### 反射

![](C:\Users\23142\Desktop\java笔记\VeryCapture_20240904233058.jpg)

一个类只有一个Class对象

类被加载时会在堆的方法区生成（仅有）一个Class对象

上图中的三个输出全都是同一个值

利用反射可以获取类的属性与方法

例如获取类的方法并执行：

①找到类：`Class cl = Class.forName("java.util.Date");`//获取类信息
②创建对象(要求这个类中含有空参数的构造方法)：`Object obj = cl.newInstence();` 此处obj可以是Object类型（所有类的根）也可以是原本的类型（使用强制性转换）。另外此处默认调用了无参构造函数。
③根据名称和参数找方法：`Method method1 = cl.getMethod("getTime");`//如果没有参数不用写第二个参数如果有参数的如：`Method method2 = cl.getMethod("setTime",long.class);`
④在某个对象上面执行方法，`Object result = method1.invoke(obj);//如果有参数的Object result = method2.invoke(obj,21317362721);`
执行的方如果有返回值，将返回值返回，否则返回null。

原文链接：https://blog.csdn.net/ZhoumingCSDN/article/details/79972279

上述例子中，获取方法最好加上参数类别，因为某些方法会被重载。

又比如获取构造器：

```java
import org.junit.Test;
 
import java.lang.reflect.Constructor;
 
public class Test2Constructor {
    @Test
    public void testGetConstructors() throws Exception {
        //1.反射第一步：必须得到这个类的Class对象
        Class c = Cat.class;
        //2.获取某个构造器：无参构造器
        Constructor constructor = c.getDeclaredConstructor();
        System.out.println(constructor.getName() + "---->" +
                constructor.getParameterCount());
        //3.获取某个构造器：有参构造器
        Constructor constructor2 = c.getDeclaredConstructor(String.class,int.class);
        System.out.println(constructor2.getName() + "---->" +
                constructor2.getParameterCount());
    }
}
```

#### 修改/访问私有成员

通过反射获取的成员仍带有权限

若想要突破权限进行修改等操作需要设置属性权限检测是否开启

使用**setAccessible**方法进行修改

[setAccessible(true)用法及意义-CSDN博客](https://blog.csdn.net/buyaoshuohua1/article/details/104175188)

#### 反射获取泛型

![](C:\Users\23142\Desktop\java笔记\VeryCapture_20241007233019.jpg)

### 注解

（个人理解：注解就是一个标记，真正核心在于注解处理器，对注解处进行处理）



用途举例：使用注解向bean注入数据（就是返回一个带有注入数据的对象，可以看成new完之后赋值了）



### 注解处理器

#### 1.继承AbstractProcessor类并注册



#### 2.利用JDK动态代理（或者其他代理，如cglib）

例子：打印执行时间注解

1)定义注解LogExecutionTime2

```java
package com.example;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME) // 注解在运行时保留
@Target(ElementType.METHOD)         // 注解应用于方法
public @interface LogExecutionTime2 {

}
```

2)定义注解处理器，结合动态代理

首先需要实现`InvocationHandler`接口

```java
package com.example.myAnnoProcessor;
import com.example.LogExecutionTime2;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.logging.Logger;

public class ExecutionTimeLogger implements InvocationHandler {
    private static final Logger LOGGER = Logger.getLogger(ExecutionTimeLogger.class.getName());
    private final Object target;

    public ExecutionTimeLogger(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        if (method.isAnnotationPresent(LogExecutionTime2.class)) {
            long startTime = System.nanoTime();
            Object result = method.invoke(target, args);
            long endTime = System.nanoTime();
            long duration = endTime - startTime;
            LOGGER.info(method.getName() + " executed in " + duration + " nanoseconds");
            return result;
        } else {
            return method.invoke(target, args);
        }
    }

    public static Object createProxy(Object target) {
        return Proxy.newProxyInstance(
                target.getClass().getClassLoader(),
                target.getClass().getInterfaces(),
                new ExecutionTimeLogger(target)
        );
    }
}
```

3)在目标类上使用注解

这里需要注意：JDK动态代理只能代理有接口的类

##### JDK动态代理机制

这是由JDK机制决定的，`Proxy.newProxyInstance`返回一个实现接口并继承基础proxy的$proxy0

![](C:\Users\23142\Desktop\java笔记\VeryCapture_20241016230734.jpg)

java不支持多继承，每个动态代理类都已经继承了proxy，

代理类要想调用目标类的方法，只能通过实现接口形式了

（动态代理其实类似于python装饰器，对原功能进行拦截并拓展增强）

因此需要先定义接口，再定义目标类实现接口

```java
//接口
package com.example;
public interface MyService {
    void performTask();
}
//实现
package com.example;
import com.example.LogExecutionTime2;
public class MyServiceImpl implements MyService {
    @LogExecutionTime2
    public void performTask() {
        // 模拟一些工作
        try {
            Thread.sleep(100); // 休眠100毫秒以模拟任务执行时间
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

4.将处理器关联目标类

实际上也就是为目标类创建代理

```java
MyService myService = new MyServiceImpl();
MyService proxyService = (MyService)ExecutionTimeLogger.createProxy(myService);//创建代理，返回值转为接口类
proxyService.performTask(); // 这将输出方法的执行时间（调用接口处理器）
```



#### 3.利用切面Aspect

定义注解

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LogExecutionTime {}
```

创建切面处理注解

```java
@Aspect
@Component
public class PerformanceAspect {

    @Around("@annotation(LogExecutionTime)")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();

        Object proceed = joinPoint.proceed();

        long executionTime = System.currentTimeMillis() - start;
        System.out.println(joinPoint.getSignature() + " executed in " + executionTime + "ms");
        return proceed;
    }
}
```

在这个切面中， @Around注解声明了一个环绕通知，它将围绕那些标有 @LogExecutionTime注解的方法执行。

当调用一个标记了 @LogExecutionTime的方法时，切面的logExecutionTime方法会被执行，记录方法的执行时间。

参考：

[Spring注解是如何实现的？万字详解 - 架构师技术栈 - SegmentFault 思否](https://segmentfault.com/a/1190000044576598)





### Object类

该类是所有类的父类，里面包含一些方法，可以获取类的基本信息

### Class类

![](C:\Users\23142\Desktop\java笔记\VeryCapture_20240904234149.jpg)



![](C:\Users\23142\Desktop\java笔记\VeryCapture_20240904234318.jpg)

![](C:\Users\23142\Desktop\java笔记\VeryCapture_20241007230910.jpg)



### java内存

![](C:\Users\23142\Desktop\java笔记\VeryCapture_20240905232616.jpg)

方法区是被所有线程共享。
所有字段和方法字节码，以及一些特殊方法如构造函数，接口代码也在此定义。
简单说，所有定义的方法的信息都保存在该区域，此区属于共享区间。

**问题：方法区和堆是独立的吗？**

解答：？？？？？

（暂时先理解为特殊的堆）

#### 类的加载

![](C:\Users\23142\Desktop\java笔记\VeryCapture_20240905233119.jpg)

![](C:\Users\23142\Desktop\java笔记\VeryCapture_20240905233659.jpg)

##### 类的加载器

![](C:\Users\23142\Desktop\java笔记\VeryCapture_20240911234510.jpg)

##### 双亲委派机制

保护安全性，当定义了与系统同名包时，类加载器默认调用系统包。

#### 类的链接



#### 类的初始化

![](C:\Users\23142\Desktop\java笔记\VeryCapture_20240911232457.jpg)

注意定义**对象数组**并不会触发初始化，对象数组是开辟了内存但<font color=red>**没有执行初始化!!!**</font>

比如

```java
Son[] array=new Son[5];//并不会输出hello

class Son{
 static{
 	System.out.println("hello");
 }
}
```





### 静态块与静态成员对象

Java中的静态变量和静态代码块是在类加载的时候就执行的，

实例化对象时，先声明并实例化变量再执行构造函数。

如果子类继承父类，则先执行父类的静态变量和静态代码块，再执行子类的静态变量和静态代码块，然后再执行父类和子类**非静态代码块**和**构造函数**。

 最后给出执行步骤：

1、父类静态变量和静态代码块（先声明的先执行）；

2、子类静态变量和静态代码块（先声明的先执行）；

3、父类的变量和代码块（先声明的先执行）；

4、父类的构造函数；

5、子类的变量和代码块（先声明的先执行）；

6、子类的构造函数。

示例代码如下：

![](C:\Users\23142\Desktop\java笔记\VeryCapture_20240908113838.jpg)

图中代码输出m为100，因为按照先后顺序就是先执行m=300，再执行m=100。

理解：在类加载时会将静态代码块和类变量进行合并（见[类的加载](#类的加载)）

![](C:\Users\23142\Desktop\java笔记\VeryCapture_20240908114815.jpg)

分析：

在加载阶段，先确定对象的属性。

然后在链接阶段，静态变量被初始化并赋予了一个初始值（可能是0）。

最后，在初始化阶段。接着开始按顺序执行代码块，对m进行赋值操作。

也就是说上述代码等同于执行下述语句：

```java
//链接阶段
static int m=0;
//初始化阶段
<clinit>(){
    m=300;
    m=100;
}
```



## 动态代理

InvocationHandler

Proxy

主要实现InvocationHandler接口和调用Proxy的`newProxyInstance`方法



参考：[Java动态代理InvocationHandler和Proxy学习笔记-CSDN博客](https://blog.csdn.net/yaomingyang/article/details/80981004)



## AOP



## 拦截器