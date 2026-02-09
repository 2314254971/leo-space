# 原理

类似于QT，通过XML格式定义界面，通过代码定义交互逻辑

其背后都有一套专属的XML解析器，获取页面控件信息（位置，样式等），然后自动生成对应的“前端类”，与后台类共同作用

区别在于qt会基于XML生成对应UI类hpp文件，后台类实际上是继承了这个UI类，实现界面元素关联的

而WPF实际上是UI和后台是同一个类，通过partial关键字分开定义

例如：

```C#
//后台类
public partial class KnowledgeBaseWPF : WPFAdapter{
    ...
}

//UI类（自动生成）   (自动继承IComponentConnector接口，实现控件连接)
public partial class KnowledgeBaseWPF : Cyber.CyberEngPlatformNET.WPFAdapter, System.Windows.Markup.IComponentConnector {
    [System.Diagnostics.DebuggerNonUserCodeAttribute()]
        [System.CodeDom.Compiler.GeneratedCodeAttribute("PresentationBuildTasks", "4.0.0.0")]
        public void InitializeComponent() {  //组件初始化方法
            if (_contentLoaded) {//如果组件以初始化就跳过
                return;
            }
            _contentLoaded = true;
            System.Uri resourceLocater = new System.Uri("/TSM;component/app/wpf/knowledgebasewpf.xaml", System.UriKind.Relative);
            
            #line 1 "..\..\..\..\..\APP\WPF\KnowledgeBaseWPF.xaml"
            System.Windows.Application.LoadComponent(this, resourceLocater);//解析xaml文件生成组件
            
            #line default
            #line hidden
        }
    void System.Windows.Markup.IComponentConnector.Connect(int connectionId, object target) {//初始化组件，按照名称，将组件名作为控件指针指向已经生成的控件地址，实现关联
            switch (connectionId)
            {
            case 1:
            this.RootWindow = ((TSM.APP.WPF.KnowledgeBaseWPF)(target));
            return;
            case 2:
            this._searchButton = ((System.Windows.Controls.Button)(target));
            return;
            case 3:
            this._searchTextBox = ((System.Windows.Controls.TextBox)(target));
            return;
            case 4:
            this._fileTree = ((System.Windows.Controls.TreeView)(target));
            
            #line 433 "..\..\..\..\..\APP\WPF\KnowledgeBaseWPF.xaml"
            this._fileTree.MouseDoubleClick += new System.Windows.Input.MouseButtonEventHandler(this.TreeView_MouseDoubleClick);
            
            #line default
            #line hidden
            return;
            case 5:
            this._gimBox = ((System.Windows.Controls.ListBox)(target));
            return;
            case 6:
            this._pdfViewer = ((WPFPdfViewer.LshPdfViewerControl)(target));
            return;
            case 7:
            this._imgViewer = ((CustomControl.ImageViewer)(target));
            return;
            case 8:
            this._gimViewer = ((System.Windows.Controls.ContentControl)(target));
            return;
            case 9:
            this.LoadingOverlay = ((System.Windows.Controls.Border)(target));
            return;
            }
            this._contentLoaded = true;
        }
}
```

上述生成的UI类主要实现了界面的初始化（例如控件生成），InitializeComponent用于解析xaml并生成控件

Connect方法用于将生成的组件和变量（变量名为控件的Name属性）关联（类似于指针指向控件），即先按照控件ID生成控件列表，然后将ID对应的控件和Name进行关联。



设计模式：MVVM，界面和功能分离

## 界面设计



###  样式

样式从近到远生效，

不过有特殊

```
App.xaml 或 第三方主题 (如MaterialDesign)
    └── 定义了 TextBlock 样式 ← 优先级更高！

Window.Resources
    └── 你的 TextBlock 样式 ← 被覆盖！
```



### 数据上下文

在 XAML 元素上声明数据绑定时，它们通过查看其即时 [DataContext](https://learn.microsoft.com/zh-cn/dotnet/api/system.windows.frameworkelement.datacontext) 属性解析数据绑定。 数据上下文通常是**绑定源对象**用于评估**绑定源值路径**的。 可以在绑定中重写此行为，并设置特定的 **绑定源对象** 值。 如果未设置`DataContext`属性，则会检查承载绑定的对象的父元素的`DataContext`属性，**依次向上检查**，直到 XAML 对象树的根节点为止。 简而言之，用于解析绑定的数据上下文继承自父级，除非对对象显式设置。

可以将绑定配置为使用特定对象进行解析，而不是使用数据上下文进行绑定解析。 直接指定源对象用于在例如将一个对象的前景色绑定到另一个对象的背景色时使用。 数据的上下文不是必需的，因为绑定会在这两个对象之间自动解析。 相反，未绑定到特定源对象的绑定使用数据上下文解析。

`DataContext`当属性发生更改时，将重新评估可能受数据上下文影响的所有绑定。



### 属性绑定

通过Binding属性绑定属性（注意：**属性≠字段**，属性一般定义了set和get方法，直接绑定字段会在输出窗口报错）

Binding参数 Path是默认值，实际使用时可以不写



绑定模式

| 枚举名称       | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| OneWay         | 当源属性变化时更新目标属性                                   |
| TwoWay         | 当源属性变化时更新目标属性 ，当目标属性 变化时更新源属性     |
| OneTime        | 最初设定源属性时更新目标属性，后期的变化都被忽略 ，如果知道源属性不会变化，可以使用这种模式，降低开销 |
| OneWayToSource | 与OneWay 类似，但方向相反，当目标属性变化时更新源属性，有点向后传递，但目标属性永远不会被更新 |
| Default        | 此类绑定依赖于目标属性，即可以是双向(对于用户可以设置的属性，如TextBlock.Text，) 也可以是单向，除非明确指明一种模式，否则都采用此种模式 |

```xaml
 <TextBlock x:Name="tb" Text="{Binding Text,ElementName=sourceTBox,Mode=TwoWay,UpdateSourceTrigger=Default}" 
双向绑定，两边同时变化
            
<CheckBox x:Name="cb1"  
Visibility="Collapsed" 
IsChecked="False"  
HorizontalAlignment="Center" 
VerticalAlignment="Center" 
Width="50" Height="30" 
BorderThickness="0" 
IsVisibleChanged="cb1_IsVisibleChanged" />

<TextBox x:Name="sourceTBox" />

<TextBlock x:Name="tb" 
           Text="{Binding Text,ElementName=sourceTBox,Mode=TwoWay}" 
           Tag="{Binding Content ,ElementName=cb1,Mode=OneWay}"
           Visibility="{Binding IsChecked,ElementName=cb1,Converter={StaticResource Boolean2VisibilityConverter}}"
          />
extBlock 的 Text 、Tag 、Visibility 属性都设置绑定表达式，这就是多绑定
```

#### 绑定到元素对象

```xaml
需要绑定的目标元素,需要提前命名
<TextBox x:Name="sourceTBox" />

<TextBlock x:Name="tb" Text="{Binding ElementName=sourceTBox,Path=Text}" />
 等价于（省略path）
<TextBlock x:Name="tb" Text="{Binding Text,ElementName=sourceTBox}" />

```



#### 绑定到非元素对象

绑定到非元素的对象时，使用以下属性的其中一个。
– Soure：该属性是指向源对像的引用(数据的对像)
– RelativeSource：RelativeSource 属性可以根据相对目标对像的关系指向源对像，例如，可以使用RelativeSource 属性将元素绑定 **自身** 或 **父元素**。
– DataContent：如果没有使用Source和RelativeSource 指定源，Wpf就从当前元素开始在元素树中向上查找。检查每一个元素的DataContent属性，并使用 第一个非空的DataContent 属性。当我们在要同一个对像绑定不同元素时 DataContent属性 是非常有用的。

```xaml
<TextBlock  Text="{Binding Title,RelativeSource={RelativeSource Mode=FindAncestor,AncestorLevel=1,AncestorType={x:Type Window}}}" />
<!--一般一个窗口只有一个Window 对像，可以再次简写-->
<TextBlock  Text="{Binding Title,RelativeSource={RelativeSource Mode=FindAncestor,AncestorType={x:Type Window}}}" />
<!--等价于-->
<TextBlock  Text="{Binding Title,RelativeSource={RelativeSource AncestorType=Window}}" />
```

其中AncestorLevel表示从该目标元素向外查找几层，1代表离当前元素最近的Window元素，2则是再次向外找一层window



#### 引用绑定

```xaml
<DataTrigger Binding="{Binding IsLight, Source={x:Reference Name=RootWindow}}" Value="True">
                                        <Setter Property="Background" Value="#F5F5F5"/>
                                    </DataTrigger>


通过{x:Reference 元素名称}
形式直接获取控件引用，这种绑定可以跨阶段，即等到控件完全生成再绑定，不会出现绑定时控件还没完成关联的情况
比ElementName更为灵活
```

注意事项：

1.引用必须在同一名称范围内

```
<!-- 这样是OK的 -->
<Window>
    <Slider x:Name="slider1"/>
    <TextBox Source="{x:Reference slider1}"/>
</Window>

<!-- 这样可能有问题 -->
<Window>
    <UserControl>
        <Slider x:Name="slider1"/>
    </UserControl>
    <!-- 这里可能引用不到slider1，因为名称范围不同 -->
    <TextBox Source="{x:Reference slider1}"/>
</Window>

```

2.避免循环引用

```
<!-- 错误：循环引用 -->
<TextBox x:Name="text1" Text="{Binding Text, Source={x:Reference text1}}"/>

```

[C# WPF {x:Reference}的作用 - 竹楼风雨声 - 博客园](https://www.cnblogs.com/sound-of-wind-rain/p/19122531)





参考：

[数据绑定概述 - WPF | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/desktop/wpf/data/)

[C# Wpf Binding 使用详解_c# binding-CSDN博客](https://blog.csdn.net/badxnui/article/details/111773330)



### 数据转换



### 触发器



### 模板设计



## 资源

### 静态资源

[WPF资源文件深入解析与源码实践-CSDN博客](https://blog.csdn.net/weixin_42300144/article/details/148058733)

### 动态资源



### 资源继承和查找



# 注意事项

1.属性绑定时存在已经明确定义属性，但就是查找不到的情况

问题描述：在**window.reference**区域（不是窗体定义区域，而是资源区）定义了一个通用的样式转换触发器，根据窗体的一个变量确定使用暗色调还是亮色调

本人测试使用ElementName = rootwindow（窗体命名为rootwindow） 或者{Binding IsLight, 

​               RelativeSource={RelativeSource AncestorType=window}}

来关联窗体均报错：

```
BindingExpression path error: 'IsExpanded' property not found on 'object' ''FileNode' (HashCode=55966819)'. BindingExpression:Path=IsExpanded; DataItem='FileNode' (HashCode=55966819); target element is 'TreeViewItem' (Name=''); target property is 'IsExpanded' (type 'Boolean')
```

简单来说

都是找不到属性

经过AI和本人分析原因是：

| 绑定方式                               | 解析时机             | 依赖机制                       | 失败原因                                                     |
| -------------------------------------- | -------------------- | ------------------------------ | ------------------------------------------------------------ |
| `ElementName=RootWindow`               | STEP 1（资源加载时） | `NameScope.FindName()`         | 此时 `RootWindow` 字段 = `null`，且资源字典**不在任何 NameScope 中** |
| `RelativeSource={AncestorType=Window}` | STEP 1（资源加载时） | `VisualTreeHelper.GetParent()` | 资源字典**尚未附加到可视化树**，无法向上查找                 |
| `Source={x:Reference RootWindow}`      | **延迟到 STEP 2 后** | **编译器生成的直接字段引用**   | 绑定表达式被特殊标记，**延迟求值**直到 `RootWindow` 字段赋值完成 |

根据UI类方法分析，即使命名了窗体，也找不到窗体信息，因为rootwindow关联窗体在xaml解析之后，生成资源快照时，rootwindow还是null，所以ElementName找不到。RelativeSource也是，由于资源快照问题，无法向上查找（resource解析先于控件生成）

因此使用引用才能生效。

另一种可能性是trigger支持绑定的类型有限