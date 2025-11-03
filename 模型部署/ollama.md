## 常用

```sh
#启动服务
ollama serve


#显示模型列表
ollama list

#显示模型参数
ollama show qwen2.5:7b

#显示模型配置信息
ollama show --modelfile qwen2.5:7b 

#把配置信息写入到文件
ollama show --modelfile qwen2.5:7b > Modelfile

#按照modefile添加本地模型
ollama create qwen2.5:7b-custom -f Modelfile
```



## 其他

```sh
#重新载入 systemd 配置：
systemctl daemon-reload

# 启动服务
systemctl start ollama

#设置开机自启
systemctl enable ollama



#查看服务状态
systemctl status ollama

```



## 环境变量

OLLAMA_HOST：这个变量定义了Ollama监听的网络接口。通过设置OLLAMA_HOST=0.0.0.0，我们可以让Ollama监听所有可用的网络接口，从而允许外部网络访问。

OLLAMA_MODELS：这个变量指定了模型镜像的存储路径。通过设置OLLAMA_MODELS=F:\OllamaCache，我们可以将模型镜像存储在E盘，避免C盘空间不足的问题。

OLLAMA_KEEP_ALIVE：这个变量控制模型在内存中的存活时间。设置OLLAMA_KEEP_ALIVE=24h可以让模型在内存中保持24小时，提高访问速度。

OLLAMA_PORT：这个变量允许我们更改Ollama的默认端口。例如，设置OLLAMA_PORT=8080可以将服务端口从默认的11434更改为8080。

OLLAMA_NUM_PARALLEL：这个变量决定了Ollama可以同时处理的用户请求数量。设置OLLAMA_NUM_PARALLEL=4可以让Ollama同时处理两个并发请求。

OLLAMA_MAX_LOADED_MODELS：这个变量限制了Ollama可以同时加载的模型数量。设置OLLAMA_MAX_LOADED_MODELS=4可以确保系统资源得到合理分配。



### 如何操作

windows

直接配置环境变量



linux配置ollama.service

[ollama | 环境变量配置以及本地部署 - 知乎](https://zhuanlan.zhihu.com/p/24426375604)

```sh
vim /etc/systemd/system/ollama.service
```



```
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/local/bin/ollama serve
User=ollama
Group=ollama
Restart=always
RestartSec=3
Environment="OLLAMA_MODELS=/data/ollama/models"
Environment="OLLAMA_HOST=0.0.0.0"
[Install]
WantedBy=default.target
```

最后重新加载配置文件，重启服务

```text
systemctl daemon-reload 
systemctl restart ollama
```





## 修改上下文数量

[Ollama介绍 -- Ollama 中文文档|Ollama官方文档](https://ollama.cadn.net.cn/faq.html)（全都写在里面）



1.修改配置文件，重建模型

[在Ollama中修改模型支持的上下文token数 - 知乎](https://zhuanlan.zhihu.com/p/24240837427)

2.使用额外参数：

```python
import requests
 
url = "http://10.10.40.102:10434/api/chat"
 
payload = {
    "model": "qwen2.5:14b-instruct-q8_0",
    "stream": False,
    "messages": [
        {
            "role": "system",
            "content": """你可以根据需要修改这个系统提示""",
        },
        {"role": "user", "content": "如何快速创建可视化应用"},
    ],
    "options": {"num_ctx": 8192}
}
 
response = requests.post(url, json=payload)
print(response.text)
```



openai api做不到：[OpenAI 兼容性 -- Ollama 中文文档|Ollama官方文档](https://ollama.cadn.net.cn/openai.html)





3.配置环境变量

0.5.13以上才有



## 并发

Ollama 支持两个级别的并发处理。如果您的系统有足够的可用内存（使用 CPU 推理时的系统内存，或用于 GPU 推理的 VRAM），则可以同时加载多个模型。对于给定模型，如果在加载模型时有足够的可用内存，则会将其配置为允许并行请求处理。

如果在已加载一个或多个模型的情况下**没有足够的可用内存**来加载新模型请求，则所有新请求都将**排队**，直到可以加载新模型。当以前的模型变为空闲状态时，将卸载一个或多个模型，以便为新模型腾出空间。排队的请求将按顺序处理。使用 GPU 推理时，新模型必须能够完全适应 VRAM 以允许并发模型加载。



给定模型的并行请求处理会导致上下文大小增加并行请求的数量。例如，具有 4 个并行请求的 2K 上下文将导致 8K 上下文和额外的内存分配。



以下服务器设置可用于调整 Ollama 在大多数平台上处理并发请求的方式：

- `OLLAMA_MAX_LOADED_MODELS`- 如果模型适合可用内存，则可以同时加载的最大模型数。默认值为 3 * GPU 数量或 3 用于 CPU 推理。
- `OLLAMA_NUM_PARALLEL`- 每个模型将同时处理的最大并行请求数。默认值将根据可用内存自动选择 4 或 1。
- `OLLAMA_MAX_QUEUE`- Ollama 在忙碌时在拒绝其他请求之前将排队的最大请求数。默认值为 512
