# python

## python对象

在python中一切皆为对象，变量是对象，函数是对象，类也是对象。

每一个对象都具有两个标准的头部信息：

**类型标志符**：标识对象的类型。

**引用计数器**：用来决定对象是不是进行回收。



Python对象三要素：Id，Type，Value
Id：唯一标识一个对象
Type：标识对象的类型
Value：对象的值



### 引用

python中赋值语句，

总是**建立对象的引用值**，而不是复制对象。

因此，python变量更像是**指针**，**而不是数据存储区域**。

```python
a = 1  # 创建一个对象来代表值1，创建一个变量a，如果它还没有创建的话。将变量a 与 新的对象3 相连接。
a = 'python'
a = 1.2

# 这里的 变量a 被多次赋值，
# 并不是修改的对象，而是修改的引用，
# a指向1，然后修改引用指向 'python'，
# 最后指向1.2。
```

1 和 'python' 被放在内存空间内，

在没有其他变量引用时，**引用计数**为0，

这个对象的**内存空间**就会**自动回收**。



这里也并不是修改变量 a 的类型，

因为**变量没有类型**，只是它**指向的对象具有类型**，

即对象头部信息的类型标志符。

### 拷贝

[(26 封私信 / 80 条消息) Python彻底搞懂：变量、对象、赋值、引用、拷贝 - 知乎](https://zhuanlan.zhihu.com/p/265179766)



### 函数对象

函数身为一个对象，拥有对象模型的三个通用属性：id、类型、和值。

> 把函数内部的变量看成函数的一个属性，使用赋值语句将一个变量添加为该函数对象的属性。

> **注意：**只有函数被调用（函数内部的赋值语句被执行过）后，我们才可以用**对象点的**方式访问函数对象的属性

```python
# 示例一
def funX(x):
 
    m_x = x
    funX.m_x = m_x  # 把函数内部的变量看成函数的一个属性，使用赋值语句将一个变量添加为该函数对象的属性。
 
 
print(funX)
funX(3)  # 只有函数被调用（函数内部的赋值语句被执行过）后，我们才可以用对象点的方式访问函数对象的属性
print("funX.m_x = ", funX.m_x)  # m_x被编译器置灰了，说找不到该属性，因为函数还没有调用（函数内部的赋值语句还执行过，所以还没有这个属性）


#示例二
 # 下次调用就可以查看上次调用的结果。
# 下面实现的功能：如果一个函数被执行过一次后（属性被赋值），后面再执行就用原来的属性值
# 和单例思想有点像
def funY(y):
 
    if hasattr(funY, "m_y"):  # hasattr:意思为has a attribute? 就是验证某对象是否有某个属性
        return funY.m_y  # 返回保存的属性值
 
    m_y = y
    funY.m_y = m_y  # 将m_y设为函数对象的属性，保存起来
 
    return m_y
 
 
funY(8)
print(funY.m_y) # 输出 8
funY(10)
print(funY.m_y) # 输出 8
```







## 装饰器

装饰器本质就是函数闭包，拓展原函数功能

### 原生装饰器



### 自定义装饰器

```python
# 语法
def my_wrapper(func):
    def wrapper():
        print("my_wrapper是一个装饰器函数")
        func()
    return wrapper

@my_wrapper
def say_hello():
    print("hello python")

say_hello()

输出：
my_wrapper是一个装饰器函数
hello python

```

`@my_wrapper`就是一个python的装饰器，即装饰器就是一个符号`@`加上一个`函数名`。因为函数`say_hello`已经被`my_wrapper`函数装饰过了，所以直接使用`say_hello()`即可完成上面的调用。

```python
	...
@my_wrapper
def say_hello():
    print("hello python")

say_hello()

"""上面的代码等价于下面的代码："""

	...
def say_hello():
    print("hello python")
    
say_hello = my_wrapper(say_hello)
say_hello()

```

函数上面加上个装饰器`@my_wrapper`，就等价于执行`say_hello = my_wrapper(say_hello)`

将原函数对象指向的新的函数对象（被装饰器加强后的函数对象）

（调试时可以输出id，发现传入和传出后，地址不一样了）

#### 带参数装饰器

写法：（总的来说，在原装饰器外部，再添加一层，用于接收参数）

```python
# 例子6
def repeat(n):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(n):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")

输出：
Hello, Alice!
Hello, Alice!
Hello, Alice!

```

等价于`greet = repeat(3)(greet)`。

repeat(3)的返回值是函数对象decorator，上述代码进一步优化为：`greet = decorator(greet)`。

decorator(greet)返回值是函数对象wrapper，即：`greet = wrapper`。

那么greet("Alice")就等价于wrapper("Alice")。



#### 类装饰器



[Python 类方法装饰器：深入理解与高效使用 - 极客技术博客](https://geek-blogs.com/blog/class-method-decorator-python/#装饰器)



### 装饰器嵌套

装饰器的**执行**是**从外到内**执行的

装饰器的结合顺序是从内到外的

理解：根据上一节的解释，多个装饰器装饰一个函数，实际上一个函数嵌套

`装饰器1（装饰器2（装饰器n(func)））`

```python
def wapper21(f):
    def inner(*args,**kwargs):
        f(*args,**kwargs)
        print('装饰器21')
    return inner
 
def wapper22(f):
    def inner(*args,**kwargs):
        f(*args,**kwargs)
        print('装饰器22')
    return inner

def wapper23(f):
    def inner(*args,**kwargs):
        f(*args,**kwargs)
        print('装饰器23')
    return inner


@wapper23
@wapper21
@wapper22
def fun1():
    print('我是原函数！')

fun1()

```

![多层装饰器嵌套执行的过程和执行结果](https://i-blog.csdnimg.cn/blog_migrate/09efd7bf8e647eb02bdcba629eaf25b7.png)

上述情况等价于：

```python
func1=wapper23(wrapper21(wrapper22(func1)))

拆分：
进入wrapper23返回inner(wrapper21(wrapper22(func1)))
最后
func1=inner(inner21(inner22(func1)))

```

因此执行从外到内



#### 特殊情况

装饰器a 装饰 装饰器b

等价于`装饰器a(装饰器b)（func）` 

区分上一种情况

```python
# config是一个全局对象，用于配置回调函数

def trace(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        if config.project.enable_telemetry:
            event_name = func.__name__
            with chainlit_telemetry.tracer.start_as_current_span(
                event_name, record_exception=False, set_status_on_exception=False
            ):
                return func(*args, **kwargs)
        else:
            return func(*args, **kwargs)

    return wrapper

@trace
def on_stop(func: Callable) -> Callable:
    """
    Hook to react to the user stopping a thread.

    Args:
        func (Callable[[], Any]): The stop hook to execute.

    Returns:
        Callable[[], Any]: The decorated stop hook.
    """

    config.code.on_stop = wrap_user_function(func)
    return func


# 另一个文件，导入on_stop装饰器
@cl.on_stop
async def on_stop_chat():
    pass
```

这种情况属于trace装饰器 包装了on_stop装饰器

上述情况等价于

```
on_stop_chat= trace(on_stop)(on_stop_chat)

拆分一下：
trace(on_stop)(on_stop_chat) 
==> wrapper(on_stop)(on_stop_chat)
==>
def wrapper(*args, **kwargs):
        if config.project.enable_telemetry:
            event_name = func.__name__
            with chainlit_telemetry.tracer.start_as_current_span(
                event_name, record_exception=False, set_status_on_exception=False
            ):
                return on_stop(*args, **kwargs)
        else:
            return func(*args, **kwargs)
==>
在on_stop中
返回func
因此最后等价于
on_stop_chat = on_stop_chat

（config.code.on_stop = wrap_user_function(func)它绑定的是包裹后的on_stop_chat函数，但是on_stop_chat本身为被改变）
```



## 异步/协程

异步针对io操作进行优化，适用于请求/文件读写场景

底层基于事件循环，即开启一个事件循环线程，创建任务队列，当协程任务提交后，调度器自动从队列中获取任务并执行

协程通过yield让出执行权，去执行其他任务

因此在一个eventloop中，协程是串行的

==>协程不适合做cpu密集型任务，无法并行



细节查看asyncio的git：

c++: https://github.com/netcan/asyncio

python版本：https://github.com/python/asyncio







# CPython