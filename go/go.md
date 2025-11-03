安装

方法一：

yum/apt安装

方法二：压缩包安装：

1.解压

2.配置环境变量

一般而言配置bin目录即可

如果有问题再配GOPATH GOROOT



换源

```sh
go env -w GOPROXY=https://goproxy.cn,direct
```



调试：

安装delve

[Releases · go-delve/delve](https://github.com/go-delve/delve/releases)

安装对应版本即可（go build   go install）

或者直接 

```sh
go install github.com/go-delve/delve/cmd/dlv@latest
```

