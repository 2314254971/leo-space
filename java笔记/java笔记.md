# JAVA笔记

## java的个人认识

类似于python

都不是直接编译为可执行文件

都是通过解释器执行代码

java---jvm    python---python解释器

总结，可跨平台，移植性好，但是执行效率可能不高

## java开发包以及环境配置

参考链接

[Java 的简要介绍及开发环境的搭建（超级详细）_java环境-CSDN博客](https://blog.csdn.net/weixin_62651706/article/details/130460522)

总结：

1.安装jdk（java开发工具包）【并完成环境变量配置（有的可能自动配置好了）】

2.安装开发集成工具（如idea），配置jdk路径

## JVM了解

参考：[这一次，彻底解决Java的值传递和引用传递 - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000016773324)

jvm结构：

![](C:\Users\leo\Desktop\java笔记\453148037-5bcebab0e8aed.png)



本地方法是指使用非Java语言（如C、C++、汇编等）编写的代码，它可以直接在Java程序中调用和执行。Java的本地方法机制提供了一种与底层系统和库进行交互的途径，使得Java程序可以调用本地代码来完成一些特定的任务，比如调用操作系统的API、访问硬件设备等。本文介绍了Java中的本地方法（Native Methods）的概念和使用方法。本地方法允许开发者使用非Java语言编写的代码与Java程序进行集成，以便进行底层系统和库的交互。

## java手动运行

![](C:\Users\leo\Desktop\java笔记\e2a7be4213e539a3f2baf97177e45e60.jpeg)

步骤：

1.执行javac 指令将java文件编译为class文件

2.执行java指令运行class代码

更复杂的情况一般直接用集成工具，或者百度

## java多态

多态存在的三个必要条件
1.继承
2.重写
3.父类引用指向子类对象：Parent p = new Child();

多态的实现方式
方式一：重写

方式二：接口

方式三：抽象类和抽象方法



## java语法

### 包名与导入

java通过package关键字定义包名

Java 使用包（package）这种机制是为了防止命名冲突，访问控制，提供搜索和定位类（class）、接口、枚举（enumerations）和注释（annotation）等。

例如一个Something.java 文件它的内容

```java
package net.java.util;
public class Something{
   ...
}
```

那么它的路径应该是 net/java/util/Something.java 这样保存的。



**import** 关键字用于导入其他类或包中定义的类型，以便在当前源文件中使用这些类型

import 语句位于 package 语句之后：

```java
// 第一行非注释行是 package 语句
package com.example;
 
// import 语句引入其他包中的类
import java.util.ArrayList;
import java.util.List;
 
// 类的定义
public class MyClass {
    // 类的成员和方法
}
```

1.如果在一个包中，一个类想要使用本包中的另一个类，那么该包名可以省略。

2.可以使用 import语句来引入一个特定的类：

```java
import com.runoob.MyClass;
```

3.可以使用通配符 * 来引入整个包或包的子包：

```c++
import com.runoob.mypackage.*;
```

4.static import和import其中一个不一致的地方就是static import导入的是静态成员，而import导入的是类或接口类型。

```java
package com.assignment.test;
public class staticFieldsClass {
	static int staticNoPublicField = 0; 
	public static int staticField = 1;
    public static void staticFunction(){}
}
//另一个包
//直接导入具体的静态变量、常量、方法方法，注意导入方法直接写方法名不需要括号。
import static com.assignment.test.StaticFieldsClass.staticField;
import static com.assignment.test.StaticFieldsClass.staticFunction;
```

注：类似于python，java按需导入也会提高效率

参考：

[Java 包(package) | 菜鸟教程 (runoob.com)](https://www.runoob.com/java/java-package.html)

### 包与包之间的访问

同一个包的类之间直接使用相应的类名进行访问，但不同的包之下的类之间必须使用完整的类名进行访问，即使用完整包名+类名的方式进行访问使用。值得注意的是，虽然子包可以表明子包是父包中的一个模块，但是如果父包中的类调用子包中的类进行对象的创建的时候，仍然需要使用子包的全名，而不能省略父包的部分。

```java
package lee;
public class HelloTest
{
	lee.sub.Apple a = new lee.sub.Apple();
}
/*
不能省略父包的部分，使用sub.Apple a = new sub.Apple();进行创建
*/

```

原文链接：https://blog.csdn.net/qq_49086305/article/details/117994677

### 源文件声明规则

- 一个源文件中<font color=red>**只能有一个**</font> public 类

- 一个源文件可以有多个非 public 类

- 源文件的名称应该**和 public 类的类名保持一致**。例如：源文件中 public 类的类名是 Employee，那么源文件应该命名为Employee.java。文件中可以只有非public类，如果只有一个非public类，此类可以跟文件名不同。

  为什么每个java文件只能存在一个public类：[【java扫盲】一个“.java“源文件中 只能有一个public类吗，一个“.java“源文件中是否可以包括多个类（不是内部类）？_在一个java源程序文件中,可以有多个public类-CSDN博客](https://blog.csdn.net/huaishitou/article/details/120741673)

  每个编译单元(文件)都只有一个 public 类。因为每个编译单元都只能有一个公共接口，用 public 类来表现。该接口可以按照要求包含众多的支持包访问权限的类。

- 如果一个类定义在某个包中，那么 package 语句应该在源文件的首行。

- 如果源文件包含 import 语句，那么应该放在 package 语句和类定义之间。如果没有 package 语句，那么 import 语句应该在源文件中最前面。

- import 语句和 package 语句对源文件中定义的所有类都有效。在同一源文件中，不能给不同的类不同的包声明。

### 混合运算

**整型、实型（常量）、字符型数据可以混合运算。运算中，不同类型的数据先转化为同一类型，然后进行运算。**

转换等级如下：

```
低  ------------------------------------>  高

byte,short,char—> int —> long—> float —> double 
```

数据类型转换必须满足如下规则：

- 1. 不能对boolean类型进行类型转换。

- 2. 不能把对象类型转换成不相关类的对象。

- 3. 在把容量大的类型转换为容量小的类型时必须使用强制类型转换。

- 4. 转换过程中可能导致溢出或损失精度，例如：

     ```java
     int i =128;   
     byte b = (byte)i;//byte 类型是 8 位，最大值为127，所以当 int 强制转换为 byte 类型时，值 128 时候就会导致溢出。
     ```

  5.  浮点数到整数的转换是通过舍弃小数得到，而不是四舍五入，例如：

     ```java
     (int)23.7 == 23;        
     (int)-45.89f == -45
     ```

1.自动类型转换:

转换前的数据类型的位数要低于转换后的数据类型，例如: short数据类型的位数为16位，就可以自动转换位数为32的int类型

```java
public class ZiDongLeiZhuan{
        public static void main(String[] args){
            char c1='a';//定义一个char类型
            int i1 = c1;//char自动类型转换为int
            System.out.println("char自动类型转换为int后的值等于"+i1);
            char c2 = 'A';//定义一个char类型
            int i2 = c2+1;//char 类型和 int 类型计算
            System.out.println("char类型和int计算后的值等于"+i2);
        }
}
```

2.强制类型转换和c++一样

### 变量命名规则

Java变量的基本命名法则：
1、以下划线、字母、<font color="red">美元符$</font>开头。
2、后面跟下划线、字母、美元符以及数字。
3、 没有长度限制（但也不能太长！）。
4、对**大小写敏感**（意思是大小写代表不同含义）

### 特殊关键字

#### final

修饰变量就是表示是常量

修饰类表示该类无法被继承

#### synchronized 

synchronized 关键字声明的方法同一时间**只能**被一个线程访问。synchronized 修饰符可以应用于四个访问修饰符。

#### transient 

序列化的对象包含被 transient 修饰的实例变量时，java 虚拟机(JVM)跳过该特定的变量。

？？？

参考：[java中的transient关键字详解_transient关键字作用-CSDN博客](https://blog.csdn.net/qq_44543508/article/details/103232007)



#### volatile 

volatile 修饰的成员变量在每次被线程访问时，都强制从共享内存中重新读取该成员变量的值。而且，当成员变量发生变化时，会强制线程将变化值回写到共享内存。这样在任何时刻，两个不同的线程总是看到某个成员变量的同一个值。

### 运算符

<<=   左移位赋值运算符  C << = 2等价于C = C << 2

\>\>=   左移位赋值运算符  C \>\>= 2等价于C = C \>\> 2

＆=	按位与赋值运算符	C＆= 2等价于C = C＆2
^ =	按位异或赋值操作符	C ^ = 2等价于C = C ^ 2
| =	按位或赋值操作符	C | = 2等价于C = C | 2

#### instanceof 运算符

该运算符用于操作对象实例，检查该对象是否是一个特定类型

```java
String name = "James";
boolean result = name instanceof String; // 由于 name 是 String 类型，所以返回真
```

如果被比较的对象兼容于右侧类型，该运算符仍然返回 true。

比如子类

### 数组的创建与使用

创建(可以使用new初始化)

```java
dataType[] arrayRefVar = new dataType[arraySize];
//或者
dataType[] arrayRefVar = {value0, value1, ..., valuek};
```

多维数组：（多维数组可以没每一个维度单独分配不同大小的空间）

```java
String[][] str = new String[3][4];
//或者
type[][] typeName = new type[typeLength1][typeLength2];
//示例
String[][] s = new String[2][];
s[0] = new String[2];//每个维度大小都不一样
s[1] = new String[3];
s[0][0] = new String("Good");
s[0][1] = new String("Luck");
s[1][0] = new String("to");
s[1][1] = new String("you");
s[1][2] = new String("!");
```



使用：

作为形参：(这一点和c++差距大，这里的数组甚至可以和对象一样使用new初始化，也<font color="red">可以调用length属性</font>)

```java
public static void printArray(int[] array) {
  for (int i = 0; i < array.length; i++) {
    System.out.print(array[i] + " ");
  }
}
```



### 循环

和c++类似：for each循环

```java
public class TestArray {
   public static void main(String[] args) {
      double[] myList = {1.9, 2.9, 3.4, 3.5}; 
      // 打印所有数组元素
      for (double element: myList) {
         System.out.println(element);
      }
   }
}
```

### 传参

Java中**只有按值传递**，没有按引用传递！

当java形参类型为基本类型时，只能做到值传递的**效果**

而形参类型为对象时（不包括String），可以做到引用传递的**效果**（只是达到这个效果，本质不是引用传参）

其本质都是值传递

原因如下：

其本质和jvm有关

![](C:\Users\leo\Desktop\java笔记\453148037-5bcebab0e8aed.png)

1.java的栈区是线程独立的，而堆区是共享的

2.对于基本类型的参数是直接压栈，相当于拷贝了一份，而对象是通过new得到的，即保存在堆上，当执行如下语句时

```java
Person p=new Person();
//相当于如下过程
Person p;//定义变量
p=new Person();//赋值
```

实际上JVM会在堆内开辟一块内存，用来存储p对象的所有内容，同时在main（）方法所在线程的栈区中创建一个引用p存储堆区中p对象的真实地址，如图：

![](C:\Users\leo\Desktop\java笔记\649263324-5bcebab0614e3_fix732.png)

也就是说p相当于对象的指针/句柄，因此对p进行压栈实际上是传递了person对象的指针/句柄

因此能达到引用传递的效果

总之：<font color=red>**java只存在值传递**</font>

参考链接：

[这一次，彻底解决Java的值传递和引用传递 - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000016773324)

### 不定数量参数

可变参数语法：

```java
public void methodName(Type... variableName) {
    // 方法体
}
//例如
public void printNumbers(int... numbers) {
    for (int num : numbers) {
        System.out.print(num + " ");
    }
    System.out.println();
}
```

注意：

1.可变参数必须是最后一个参数

这是因为可变参数会捕获所有传递的参数，导致无法确定参数的数量

2.可变参数**不是**数组。

但它是数组的语法糖，编译器会将可变参数转换为数组，因此可以像处理数组一样处理可变参数。





与c++不同又类似

```c++
template <typename T, typename... Args>
void func(const T &t, const Args& ... rest);
```

都是三个点表示多个值

```c++
int i = 0;
double d = 3.14;
string s = "str";
func(i, s, 1, d);
func(s, 1, "ghel");
func(d, s);
func("sss");
//对于上述的四个函数调用，编译器会为func实例化出四个不同的版本
void func(const int&, const string&, const int&, const double&);
void func(const string&, const int&, const string&);
void func(const double&, const string&);
void func(const string&);
```

实际应用时使用递归来处理各参数（一次性处理一个，每次处理T）

```c++
template<typename T>
ostream &print(ostream &os, const T& t)
{
  return os << t;
}
template<typename T, typename... Args>
ostream &print(ostream &os,  const T& t, const Args&... rest)//每次处理一个参数
{
  os << t << ' ';
  return print(os, rest...);
}
```

参考：[C++11：可变参数 - 简书 (jianshu.com)](https://www.jianshu.com/p/06c18ce156ff)



### 输入输出流

System.in

System.out

这两个都是对象



### JAVA异常处理

异常通常使用 **try-catch** 块来捕获并处理异常，或者在方法声明中使用 **throws** 子句声明方法可能抛出的异常。

例如：

```java
try {
    // 可能会抛出异常的代码
} catch (IOException e) {
    // 处理异常的代码
}

//或者
public void readFile() throws IOException {
    // 可能会抛出IOException的代码
}
```

Java 提供了以下关键字和类来支持异常处理：

- **try**：用于包裹可能会抛出异常的代码块。
- **catch**：用于捕获异常并处理异常的代码块。
- **finally**：用于包含无论是否发生异常都需要执行的代码块。
- **throw**：用于**手动抛出异常**。
- **throws**：用于在<font color=red>**方法声明中**</font>指定方法可能抛出的异常。（见上面示例）
- **Exception**类：是所有异常类的父类，它提供了一些方法来获取异常信息，如 **getMessage()、printStackTrace()** 等。

多重捕获

```java
try{
   // 程序代码
}catch(异常类型1 异常的变量名1){
  // 程序代码
}catch(异常类型2 异常的变量名2){
  // 程序代码
}catch(异常类型3 异常的变量名3){
  // 程序代码
}finally{//finally不是必要的
  // 程序代码
}
```

按照异常类型一个一个往下判断

#### try-with-resources

 try-with-resources是Java7中加入的，大大简化的对资源的管理，而且使用try-with-resources的代码清晰易懂

原理：当try中代码执行结束后（正常结束或者异常结束），都会调用try()括号中对象的**close()**方法来关闭资源。

类似于python的with关键字操作

写法：

```java
try(xxx[;xxxx]){//把对象创建写在括号中
   // 程序代码
}catch(异常类型1 异常的变量名1){
  // 程序代码
}
//示例：
package onlytest;
import static java.lang.System.out;

public class testAutoClose {
    public static void main(String[] args) {
        try (MyClose close = new MyClose()) {
            close.println("liuqinhou");
            throw new Throwable("exist error");
        } catch (Exception e) {
            out.println("exception exists");
            e.printStackTrace();
        } catch (Throwable throwable) {
            out.println("throwable exists");
            throwable.printStackTrace();
        }
        out.println("main thread end");
    }
}
 
class MyClose implements AutoCloseable {
    public void println(String str) {
        out.println(str + " is working...");
    } 
    @Override
    public void close() throws Exception {
        out.println("MyClose is closed now");
    }
}

//输出

liuqinhou is working...
MyClose is closed now //try中代码执行后会立马执行resource对象的close方法，如果发生了异常，再执行catch中的语句
throwable exists
main thread end
java.lang.Throwable: exist error
	at onlytest.testAutoClose.main(testAutoClose.java:10)

```

参考：[Java中try-with-resources的使用_try with resources用法-CSDN博客](https://blog.csdn.net/liuqinhou/article/details/127950993)

对多个资源操作时

```java
class RunoobTest {
    public static void main(String[] args) throws IOException{
        try (Scanner scanner = new Scanner(new File("testRead.txt"));
            PrintWriter writer = new PrintWriter(new File("testWrite.txt"))) {
            while (scanner.hasNext()) {
                writer.print(scanner.nextLine());
            }
        }
    }
}
```

1.声明多个资源，方法是使用分号 **;** 分隔各个资源

2.以相反的顺序关闭这些资源。 在本例中，PrintWriter 对象先关闭，然后 Scanner 对象关闭。

#### 自定义异常

在 Java 中你可以自定义异常。编写自己的异常类时需要记住下面的几点。

- 所有异常都必须是 Throwable 的子类。
- 如果希望写一个检查性异常类，则需要继承 Exception 类。
- 如果你想写一个运行时异常类，那么需要继承 RuntimeException 类。

参考：[Java 异常处理 | 菜鸟教程 (runoob.com)](https://www.runoob.com/java/java-exceptions.html)



## java类

java一切都写在类的内部

### 成员

接口作为成员变量

```java
 // 这是新定义的接口：Skill
public interface Skill {
    void use();
} 
```

```java
public class Hero {
    private String name; // 英雄名
    private int age;
    private Weapon weapon;
    private Skill skill;// 英雄的技能
    public Hero() {
    }

    public Hero(String name, int age, Weapon weapon, Skill skill) {
        this.name = name;
        this.age = age;
        this.weapon = weapon;
        this.skill = skill;
    }

    public void attack() {
        System.out.print("年龄为" + age + "岁的" + name +
                "正在用" + weapon.getWeaponCode() + "攻击敌方并使用了技能：");
        skill.use(); // 调用接口中的抽象方法

    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public Weapon getWeapon() {
        return weapon;
    }

    public Skill getSkill() {
        return skill;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setWeapon(Weapon weapon) {
        this.weapon = weapon;
    }

    public void setSkill(Skill skill) {
        this.skill = skill;
    }
}
```

```java
public class Demo {
    public static void main(String[] args) {
        Hero hero = new Hero();
        hero.setName("[老亚瑟]");
        hero.setAge(98);
        Weapon weapon = new Weapon("[大宝剑]");
        hero.setWeapon(weapon);

        hero.setSkill(new SkillImpl());// 写法1：使用单独定义的实现类，SkillImpl为此而生
        hero.attack();

        Skill skill2 = new Skill() {// 写法2：使用匿名内部类
            @Override
            public void use() {
                System.out.println("[回旋打击]");
            }
        };
        hero.setSkill(skill2);
        hero.attack();

        hero.setSkill(new Skill() {// 写法3：使用匿名内部类、匿名对象
            @Override
            public void use() {
                System.out.println("[誓约之盾]");
            }
        });
        hero.attack();
    }
}

/*
年龄为98岁的[老亚瑟]正在用[大宝剑]攻击敌方并使用了技能：[圣剑裁决]
年龄为98岁的[老亚瑟]正在用[大宝剑]攻击敌方并使用了技能：[回旋打击]
年龄为98岁的[老亚瑟]正在用[大宝剑]攻击敌方并使用了技能：[誓约之盾]
*/
```

对于上面的写法1，由于没有使用匿名内部类，因此需要创建一个实现类SkillImpl如下：

```java
public class SkillImpl implements Skill {
    @Override
    public void use() {
        System.out.println("[圣剑裁决]");
    }
} 
```



### 继承

![](C:\Users\leo\Desktop\java笔记\java-extends-2020-12-08.png)

1.**JAVA不支持多继承!!!**

2.子类自动拥有了基类所有成员（成员变量和方法，但构造方法不能被继承），将父类的private成员也继承下来，但是不能访问（有父类private成员的所有权，但没有使用权）；



关键字：**extends  implements**

extends 只能继承一个类。

implements 关键字可以变相的使java具有多继承的特性，使用范围为类继承接口的情况，可以同时继承多个接口（接口跟接口之间采用逗号分隔）。

```java
public class C implements A,B {
}
```



**super 关键字：**我们可以通过 super 关键字来实现对父类成员的访问，用来引用当前对象的父类。（类似于python）

```java
class Animal {
    void eat() {
        System.out.println("animal : eat");
    }
}
 
class Dog extends Animal {
    void eat() {
        System.out.println("dog : eat");
    }
    void eatTest() {
        this.eat();   // this 调用自己的方法
        super.eat();  // super 调用父类方法
    }
}
 
public class Test {
    public static void main(String[] args) {
        Animal a = new Animal();
        a.eat();
        Dog d = new Dog();
        d.eatTest();
    }
}
//输出
animal : eat
dog : eat
animal : eat
```

### 权限

java不像c++有三种权限的继承

他只有一种默认的继承方式

而且JAVA多了一种叫默认default的权限

（default就是啥也不写，不能写具体的字）

对于类来说：

class的权限修饰符只可以是public或default

　　public:类可以在任何地方被访问；

　　**default:类只可以被同一个包内部的类访问。**

对于成员来说：

![](C:\Users\leo\Desktop\java笔记\091720133406473.jpg)

自己的理解：private<default<protected<public

### 方法调用问题、是否存在虚函数概念

使用父类型引用接收子类对象，会发现调用同名方法会调用子类的方法（静态方法除外，静态方法仍是调用父类）

这是因为java里面不存在虚函数一说，普通函数成员**都**可以看作是虚函数，重写后就相当于重写了虚函数，实现了多态（区别与c++）

另外该引用**无法**调用子类独有的方法

下面是对应表：

*C++*虚函数  *== Java*普通函数

*C++*纯虚函数 *== Java*抽象函数

*C++*抽象类  *== Java*抽象类

*C++*虚基类  *== Java*接口

参考链接：[JAVA – 虚函数、抽象函数、抽象类、接口_java需要虚函数吗-CSDN博客](https://blog.csdn.net/trojanpizza/article/details/6556604)



### 静态方法、成员

调用时一般通过类名访问

```java
MyClass.myStaticMethod();
```

注意：1.静态方法中不能使用`this`关键字，因为它没有当前对象的引用。实例方法可以使用`this`来引用当前对象。

2.静态方法**在类加载时初始化**，而实例方法在对象创建时初始化。



对于静态变量可以通过类名/示例来访问

**静态变量的生命周期**：

静态变量的生命周期与类的生命周期相同。它们**在类加载时初始化**，一直存在，直到程序结束或类被卸载。静态变量的值在类加载后保持不变，因为它们是类级别的，而不是与对象实例相关联。



### 静态成员初始化问题：

先来看看c++的静态成员初始化

1.静态数据成员**不能**在类中初始化，一般在**类外**和main()函数**之前**初始化，缺省时初始化为0。

2.静态成员仍然遵循public，private，protected访问准则。

3.静态成员函数没有this指针，它不能返回非静态成员，因为除了对象会调用它外，类本身也可以调用

4.static成员的所有者是类本身，但是多个对象拥有一样的静态成员。从而在定义对象是不能通过构造函数对其进行初始化

在java里面，初始化方式如下：

1. 声明的时候初始化

   注：事实上通过观察class发现：声明时初始化，编译器会为该类提供一个默认的static块，也就是说还是通过静态块初始化

2. 在静态块 [static](https://so.csdn.net/so/search?q=static&spm=1001.2101.3001.7020) block中初始化

   ```java
   class Demo
   {
       public static int z; 
    
       static 
       {
           z = 10;  //初始化代码
       }
   }
   ```

   注意：若有多个静态块，则按照**先后顺序**进行赋值

3. 写一个私有<font color='red'>**静态**</font>方法去初始化

   ```java
   class name {
       public static varType myVar = initializeVar();
    
       private static varType initializeVar() {
            // 初始化代码
       }
   }
   ```



### 静态块和非静态代码块（普通代码块）

静态块特点：<font color=red>**随着类的加载而执行，而且只执行一次**</font>

1.代码块执行顺序：**静态代码块----->非静态代码块-------->构造函数**

示例：[Static静态代码块以及各代码块之间的执行顺序_static代码块执行顺序-CSDN博客](https://blog.csdn.net/qq_44543508/article/details/102593419)

```java
package com.gx.initializationblock;
public class Initializationblock {
    int intA;
    int intB;
    public Initializationblock() {
        System.out.println("无参构造器00000000");
    }
    public Initializationblock(int a) {
        System.out.println("一个参数的构造器");
        
    }
    {
        intA = 10;
        intB = 15;

        System.out.println("构造初始化块11111");
    }
    {
        System.out.println("构造初始化块22222");
    }
    {
    	
        System.out.println("构造初始化块33333");
    }
    //静态初始化块
    static {
        System.out.println("静态初始化块01010101");
    }
    static {
        System.out.println("静态初始化块0202020202");
    }
    public void method(){
    	{
    		System.out.println("普通初始化块");
    	}
    }
}

public class Demo1 {
    public static void main(String[] args) {
        Initializationblock initializationblock = new Initializationblock();
        initializationblock.method();
        System.out.println("------------");
        //多打印几个对象的目的是：好看出Static静态代码块只执行一次！！！
        Initializationblock initializationblock2 = new Initializationblock(); //因为静态块是在类的初始化阶段完成的，因此在创建某个类的第二个对象时，该类的静态块就不会执行了
        initializationblock2.method();
        Initializationblock initializationblock3 = new Initializationblock();
        initializationblock3.method();
    }
}
```

执行结果：

```
静态初始化块01010101
静态初始化块0202020202
构造初始化块11111
构造初始化块22222
构造初始化块33333
无参构造器00000000
普通初始化块
------------
构造初始化块11111
构造初始化块22222
构造初始化块33333
无参构造器00000000
普通初始化块
构造初始化块11111
构造初始化块22222
构造初始化块33333
无参构造器00000000
普通初始化块
```

2.继承中，多个类的继承中初始化块、静态初始化块、构造器的执行顺序为：**父类静态块——>子类静态块——>父类代码块——>父类构造器——>子类代码块——>子类构造器**

总结：还是和第一点一样，静态块优先级高

### 方法重载(overload)

要求：

1.方法名相同

2.参数列表不同，即参数类型、个数、顺序至少有一项不相同（必须的）

3.返回值可以不同

4.方法的修饰符可以不同（权限）

### 方法重写(override)

1.参数列表与被重写方法的参数列表必须完全相同。

2.访问权限**不能**比父类中被重写的方法的访问**权限更低**。例如：如果父类的一个方法被声明为 public，那么在子类中重写该方法就不能声明为 protected。(总结：只能扩大或保持权限)

3.声明为 final 的方法**不能**被重写，可以重载。

4.声明为 static 的方法不能被重写，但是能够被再次声明。

5.子类和父类在同一个包中，那么子类可以重写父类所有方法，**除了**声明为 private 和 final 的方法。子类和父类不在同一个包中，那么子类只能够重写父类的声明为 public 和 **protected** 的**非 final** 方法。（区别就在于包内可以额外重载default权限的方法）

技巧：<font color=red>在重写方法上面添加@override标记，系统便会提示能否重写</font>



注意：子类同名方法，在无权访问的情况下，会**算作子类的新方法**，而不是重写。

#### 重写和覆盖

java里面因为普通方法相当于虚函数，所以对于非静态函数而言，覆盖=重写

静态方法只能覆盖，不能重写



### finalize方法

Java 允许定义这样的方法，它在对象被垃圾收集器析构(回收)**之前**调用，这个方法叫做 finalize( )，它用来清除回收对象。

例如，你可以使用 finalize() 来确保一个对象打开的文件被关闭了。

在 finalize() 方法里，你必须指定在对象销毁时候要执行的操作。

```java
protected void finalize()
{
   // 在这里终结代码
}
```

jvm会自动回收所创建的对象，一般无需手动销毁

#### 手动销毁对象方法

1、对象的引用超过其作用范围

```java
{
    Object o = new Object(); // 对象o的作用范围，超过这个范围对象将被视为垃圾
    ...
}
```

2、对象被赋值为null

```java
{
    Object o = new Object();
    o = null; // 对象被赋值为null将被视为垃圾
    ...
}
```

在java的Object类当中，提供了一个protected类型的finalize()方法，所以的话，任何的java类都能够覆盖这个方法，在这个方法中进行释放对象所占有的相关资源的操作。

参考：[java手动销毁对象_java对象的销毁详解-CSDN博客](https://blog.csdn.net/weixin_31715171/article/details/114247389)

### java抽象类

1.抽象类不一定全是抽象方法，可以有已经实现的方法，即抽象类中不一定包含抽象方法，但是有抽象方法的类必定是抽象类。

2.抽象类可以拥有数据成员

3.抽象类不能直接生成实例对象，只有抽象类的非抽象子类可以创建对象。

4.抽象类的子类必须给出抽象类中的抽象方法的具体实现，除非该子类也是抽象类。

```java
public abstract class Employee
{
   private String name;
   private String address;
   private int number;   
   public abstract double computePay(); //抽象方法
   //其余代码
}
```

声明抽象方法会造成以下两个结果：

- 如果一个类包含抽象方法，那么该类必须是抽象类。
- 任何子类必须重写父类的抽象方法，或者声明自身为抽象类。
- 构造方法，类方法（用 static 修饰的方法）不能声明为抽象方法。



### 枚举

Java 枚举是一个特殊的**类**，一般表示一组常量

枚举跟普通类一样可以用自己的变量、方法和构造函数，构造函数只能使用 private 访问修饰符，所以外部无法调用。

```java
enum Color 
{ 
    RED, GREEN, BLUE; 
  
    // 构造函数
    private Color() 
    { 
        System.out.println("Constructor called for : " + this.toString()); 
    } 
  
    public void colorInfo() 
    { 
        System.out.println("Universal Color"); 
    } 
} 
  
public class Test 
{     
    // 输出
    public static void main(String[] args) 
    { 
        Color c1 = Color.RED; 
        System.out.println(c1); 
        c1.colorInfo(); 
    } 
}
//输出结果
Constructor called for : RED
Constructor called for : GREEN
Constructor called for : BLUE
RED
Universal Color
```

## java接口

接口并不是类，编写接口的方式和类很相似，但是它们属于不同的概念。

类描述对象的属性和方法。接口则包含类要实现的方法。

除非实现接口的类是抽象类，否则该类要定义接口中的所有方法。

### 接口与类的区别：

- 接口不能用于实例化对象。
- 接口没有构造方法。
- 接口中所有的方法必须是抽象方法，Java 8 之后 接口中可以使用 default 关键字修饰的非抽象方法。
- 接口不能包含成员变量，除了<font color=red>**static** 和 **final**</font> 变量。
- 接口不是被类继承了，而是要被类实现。
- 接口支持多继承。



### 抽象类和接口的区别

- 1. 抽象类中的方法可以有方法体，就是能实现方法的具体功能，但是接口中的方法不行。
- 2. 抽象类中的成员变量可以是各种类型的，而接口中的成员变量**只能**是 **public static final** 类型的。
- 3. 接口中**不能**含有静态代码块以及静态方法(用 static 修饰的方法)，而抽象类是可以有静态代码块和静态方法。
- 4. 一个类只能继承一个抽象类，而一个类却可以实现**多个**接口。

### 特性

1.接口中的方法是**不能**在接口中实现的，只能由实现接口的类来实现接口中的方法。就是说接口中每一个方法也是隐式**抽象**的

2.接口中的方法会被隐式的指定为 **public** abstract（**只能是<font color=red> public</font> abstract**，其他修饰符都会报错），即接口中的方法都是**公有的**。

3.接口是隐式抽象的，当声明一个接口的时候，不必使用abstract关键字，方法亦是如此。

```java
public interface interTest {
     abstract void abFunc();
     public abstract void abFunc2();
     void abFunc3();
}

class AchiveInter implements interTest{
     void abFunc(){//错误方式，应为接口类方法默认权限为public，此处弱化权限了
          
     }
     public  void abFunc2(){//正确
          
     }
}
```

### 实现

类使用**implements**关键字**实现接口**。在类声明中，Implements关键字放在class声明后面。（如上文例子所示）。

- 一个类只能继承一个类，但是能**实现多个接口**。

```java
class AchiveInter implements interTest,interTest2
```



注意：实现归实现（implements），继承归继承（extends）

### 继承

一个接口能继承另一个接口，和类之间的继承方式比较相似。接口的继承使用extends关键字，子接口继承父接口的方法。

类的多继承是不合法，但接口允许**多继承**。

示例：

```java
public interface Hockey extends Sports, Event
```





# JAVA实战

## 序列化

Java 序列化是一种将对象转换为字节流的过程，以便可以将对象保存到磁盘上，将其传输到网络上，或者将其存储在内存中，以后再进行反序列化，将字节流重新转换为对象。

序列化在 Java 中是通过 java.io.Serializable 接口来实现的，该接口**没有任何方法**，只是一个标记接口，用于**标识**类可以被序列化。

如何序列化：查看参考链接



如何反序列化：



一个类的对象要想序列化成功，必须满足两个条件：

1.该类必须实现 java.io.Serializable 接口。（implements java.io.Serializable ）

2.该类的所有属性必须是可序列化的。如果有一个属性不是可序列化的，则该属性必须注明是短暂的。（使用transient关键字）

示例：

```java
public class Employee implements java.io.Serializable
{
   public String name;
   public String address;
   public transient int SSN;
   public int number;
   public void mailCheck()
   {
      System.out.println("Mailing a check to " + name
                           + " " + address);
   }
}
```

参考：[Java 序列化 | 菜鸟教程 (runoob.com)](https://www.runoob.com/java/java-serialization.html)



## 泛型编程

java

1.所有泛型方法声明都有一个类型参数声明部分（由**尖括号**分隔），该类型参数声明部分在方法返回类型**之前**

```java
public static < E > void printArray( E[] inputArray )
```

2.每一个类型参数声明部分包含一个或多个类型参数，参数间用逗号隔开。

3.类型参数只能代表引用型类型，不能是原始类型（像 **int、double、char** 等）。

4.类型参数能被用来声明**返回值类型**，并且能作为泛型方法得到的实际参数类型的占位符。

**java 中泛型标记符：**

- **E** - Element (在集合中使用，因为集合中存放的是元素)
- **T** - Type（Java 类）
- **K** - Key（键）
- **V** - Value（值）
- **N** - Number（数值类型）
- **？** - 表示不确定的 java 类型

示例：

```java
// 泛型方法 printArray                         
   public static < E > void printArray( E[] inputArray )
   {
      // 输出数组元素            
         for ( E element : inputArray ){        
            System.out.printf( "%s ", element );
         }
         System.out.println();
    }
 // 比较三个值并返回最大值
   public static <T extends Comparable<T>> T maximum(T x, T y, T z)
   {                     //限制了返回值类型
      T max = x; // 假设x是初始最大值
      if ( y.compareTo( max ) > 0 ){
         max = y; //y 更大
      }
      if ( z.compareTo( max ) > 0 ){
         max = z; // 现在 z 更大           
      }
      return max; // 返回最大对象
   }
//另一个示例
import java.util.*;
public class GenericTest {     
    public static void main(String[] args) {
        List<String> name = new ArrayList<String>();
        List<Integer> age = new ArrayList<Integer>();
        List<Number> number = new ArrayList<Number>();       
        name.add("icon");
        age.add(18);
        number.add(314);
        //getUperNumber(name);//1  不合法，因为已经限制了类型为Number，不匹配string
        getUperNumber(age);//2
        getUperNumber(number);//3      
   }
 
   public static void getData(List<?> data) {
      System.out.println("data :" + data.get(0));
   }
   
   public static void getUperNumber(List<? extends Number> data) {
          System.out.println("data :" + data.get(0));
       }
}
```

c++模板
