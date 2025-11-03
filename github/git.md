# 概览

![img](https://www.runoob.com/wp-content/uploads/2015/02/git-command.jpg)

**说明：**

- workspace：工作区
- staging area：暂存区/缓存区
- local repository：版本库或本地仓库
- remote repository：远程仓库

流程：本地代码先添加到暂存区，再提交至本地仓库，然后才能提交至远程仓库

而下载直接pull/clone即可

# 本地仓库

```bat
git init    
git add .    
git commit  
```

- git init - 初始化仓库。
- git add . - 添加文件到暂存区。
- git commit - 将暂存区内容添加到仓库中。

## 创建本地仓库

```
#使用当前目录作为 Git 仓库，我们只需使它初始化。
git init
#使用我们指定目录作为Git仓库。
git init 文件夹名字
```

初始化后，会在 newrepo 目录下会出现一个名为 .git 的目录（这就是本地仓库），所有 Git 需要的数据和资源都存放在这个目录中。

## 查看代码修改状态

```
git status [-s]
```

可以查看在你上次提交之后是否有对文件进行再次修改。

通常我们使用 **-s** 参数来获得简短的输出结果

## 添加文件到暂存区

```bat
#文件
git add [file1] [file2] ...
#文件夹
git add [dir]
#添加当前目录下的所有文件到暂存区：
git add .

#重置暂存区
git reset .
```

暂存区删除文件

```
git reset HEAD -- . (注意最后的一个".",这条命令帮助我们一次性撤销所有放入暂存区的文件)

git reset HEAD -- filename(撤销指定目标文件)

git rm --cached filename(撤销指定目标文件)
```

以上三种方法只是把文件从暂存区中删除了（或者叫撤销了），并不会修改文件内容，也不会删除文件，在本地夹里还能找到。

注意：

`git rm -f filename`命令，也能把文件从暂存区删除，但是此命令也同时删除了本地文件，回收站中也找不到了，提醒广大同胞，**慎重使用**此命令来撤销暂存区的文件。

## 提交

```bash
git commit  
```

执行后会将暂存区的数据存放之.git文件夹下（即本地仓库）

## 分支

查看分支

```bat
#查看本地分支名称
git branch
#查看所有远程分支的名称
git branch -r
#查看所有的本地和远程分支
git branch -a
```

创建分支

```bat
#创建一个本地分支：
git checkout -b  V1.1(分支名)

#建立本地分支和远程分支的关联（创建远程分支）
git push --set-upstream origin V1.1（分支名）
```

修改分支名

```sh
git branch -m master main #把master名称修改为main
```



# 远程仓库

## 远程登录



## 查看版本

```bat
// -v 是 --verbose 的简写
git remote -v
```

查看其他信息

```bat
git remote show [remote_name]
```

## 添加远程仓库

```bat
git init#先本地初始化
git remote add origin http://git.XXXX.com/XXX/XXX.git/#后面这个是远程仓库名和url地址，名字可以自定义（只是对本地而言的名字）

git remote rename origin new-origin#修改远程仓库名字（只是对本地而言的名字）
```



```bat
git remote add <remote_name> <remote_url>
```

- `<remote_name>`：要添加的远程仓库的名称。通常，远程仓库的名称为 `origin`，但你也可以自定义一个名称。
- `<remote_url>`：远程仓库的 URL。它可以是一个指向远程 Git 仓库的 HTTPS、SSH 或 Git 协议链接。

## 切换/修改远程仓库地址

```bat
git remote set-url origin http://git.XXXX.com/XXX/XXX.git/

git remote set-url <your hub name> <yuor hub url>
```

## 删除仓库

```bat
git remote rm name  # 删除远程仓库
```

## 代码提交

```bat
git add .
git commit -m 'xxx '#自己的备注
git push -u origin master  // 这是关键一步，这里可以切换分支名
```



```bat
git push <远程主机名> <本地分支名>:<远程分支名>
#如果本地分支名与远程分支名相同，则可以省略冒号
git push <远程主机名> <本地分支名>
```

## 撤销提交

`git reset` 命令有三种主要模式用于撤销提交：`--soft`、`--mixed` 和 `--hard`。

- `--soft`：撤销提交，但保留暂存区的状态，适用于需要保留代码改动的情况。
- `--mixed`（默认）：撤销提交并清除暂存区的状态，但保留工作目录中的代码改动。
- `--hard`：删除提交并删除工作目录中的代码改动。



--soft 模式 （一般用这个）
不删除工作目录中的代码改动。
只撤销提交，保留暂存区的状态。
使用命令：

```
git reset --soft HEAD^
```

--mixed 模式（默认）
不删除工作目录中的代码改动。
撤销提交和暂存区的状态。
使用命令：

```
git reset HEAD^
```

或

```
git reset --mixed HEAD^
```

--hard 模式
删除工作目录中的代码改动。
撤销提交和暂存区状态。
使用命令：

```
git reset --hard HEAD^
```

撤销指定的提交版本
可以通过以下方式指定要撤销的版本：

使用 HEAD^ 表示上一个提交版本：

```
git reset --soft HEAD^    # 撤销最近一次提交
git reset --soft HEAD~2   # 撤销最近两次提交
```

2.使用提交的 commit ID： 

```
git reset --soft <commit-id>  # 回退到指定的提交版本
```

## 删除文件夹



```sh
 git rm -r 【文件夹名称】
 
 # 然后commit push
```





## git添加gitlab





# git lfs

用于下载大文件

首先自行百度下载安装gitlfs

然后使用git-lfs下载大文件，步骤如下：

```sh
#在终端输入以下命令，将环境变量GIT_LFS_SKIP_SMUDGE设置为1：
# 这条命令允许在克隆时，跳过下载lfs文件。
export GIT_LFS_SKIP_SMUDGE=1
#第二步：正常克隆文件
git lfs install #初始化
git clone xxxx #正常下载文件，但是会跳过大文件
#第三步：下载大文件
cd xxxx#切换到刚才的目录
git lfs pull #开始下载大文件
```

