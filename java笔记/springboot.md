## Bean和IOC

- bean 是对象，一个或者多个不限定
- bean 由 Spring 中一个叫 IoC 的东西管理的
- 我们的应用程序由一个个 bean 构成

控制反转英文全称：Inversion of Control，简称就是 IoC。控制反转通过依赖注入（DI）方式实现对象之间的松耦合关系。程序运行时，依赖对象由辅助程序动态生成并注入到被依赖对象中，动态绑定两者的使用关系。Spring IoC 容器就是这样的辅助程序，它负责对象的生成和依赖的注入，然后再交由我们使用。

在 Spring 中，你不需要自己创建对象，你只需要告诉 Spring，哪些类我需要创建出对象，然后在启动项目的时候 Spring 就会自动帮你创建出该对象，并且只存在一个类的实例。这个类的实例在 Spring 中被称为 Bean。而这种模式，我们称之为“单例模式”。也就是一个类只有一个实例的意思。

注解配置

| 声明        | 含义                         |
| ----------- | ---------------------------- |
| @Component  | 当前类是组件，没有明确的意思 |
| @Service    | 当前类在业务逻辑层使用       |
| @Repository | 当前类在数据访问层           |
| @Controller | 当前类在展示层（MVC）使用    |

注入注解：

| 声明       | 含义                                                     |
| ---------- | -------------------------------------------------------- |
| @Autowired | 根据 Bean 的 Class 类型来自动装配                        |
| @Inject    | 翻译为“注入”最易懂得注入注解                             |
| @Resource  | 翻译为“资源”，根据 Bean 得属性名称（id 或 name）自动装配 |



这就是 IOC
在 Spring 中，类的实例化、依赖的实例化、依赖的传入都交由 Spring Bean 容器控制，而不是用 new 方式实例化对象、通过非构造函数方法传入依赖等常规方式。实质的控制权已经交由程序管理，而不是程序员管理，所以叫控制反转。



bean注解

[【Java基础】Spring 中 Bean 的理解与使用_java bean-CSDN博客](https://blog.csdn.net/yuxiangdeming/article/details/122876550)



### @Component

通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。



### @Controller 

对应 Spring MVC 控制层，主要用于接受用户请求并调用 Service 层返回数据给前端页面。

### @RestController

`@RestController`注解是`@Controller`和`@ResponseBody`的合集,表示这是个控制器 bean,并且是将函数的返回值直接填入 HTTP 响应体中,是 REST 风格的控制器。



### @Autowired

它可以对类成员变量、方法及构造函数进行标注，让 spring 完成 bean 自动装配的工作。

默认是按照类去匹配，配合 @Qualifier 指定按照名称去装配 bean。

示例：

向某个类注入一个接口，接口有2个实现类。 

默认情况是@Autowired标签会自动加载其中的一个实现类，然后使用这个实现类（标签默认是按照类型来加载的)。

需求：需要加载**特定的**某一个实现类。

第一步：将实现类注册为一个Bean(@Service、@Repository标签都可以将类注册为bean）

第二步：使用@Qualifier("beanId")标签将bean的Id作为参数使用，加载特定的类。Id一般默认是bean名称首字母小写

```java
@Service
public class AaaService implements IChangePassword {
 
    @Override
    public void changePassword(String username, String password) {
    }
 
}
 
@Service
public class BbbService implements IChangePassword {
 
    @Override
    public void changePassword(String username, String password) {
    }
 
}
 
public class AccountController extends BaseController {
    @Autowired
    @Qualifier("aaaService")
    private IChangePassword aaaService;
 
        @Autowired
    @Qualifier("bbbService")
    private IChangePassword bbbService;
}
```



### @Bean

导入第三方包里面的注解

### @Value

可以使用 `@Value` 向 Bean 注入属性值。它与构造函数、Setter 和字段注入兼容。



@Import

@Import(要导入到容器中的组件)；

```java
@Import({Color.class, MyImportSelector.class, MyImportBeanDefinitionRegistrar.class})
public class MainConfig2 {
	@Scope("prototype")
	@Bean("person")
	public Person person() {
		System.out.println("我是Person");
		return new Person("素小暖",25);
	}
}
```

参考：[Spring 核心注解 - spring 中文网 (springdoc.cn)](https://springdoc.cn/spring-core-annotations/)



## 注解

**注解的本质是一个 interface 接口 , 注解接口默认继承了 java.lang.annotation.Annotation 接口 ;**

```java
public interface Annotation extends java.lang.annotation.Annotation {
}

```

1.注解的本质是接口 , 接口中可以定义 常量 和 方法 ;

2.在注解中定义 接口方法 , 就是 注解的属性 ;

```java
public @interface Annotation {
    public abstract String path();//注解属性
}

//使用格式
@注解名称(属性名称 = 属性值)
//示例
@Annotation(path = "")
Student(String name, int age){
}
```

注解属性 ( 接口方法 ) 返回值类型要求 :

① 基本数据类型 : byte , short , int , long , float , double , char , boolean ;

② 字符串类型 : String ;

③ 枚举类型 : enum ;

④ 注解类型 ;

⑤ 以上类型的数组形式 ;

### 默认值

定义 注解属性 时 , 可以 使用 default 关键字 指定属性默认值 

```java
int intValue() default 88;
```

如果 注解属性 指定了默认值 , 在使用注解时 , 可以选择 不为该属性赋值 ( 此时使用默认属性值 ) , 也可以进行赋值 ( 指定一个新的属性值 ) ;

如果 注解属性 没有指定默认值 , 则使用 注解 时 , 必须为其指定一个默认值 , 否则编译时报错 ;

特殊情况：

如果 注解属性 **名称是 value** , 并且 注解中**只有 1 个**属性 , 那么在使用 注解 为 注解属性 赋值时 , 可以省略注解名称 , 直接传入 注解属性值 ;

```java
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
@Retention(RetentionPolicy.SOURCE)
public @interface SuppressWarnings {
    String[] value();
}
//使用
@SuppressWarnings("all")
@Override
public String toString() {
    return super.toString();
}
```

### 多个注解执行顺序

1.通过在注解实现类里面使用@Order注解来指定优先级。
优先级逻辑是：**优先级值越小，优先级越大；优先级值越大，优先级越小。**

不设置优先级值。会有一个[默认值](https://so.csdn.net/so/search?q=默认值&spm=1001.2101.3001.7020)：2147483647，也就是最小优先级。

2.优先级相同情况下，注解的执行并不是按照写注解的先后顺序来的。

而是按照注解的**名字字符的顺序**来的。字符对应的编码小，相当于优先级值小，优先级大，先执行

比如名字为A的注解比B先执行

```java
@Aspect
@Order(1)
@Component
public class Test1Aspect {
    //TODO
}
```

参考：[关于注解执行优先级、同优先级情况下执行顺序_自定义注解生效优先级-CSDN博客](https://blog.csdn.net/weixin_43975276/article/details/132540837)



## 自定义注解

注解就是一种标志，单独使用注解，就相当于在类、方法、参数和包上加上一个装饰，什么功能也没有，仅仅是一个标志，然后这个标志可以加上一些自己定义的参数。就像下面这样，创建一个@interface的注解，然后就可以使用这个注解了，加在我们需要装饰的方法上,但是什么功能也没有

（类似于python装饰器）

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface XinLinLog {
    String value() default"";
}
//格式
public @interface 注解名{注解体}
```

1.创建一个注解的基本元素
修饰符
访问修饰符必须为public,不写默认为pubic；
关键字
关键字为@interface；
注解名称
注解名称为自定义注解的名称，例如上面的XinLinLog 就是注解名称
注解类型元素
注解类型元素是注解中内容，根据需要标志参数，例如上面的注解的value；

参数成员

只能用基本类型 byte,short,char,int,long,float,double,boolean 八种基本数据类型 和 String,Enum,Class,annotations 等数据类型,以及这一些类型的数组.例如,String value();这里的参数成员就为String;



2.根据注解的参数个数分类：

**1)** 标记注解，一个没有成员的Annotation类型被称为标记注解，这种类型仅仅使用自身的存在与否来为我们提供信息，比如常见的@Override

**2)** 单值注解

**3)** 完整注解

根据注解使用的方法和用途分类：

**1)** JDK内置系统注解

**2)** 元注解

**3)** 自定义注解

3.元注解（@Target、@Retention、@Inherited、@Documented）
我们上面的创建的注解XinLinLog上面还有几个注解（@Target、@Retention、@Inherited、@Documented），这四个注解就是元注解，元注解的作用就是负责注解其他注解。Java5.0定义了4个标准的元注解类型，它们被用来提供对其它 注解类型作标志操作（可以理解为最小的注解，基础注解）

**@Target**描述了注解修饰的对象范围，取值在java.lang.annotation.ElementType定义

- METHOD：用于描述方法
- PACKAGE：用于描述包
- PARAMETER：用于描述方法变量
- TYPE：用于描述类、接口或enum类型

**@Retention** 表示注解保留时间长短。取值在java.lang.annotation.RetentionPolicy中

- SOURCE：在源文件中有效，编译过程中会被忽略
- CLASS：随源文件一起编译在class文件中，运行时忽略
- RUNTIME：在运行时有效

只有定义为RetentionPolicy.RUNTIME时，我们才能通过注解反射获取到注解。

***@Document***

**@Inhrited**

参考：

[自定义注解(Annotation)详解 - 角刀牛Java - 博客园 (cnblogs.com)](https://www.cnblogs.com/jiaodaoniujava/p/18256650)

## 拦截器

前提：

定义一个LoginRequired注解

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LoginRequired {

}
```

定义两个简单的接口，访问sourceA，sourceB资源

```java
@RestController
public class IndexController {
    @GetMapping("/sourceA")
    public String sourceA() {
        return "你正在访问sourceA资源";
    }
    @LoginRequired
    @GetMapping("/sourceB")
    public String sourceB() {
        return "你正在访问sourceB资源";
    }
}
```

正常情况是直接进入方法

加入拦截器后可根据类型进行修改行为

用法：

1.spring的HandlerInterceptor 类先实现拦截器

```java
public class SourceAccessInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    System.out.println("进入拦截器了");
        
    // 反射获取方法上的LoginRequred注解
    HandlerMethod handlerMethod = (HandlerMethod) handler;
    LoginRequired loginRequired = handlerMethod.getMethod().getAnnotation(LoginRequired.class);
    if (loginRequired == null) {
        return true;
    }
    // 有LoginRequired注解说明需要登录，提示用户登录
    response.setContentType("application/json; charset=utf-8");
    response.getWriter().print("你访问的资源需要登录");
        
    return false;      
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
    }
}
```

2.spring类WebMvcConfigurer，创建配置类把拦截器添加到拦截器链中

```java
@Configuration
public class InterceptorTrainConfigurer implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new SourceAccessInterceptor()).addPathPatterns("/**");
    }
}
```

加入上述代码后，访问b资源会提示需要登陆

## AOP

[谈谈 Java 中自定义注解及使用场景 - Java 小词典 - SegmentFault 思否](https://segmentfault.com/a/1190000023881879)



高级参考示例：[springboot项目中自定义注解的使用总结、java自定义注解实战（常用注解DEMO）_springboot自定义注解加载字段上怎么使用-CSDN博客](https://blog.csdn.net/qq_21187515/article/details/109643130)





AOP （Aspect Orient Programming）,直译过来就是 面向切面编程,AOP 是一种编程思想，是面向对象编程（OOP）的一种补充。

AOP可以**拦截指定的方法并且对方法增强**，而且无需侵入到业务代码中，使业务与非业务处理逻辑分离，比如Spring的事务，通过事务的注解配置，Spring会自动在业务方法中开启、提交业务，并且在业务处理失败时，执行相应的回滚策略。

简单的说，AOP 的作用就是保证开发者在不修改源代码的前提下，为系统中的业务组件添加某种通用功能。

![](.\daxhzm5tym.jpeg)

应用场景：

- 日志记录
- 事务管理
- 权限验证
- 性能监测

技术要点

![](.\c0416fe50dae41720cb2347077df5a3f.png)

- Joinpoint：程序的执行点，如方法的执行或者异常的处理。
- Pointcut：一个用来匹配`joinpoint`的断言或者表达式。
- Advice：与一个`pointcut`关联，并在匹配点运行。
- 



advice通知有五种类型，分别是：

![](.\232c095b934145dc667e3fd8e26692d3.png)

- Before advice：在`join point`之前执行的`advice`，不能阻止程序的继续运行。
- After returning advice：在`join point`完成之后执行的`advice`。
- After throwing advice：在执行的方法抛出异常之后执行。
- After advice：在执行的`join point`退出之后执行不论正常退出或者抛出了异常。
- Around advice：可以在方法调用之前或之后执行自定义行为，并且还可以选择继续执行`join point`或者执行另外的方法。

执行顺序：**@Around ->@Before->主方法体->@Around中pjp.proceed()->@After->@AfterReturning**

使用举例：

```java
@Aspect
public class EmployeeCRUDAspect {
      
    @Before("execution(* EmployeeManager.getEmployeeById(..))")         //point-cut expression
    public void logBeforeV1(JoinPoint joinPoint)
    {
        System.out.println("EmployeeCRUDAspect.logBeforeV1() : " + joinPoint.getSignature().getName());
    }
}
```

面向切面编程首先需要在类上加`@Aspect`注解表名这是一个`AOP`管理的类，然后在方法上加上`point-cut`表达式用来匹配`joint-point`方法。

```java
@Component
public class EmployeeManager
{
    public EmployeeDTO getEmployeeById(Integer employeeId) {
        System.out.println("Method getEmployeeById() called");
        return new EmployeeDTO();
    }
}
```

在上面的例子中，`logBeforeV1`方法会在`getEmployeeById`方法执行之前执行。

参考：

[Spring AOP使用指南，详细了解AOP相关注解-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1430631)

纵观AOP编程，程序员只需要参与三个部分：

1、定义普通业务组件

2、定义切入点，一个切入点可能横切多个业务组件

3、定义增强处理，增强处理就是在AOP框架为普通业务组件织入的处理动作

### @Pointcut

该注解后面常接切点表达式用以筛选

通常注解在一个空的函数上

如：

```java
@Aspect
public class AspectTest2 {

    @Pointcut("within(C1)") //@1
    public void pc() {
    }

    @Before("pc()") //@2
    public void beforeAdvice(JoinPoint joinpoint) {
        System.out.println(joinpoint);
    }

}
```



### AOP注解失效



参考：[AOP 与 注解的那些事儿~ - 爱撒谎的男孩 - 博客园 (cnblogs.com)](https://www.cnblogs.com/Chenjiabing/p/13984651.html)



### 切点表达式

用于匹配作用对象



#### execution表达式

可以匹配方法、类、包。

```java
execution(<修饰符模式>?<返回类型模式><方法名模式>(<参数模式>)<异常模式>?) 
```

 除了返回类型模式、方法名模式和参数模式外，其它项都是可选的。

![](C:\Users\leo\Desktop\java笔记\b14924c2-d1ee-4195-8435-14ad7c2b6159.webp)

[AOP(execution表达式)_根据类名后缀进行切面-CSDN博客](https://blog.csdn.net/somilong/article/details/74568223)

#### within

匹配指定类型。匹配指定类的任意方法，不能匹配接口。

表达式模式：

```java
within(declaring-type)

// 匹配service包的类
within(com.xyz.service.*)

// 匹配service包及其子包的类
within(com.xyz.service..*)

// 匹配AccountServiceImpl类
within(com.xyz.service.AccountServiceImpl)
```

#### **target**

匹配目标对象实例的类型，匹配 AOP 被代理对象的类型。

#### @annotation

匹配方法是否含有注解。当方法上使用了注解，该方法会被匹配，在接口方法上使用注解不匹配。

等等，还有多种表达式。



要使切点的匹配性能达到最佳，编写表达式时，应该尽可能缩小匹配范围，切点表达式分为三大类：

类型表达式：匹配某个特定切入点，如 execution
作用域表达式：匹配某组切入点，如 within
上下文表达式：基于上下文匹配某些切入点，如 this、target 和 @annotation

#### 表达式组合

使用 `&&`、`||` 和 `!` 来组合多个切点表达式，表示多个表达式“与”、“或”和“非”的逻辑关系。
这可以用来组合多种类型的表达式，来提升匹配效率。

```java
// 匹配doExecution()切点表达式并且参数第一个为Account类型的方法
@Before("doExecution() && args(account,..)")
public void validateAccount(Account account) {
    // 自定义逻辑
}
```

参考：[第15章-Spring AOP切点表达式（Pointcut）详解-CSDN博客](https://blog.csdn.net/weixin_43793874/article/details/124753521)





[Spring AOP 详细深入讲解+代码示例-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/1357215)



## enum类

可以把enum看成是一个普通的class，它们都**可以定义一些属性和方法**，不同之处是：enum不能使用extends 关键字继承其它类，因为enum已经继承了java.lang.enum（java是单一继承）

```java
public class Test {
    public enum Color {
        RED("红色", 1), GREEN("绿色", 2), BLANK("白色", 3), YELLO("黄色", 4);
        // 成员变量
        private String name;
        private int index;
 
        // 构造方法
        private Color(String name, int index) {
            this.name = name;
            this.index = index;
        }
 
        // 覆盖方法
        @Override
        public String toString() {
            return this.index + "_" + this.name;
        }
    }
 
    public static void main(String[] args) {
        System.out.println(Color.RED.toString());
    }
}
```





## XML



## get与post前后端对应传参

在客户机和服务器之间进行请求-响应时，两种最常被用到的方法是：GET 和 POST。

- **GET** - 从指定的资源请求数据。
- **POST** - 向指定的资源提交要被处理的数据

![img](https://www.runoob.com/wp-content/uploads/2013/07/get-post.png)

GET 提交参数**一般显示在 URL 上**，POST 通过表单提交**不会显示在 URL 上**，POST 更具隐蔽性

> Get 将表单中数据的按照 variable=value 的形式，添加到 action 所指向的 URL 后面，并且两者使用“?”连接，而各个变量之间使用“&”连接；Post 是将表单中的数据放在 form 的数据体中，按照变量和值相对应的方式，传递到 action 所指向 URL。

Get 是不安全的，因为在传输过程，数据被放在请求的 URL 中，而如今现有的很多服务器、代理服务器或者用户代理都会将请求URL记录到日志文件中，然后放在某个地方，这样就可能会有一些隐私的信息被第三方看到。另外，用户也可以在浏览器上直接看到提交的数据，一些系统内部消息将会一同显示在用户面前。Post 的所有操作对用户来说都是不可见的。

注意：Get 传输的数据量小，这主要是因为受 URL 长度限制；而 Post 可以传输大量的数据，所以在上传文件只能使用 Post。

使用 Post 传输的数据，可以通过设置编码的方式正确转化中文；而 Get 传输的数据却没有变化。

在前后端传参时一般是：

```js
//传输对象，通过data属性
return request({
    url: '/manage/newFolder',
    method: 'post',
    data: treeNode
  })

class MYDTO{//通过data属性将前端对象转为后端对象
    STring id;
    STring name;
    //其他属性
}

@PostMapping("/newFolder")
public R<Object> foldOperate(@RequestBody MYTDTO dto){
   //todo
}
```

```js
//若是通过params形式则会体现在url上，而且后端接收对象必须有这个对象的成员要不然数据会被丢弃
return request({
    url: '/manage/newFolder',
    method: 'post',
    params: {
       node: treeNode
    }
  })

//后端
class MYDTO{//这种能正常获得前端的数据
    Map<String,Object> node;
	//其他属性
}
class MYDTO{//这种能无法正常获得
    STring id;
    STring name;
    //其他属性
}
@PostMapping("/newFolder")
public R<Object> foldOperate(@RequestBody MYTDTO dto){
   //todo
}
```

不过使用param时若后端使用对象接收，前端可以一条一条属性列到参数表，此时能正常转换

注意：

使用data属性时不要转为字符串，要不然会转换失败





[axios 传递参数的方式(data 与 params 的区别)_axios data-CSDN博客](https://blog.csdn.net/qq_41499782/article/details/118916901)



## 报错及解决方案

### Required request body is missing

1.@GetMapping，Spring Get请求不能使用@RequestBody

2.Post却用了Get请求
