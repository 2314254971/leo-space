## 文件夹

```shell
# 列出文件夹下文件总占用
du -sh /home/user/Documents
# 输出：2.3G    /home/user/Documents

```



## 安装package问题

apt安装时找不到包大概率是源有问题

关于换源后仍旧有概率遇到如下问题

`ubantu换源之后执行sudo apt-get update报错The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 40976EAF437D05B5 NO_PUBKEY 3B4FE6ACC0B21F32。怎么解决`

1. 首先，你需要知道每个NO_PUBKEY后面跟着的密钥ID对应的公钥。在你的例子中，密钥ID是`40976EAF437D05B5`和`3B4FE6ACC0B21F32`。

2. **导入公钥**

   你可以使用`apt-key`命令来导入公钥。打开终端，然后分别为每个密钥ID运行以下命令：

   ```bash
   sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 40976EAF437D05B5  
   sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 3B4FE6ACC0B21F32
   ```

   这里`--keyserver keyserver.ubuntu.com`指定了从哪个密钥服务器获取公钥，`--recv-keys`后面跟着的是你想要导入的密钥ID。

3. **再次更新软件包列表**

   导入公钥后，再次运行`sudo apt-get update`来更新你的软件包列表。

   ```bash
   bash复制代码
   
   sudo apt-get update
   ```





# 防火墙

```shell
#查看版本
firewall-cmd --version

#显示状态
firewall-cmd --state

#查看当前所有规则
firewall-cmd --list-all

#列出所有打开的端口
firewall-cmd --zone=public --list-ports

#添加开放端口
firewall-cmd --zone=public --add-port=80/tcp --permanent (permanent永久生效，没有此参数重启后失效)
#添加多个端口
firewall-cmd --zone=public --add-port=80/tcp --add-port=8000/tcp --permanent

#删除开放端口
firewall-cmd --zone=public --remove-port=80/tcp --permanent

#更新防火墙规则
firewall-cmd --reload
```

参考：[firewalld开放端口-CSDN博客](https://blog.csdn.net/weixin_46106069/article/details/114700217)

