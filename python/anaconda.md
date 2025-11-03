# 操作

## 镜像源

```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
 
conda config --show-sources 查看所有源
conda install 包
conda config --remove-key channels 删除全部源
 
移除指定源：
conda config --remove channels 'https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/'
 
查看anaconda下有多少环境：
 conda info --envs
 
 
 #添加镜像源yes
 
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2

#终端显示包从哪个channel下载，以及下载地址是什么
conda config --set show_channel_urls yes


```

## 环境

```bat
#列出所有环境
conda env list 
conda info --envs

#创建环境
conda create -n env-name [list of package] #-n env-name是设置新建环境的名字，list of package是可选项，选择要为该环境安装的包。
#激活环境
conda activate env-name

#删除环境
conda remove -n xxxxx(名字) --all

```



## 下载包

类似于pip都是通过install指令安装包

不过有些包只能pip下载，这要看分包的人把包放在哪个平台

### 安装requirement

```sh
#pip
pip install -r requirements.txt

#conda
conda install --yes --file requirements.txt
```



### pip相关使用

安装包文件

```cmd
pip install xxx #安装特定包

pip install -e . #以软连接形式安装包，注意后面有个点符号，表示安装当前目录下的包，不是下载，下载使用上面那个指令
#.-e，--editable <path/url>，从本地项目路劲或VCS url以可编辑模式(即setuptools开发模式)安装项目。

pip install -r requirements.txt # 安装requirement.txt的依赖包
```

![img](https://i-blog.csdnimg.cn/blog_migrate/b86b6e793e4da0f02501dd2165e5ed05.png)

执行pip install -h就可以查看具体安装指令选项

pip [command] [-h] 输入指令然后接-h可以看指令的选项



换源

```
pip config set global.index-url <清华源的url>
```



# 安装

执行安装

```shell
chmod +x Anaconda3-5.3.0-Linux-x86_64.sh
Anaconda3-5.3.0-Linux-x86_64.sh
```



卸载：

```
##首先使用conda安装anaconda清理工具
conda install anaconda-clean
##然后执行
anaconda-clean
or
anaconda-clean --yes
```



添加环境变量linux：

```sh
export PATH=~/anaconda3/bin:$PATH 
#在尾部添加即可

#后续使用source激活即可，或者先激活试试看有没有自动添加
source ./bashrc
```





## 问题：

无法下载包：

可能是ssl问题

```cmd
conda config --set ssl_verify false
#设置为false就能正常访问http
#下载完记得改回true
```

