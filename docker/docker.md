# docker换源

：（修改daemon.json）

```json
{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "experimental": false,
  "registry-mirrors": [
    "https://docker.1panel.live",
    "https://dockerproxy.net",
    "https://docker.mirrors.ustc.edu.cn",
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com",
    "https://ccr.ccs.tencentyun.com",
    "https://registry.docker-cn.com"
  ]
}
```

# docker常用操作

```shell
#列出所有docker镜像
docker images

#查看正在运行的docker容器
docker ps
docker ps -a#这个命令也会显示未启动的容器信息



#停止所有容器
docker stop $(docker ps -aq)
#停止单个容器
docker stop [容器ID或名称]


#删除所有容器
docker rm $(docker ps -aq)
#删除单个镜像
docker rmi [镜像ID或名称]
#如果镜像正在被容器使用，可能需要先删除那些容器或使用强制删除选项：
docker rmi -f [镜像ID或名称]

#删除所有镜像
docker rmi $(docker images -q)
```



# docker yaml编写

定义变量：

```yml
#后面是默认值
x-shared-env: &shared-api-worker-env
  ELASTICSEARCH_HOST: ${ELASTICSEARCH_HOST:-0.0.0.0}
  ELASTICSEARCH_PORT: ${ELASTICSEARCH_PORT:-9200}
  ELASTICSEARCH_USERNAME: ${ELASTICSEARCH_USERNAME:-elastic}
  ELASTICSEARCH_PASSWORD: ${ELASTICSEARCH_PASSWORD:-elastic}
  KIBANA_PORT: ${KIBANA_PORT:-5601}
  SANDBOX_API_KEY: ${SANDBOX_API_KEY:-dify-sandbox}
  SANDBOX_GIN_MODE: ${SANDBOX_GIN_MODE:-release}
  SANDBOX_WORKER_TIMEOUT: ${SANDBOX_WORKER_TIMEOUT:-15}
  SANDBOX_ENABLE_NETWORK: ${SANDBOX_ENABLE_NETWORK:-true}
  SANDBOX_HTTP_PROXY: ${SANDBOX_HTTP_PROXY:-http://ssrf_proxy:3128}
  SANDBOX_HTTPS_PROXY: ${SANDBOX_HTTPS_PROXY:-http://ssrf_proxy:3128}
  SANDBOX_PORT: ${SANDBOX_PORT:-8194}
  
```







# docker相关问题

无法访问镜像端口：（原因：未做端口映射）

```yaml
#方法一：修改yaml
#添加端口映射
services:
    # The Weaviate vector store.
    weaviate:
        image: semitechnologies/weaviate:1.19.0
        profiles:
          - ''
          - weaviate
        ports:      
          #TCP 端口映射
          - 8080:8080
        volumes:
          # Mount the Weaviate data directory to the con tainer.
          - ./volumes/weaviate:/var/lib/weaviate
        environment:
```

```sh
# 方法二：启动时指定端口映射
docker run -d -p 原端口号:目标映射端口号 镜像名
#例如
docker run -d -p 8080:8080 semitechnologies/weaviate:1.19.0
```



# docker compose

```shell
docker compose up -d #安装并运行当前路径下dockercompose yaml,项目名字看配置

docker-compose -p 【项目名】 up -d   # 指定项目名字

docker-compose -f 【文件名】 up -d   #指定文件加载
```

## docker-compose.yaml

profile用于控制安装流程，根据输入profile决定是否安装该镜像

```yaml
services:
    weaviate:
        image: semitechnologies/weaviate:1.19.0
        profiles:
          - ''
          - weaviate
        restart: always
        volumes:
          # Mount the Weaviate data directory to the con tainer.
          - ./volumes/weaviate:/var/lib/weaviate
        environment:
          # The Weaviate configurations
          # You can refer to the [Weaviate](https://weaviate.io/developers/weaviate/config-refs/env-vars) documentation for more information.
          PERSISTENCE_DATA_PATH: ${WEAVIATE_PERSISTENCE_DATA_PATH:-/var/lib/weaviate}
          QUERY_DEFAULTS_LIMIT: ${WEAVIATE_QUERY_DEFAULTS_LIMIT:-25}
          AUTHENTICATION_ANONYMOUS_ACCESS_ENABLED: ${WEAVIATE_AUTHENTICATION_ANONYMOUS_ACCESS_ENABLED:-false}
          DEFAULT_VECTORIZER_MODULE: ${WEAVIATE_DEFAULT_VECTORIZER_MODULE:-none}
          CLUSTER_HOSTNAME: ${WEAVIATE_CLUSTER_HOSTNAME:-node1}
          AUTHENTICATION_APIKEY_ENABLED: ${WEAVIATE_AUTHENTICATION_APIKEY_ENABLED:-true}
          AUTHENTICATION_APIKEY_ALLOWED_KEYS: ${WEAVIATE_AUTHENTICATION_APIKEY_ALLOWED_KEYS:-WVF5YThaHlkYwhGUSmCRgsX3tD5ngdN8pkih}
          AUTHENTICATION_APIKEY_USERS: ${WEAVIATE_AUTHENTICATION_APIKEY_USERS:-hello@dify.ai}
          AUTHORIZATION_ADMINLIST_ENABLED: ${WEAVIATE_AUTHORIZATION_ADMINLIST_ENABLED:-true}
          AUTHORIZATION_ADMINLIST_USERS: ${WEAVIATE_AUTHORIZATION_ADMINLIST_USERS:-hello@dify.ai}
```

上述操作表示当输入

```sh
docker-compose up -d 
或者
docker-compose --profile weaviate up -d
```

安装weaviate镜像



# docker镜像制作





## dockerfile编写

类似于shell脚本

主要有RUN

COPY

ENV

ENTRYPOINT





ENV用于定义环境变量

COPY复制目录

RUN执行脚本命令，比如要执行shell指令就直接RUN + shell指令

ENTRYPOINT用于定义程序启动入口

一般是设置为.sh脚本（脚本内写启动代码）



## dockerignore编写



该文件类似于gitignore，里面记录的都是需要忽略的文件/文件夹



## docker镜像编译

编写完dockerfile之后，最好切换到需要打包的目录，方便找环境

docker会自动读取环境下的dockerfile和.dockerignore文件

执行

```sh
docker build 目标文件夹路径 [-f path/dockerfile] [-o 输出目录 ] [-t 镜像名:镜像标签] [--no-cache #不使用build缓存，重新编译]
```

比如

```sh
 docker build  -t "jxepdi-api:latest"  . --no-cache
 docker build  -t jxepdi-plugin:latest  -f local.dockerfile ..
```

上面命令中的·表示构建上下文路径是当前目录（Docker 会把当前目录作为上下文发送给构建引擎）

若要指定某个dockerfile 加上 -f参数



如果加了-O参数不会导出镜像，只会将文件写入到某个特定文件夹



完成构建后，导出镜像

```sh
docker save -o myimage_latest.tar myimage:latest
```





## docker 镜像加载



- 在目标服务器上，使用命令 docker load -i images.tar，先加载镜像，然后再compose文件内调用即可

