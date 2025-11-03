# windows访问wsl服务（本机）

可以直接localhost访问



# wsl访问windows服务（本机）

通过wsl适配器ip间接访问：

执行ipconfig 查找 以太网适配器 vEthernet (WSL) 对应ip

之后通过该ip加上服务的端口号即可访问



# wsl服务映射到Windows（让其他设备也可以访问）

方法一：

将wsl的网络模式设置为镜像模式，之后就相当于和windows融为一体，只要访问windows端口就能同时触发wsl

设置可以通过用户文件夹下编写.wslconfig 文件 或者 直接windows搜索wsl setting，选择网络进行调整即可



方法二：

端口转发

管理员模式打开powerhell

添加规则：

```shell
# 添加端口转发规则
netsh interface portproxy add v4tov4 listenport=8989 listenaddress=0.0.0.0 connectport=8989 connectaddress=172.17.45.152

# 注意：上面connectaddress是wsl的ip
# 在wsl控制台执行ifconfig 或者 
# powershell执行wsl -- ifconfig eth0  就可以获得ip
```

其他操作

```shell
# 删除端口转发规则
netsh interface portproxy delete v4tov4 listenport=8989 listenaddress=0.0.0.0

# 重置所有端口代理（清空规则）
netsh interface portproxy reset

# 查看所有端口转发规则
netsh interface portproxy show all
```

