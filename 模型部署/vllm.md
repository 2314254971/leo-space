官网：
https://docs.vllm.com.cn/en/latest/serving/engine_args.html

不写port参数默认是8000端口



# 聊天

```shell
CUDA_VISIBLE_DEVICES=0 vllm serve DeepSeek-R1-Distill-Qwen-32B/ --max-model-len 16384

CUDA_VISIBLE_DEVICES=0 nohup vllm serve DeepSeek-R1-Distill-Llama-70B-AWQ/ --max-model-len 107920 >vllm2.log
```


# 重排序
```shell
CUDA_VISIBLE_DEVICES=0 vllm serve  /mnt/d/RAG/bge-reranker-v2-m3/ --task score --served-model-name bge-reranker
CUDA_VISIBLE_DEVICES=0 nohup vllm serve  /mnt/d/RAG/bge-reranker-v2-m3/ --task score --served-model-name bge-reranker > rerank.log


```



#  嵌入

```shell
CUDA_VISIBLE_DEVICES=0 vllm serve --model /path/to/embed-model \
	--task embedding\
           --port 8001 \
           --served-model-name "bge-m3" 

CUDA_VISIBLE_DEVICES=0 nohup vllm serve  ./bge-m3 \
 	--task embedding\
                --port 8001 \
                --served-model-name "bge-m3" > bgem3.log
```



# 多个模型


nohup python main.py > test.log