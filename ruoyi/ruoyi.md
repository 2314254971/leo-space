## ruoyi新增网页不登陆就无法进入

权限问题

再src/permission.js里面的whitelist里面把新网页添加进来

就是添加入白名单

[ruoyi前后端分离版：添加新页面，不需登录也可访问 - qsad阿斯顿 - 博客园 (cnblogs.com)](https://www.cnblogs.com/huashenyin/p/16122113.html)



## POM导入依赖模块不正常

1.parent设置不正常导致找不到父包

2.依赖信息不齐全，一般而言只需再dependence写上groupid和artifactid即可导入

但是有些地方要求严格需要额外添加版本号

终极大法：把依赖项的所有信息都尽量填完善大概率没问题

[【dependencyManagement版本管理】dependencies.dependency.version is missing - DJ同学 - 博客园 (cnblogs.com)](https://www.cnblogs.com/10134dz/p/14294535.html)

3.或者再根目录的pom的module标签里面加入该模块





如果模块没加载进来

取项目根目录下修改pom文件

添加dependence和module

## 未登录请求数据被后端拦截

在securityConfig下的antMatcher下加入自己的请求路径





## 修改密码应该redis和数据库同步

修改密码的类是

SysUserService和SysUserMapper

SysProfileController



## 表单重置

```js
// 表单重置
function reset() {
  form.value = {
    id: null,
    userId: null,
    friendId: null,
    group_name: null
  };
  proxy.resetForm("friendshipRef");
}
```

表单重置如果是使用resetForm就只能清空显示，而没有清空实际的form，所以手动置空





```
<if test="userId != null ">  user_id = #{userId}</if>
<if test="friendId != null "> and friend_id = #{friendId}</if>
<if test="group_name != null  and group_name != ''"> and group = #{group_name}</if>)
 or (<if test="friendId != null "> user_id = #{friendId}</if>
<if test="userId != null "> and friend_id = #{userId}</if>
<if test="group_name != null  and group_name != ''"> and group = #{group_name}</if>)
```





## 修改标题和logo

vue2可以直接在layout 的component下的logo.vue修改标题和logo图标

vue3的标题被写在`.env.development`文件下

修改VITE_APP_TITLE即可



修改页面图标则直接在ruoyi-ui下的index.vue直接修改，原来的图标名为favicon.ico



## 静态资源访问

https://www.jb51.net/javascript/316020fro.htm

最好把静态资源都放入public文件夹下

直接使用/即代表public文件夹

## 资源映射

访问：

一般服务器资源需要使用一个基础的url和资源url拼接得到

```js
const baseUrl=import.meta.env.VITE_APP_BASE_API

imgurl=baseUrl+"/profile/xxx.jpg"
```

`import.meta.env.VITE_APP_BASE_API`这个实际上就是写在`.env.development`文件下

映射：

`application.yml`里面写了本地的文件地址，变量为profile

同时RuoYiConfig文件里面读取了该profile

并在ResourcesConfig  java文件中将其映射出去

参考：[Ruoyi自定义资源映射_若依框架前端profile映射本地系统-CSDN博客](https://blog.csdn.net/damowangsx/article/details/120009832)



## scope

```vue
//示例1
//我定义了一个list=[]，list里面有多条用户记录，如list=[[userid=1,avartar=""],[userid=2,avartar=""]],这些数据已经和表格绑定。使用scope.row相当于直接获取到了其中一条记录，并且可以直接访问值，例如avatar可以直接scope.row.avatar
scope相当于直接获取到了当前的控件数据
<el-table-column label="头像" align="center" prop="avatar" >
  <template #default="scope">
    <div class="demo-image__preview">
    <el-image style="width: 50px; height: 50px" :src="scope.row.avatar" fit="fill" ></el-image>
    </div>
  </template>
</el-table-column>

//示例2
 <template scope="scope">
          <div class="info" :s="JSON.stringify(scope)">
            <p>姓名:{{scope.row.name}}</p>
            <p>年龄: {{scope.row.age}}</p>
            <p>性别: {{scope.row.sex}}</p>
            <p>索引：{{scope.$index}}</p>
          </div>
        </template>
```

在调用组件的页面中我们可以使用 `template scope="xxx"` 或者`#default="scope"`来获取插槽上的**属性值**，获取到的值是一个对象。

上述scope.row就可以获取当前行的数据对象

然后直接使用属性

[理解vue中的scope的使用 - 龙恩0707 - 博客园 (cnblogs.com)](https://www.cnblogs.com/tugenhua0707/p/7745735.html)





## template界面使用变量表达式

```vue
<el-image style="width: 50px; height: 50px" :src="`/dev-api${scope.row.avatar=='' ? '/profile/avatar/2024/08/28/profile1.webp':scope.row.avatar }`" fit="fill"></el-image>
```

此处我做了一个判断，如果用户头像不存在，那我就是要默认的，否则就用传过来的。其中还包含了拼接操作，如果不使用表达式只能用js进行处理，此处直接使用表达式即可实现。

使用`${ }`符号来插入表达式，并获取表达式的值/变量值



## mybaties  XML

确保 MyBatis 的配置正确无误。在 Ruoyi 框架中，MyBatis 的配置通常是通过 Spring Boot 的自动配置来完成的，但你可以检查 `application.yml` 或 `application.properties` 文件中是否有相关的 MyBatis 配置，如 mapper 文件的扫描路径。

在 `application.yml` 中，你应该有类似这样的配置：

```yaml
mybatis:  
  mapper-locations: classpath*:mapper/**/*.xml  
  type-aliases-package: com.ruoyi.project.**.domain
```

这里面规定了xml应该放在mapper文件夹下





