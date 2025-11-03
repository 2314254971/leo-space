## 关键字

### using

1.引用命名空间。

2.为命名空间或类型创建别名。

当同一个cs引用了不同的命名空间，但这些命名控件都包括了一个相同名字的类型的时候,可以使用using关键字来创建别名，这样会使代码更简洁。注意：并不是说两个名字重复，给其中一个用了别名，另外一个就不需要用别名了，如果两个都要使用，则两个都需要用using来定义别名的。

```c#
        using System;
        using aClass = NameSpace1.MyClass;
        using bClass = NameSpace2.MyClass;
        ......
        //使用方式
        aClass my1 = new aClass();           
        Console.WriteLine(my1);
        bClass my2 = new bClass();
        Console.WriteLine(my2);
```

3.使用using语句。

定义一个范围，在范围结束时处理对象。(不过该对象必须实现了IDisposable接口)。其功能和try ,catch,Finally完全相同。

```c#
        using (SqlConnection cn = new SqlConnection(SqlConnectionString)){......}//数据库连接
        using (SqlDataReader dr = db.GetDataReader(sql)){......}//DataReader
        PS：这里SqlConnection和SqlDataReader对象都默认实现了IDisposable接口，如果是自己写的类，那就要自己手动来实现IDisposable接口。比如：
        using (Employee emp = new Employee(userCode))
        {
           ......
        }
Emlpoyee.cs类：        
public class Employee:IDisposable
{
       #region 实现IDisposable接口
        /// <summary>
        /// 通过实现IDisposable接口释放资源
        /// </summary>
        public void Dispose()
        {
            Dispose(true);
            GC.SuppressFinalize(this);
        }
        /// <summary>
        /// 释放资源实现
        /// </summary>
        /// <param name="disposing"></param>
        protected virtual void Dispose(bool disposing)
        {
            if (!m_disposed)
            {
               if (disposing)
                {
                    // Release managed resources
                    if(db!=null)
                        this.db.Dispose();
                    if(dt!=null)
                        this.dt.Dispose();
                    this._CurrentPosition = null;
                    this._Department = null;
                    this._EmployeeCode = null;                
               }
                // Release unmanaged resources（释放非托管资源）
                m_disposed = true;
            }
        }
        /// <summary>
        /// 析构函数
        /// </summary>
        ~Employee()
        {
            Dispose(false);
        }
        private bool m_disposed;
        #endregion
}       
```

可以看作python的with语句或者java的try-with-resource写法



### var

类似于c++ auto，自动识别类型

一旦初始化之后类型就确定了，不可以再更改

