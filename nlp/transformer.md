## 总体结构

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3319e3d6922a2e7f2499a3130d3b5925.png#pic_center)

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/20c5baff36eedc6100d9f107e4fe3c95.png#pic_center)

### 编码器

下一个编码器的输入是上一个编码器的输出（只有第一个编码器需要embed输入文本）

![img](https://pic4.zhimg.com/v2-a994c2267478c074af8a4c80ee060e01_1440w.jpg)

![img](https://picx.zhimg.com/v2-2274e5f5d5a7b035665dce0b1db6818f_1440w.jpg)

#### Add & Norm

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/29a24a78b70aa77ffd41b5ae2bfdc5e7.png#pic_center)

Add，就是在z的基础上加了一个残差块X，加入残差块的目的是为了防止在深度神经网络的训练过程中发生退化的问题，退化的意思就是深度神经网络通过增加网络的层数，Loss逐渐减小，然后趋于稳定达到饱和，然后再继续增加网络层数，Loss反而增大。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b9951837dd639046ca37c9fba8b4efc5.png#pic_center)



### 解码器

- 包含两个Multi-Head Attention
- 第一个Multi-Head Attention采用了Masked操作
- 第二个Multi-Head Attention的 ![\bm{K},\bm{V}](https://www.zhihu.com/equation?tex=%5Cbm%7BK%7D%2C%5Cbm%7BV%7D&consumer=ZHI_MENG) 矩阵使用Encoder的**编码信息矩阵**![\bm{C}](https://www.zhihu.com/equation?tex=%5Cbm%7BC%7D&consumer=ZHI_MENG) 进行计算，而 ![\bm{Q}](https://www.zhihu.com/equation?tex=%5Cbm%7BQ%7D&consumer=ZHI_MENG) 使用上一个 Multi-Head Attention的输出计算





### 多头注意力

#### 自注意力机制

自注意力会关注整个输入序列的所有单词

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/49c88545fae57dbf255c8ab9fd279110.png#pic_center)

计算 Self Attention 的第 1 步是：对输入编码器的每个词向量，都创建 3 个向量，分别是：Query 向量，Key 向量，Value 向量。这 3 个向量是词向量分别和 3 个矩阵相乘得到的，而**这个矩阵是我们要学习的参数**。

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d027e1a13de1965169178cb2e5eb9ec1.png#pic_center)

如上图所示输入的向量乘以权重矩阵得到KQV三个向量矩阵

第 2 步：是计算 Attention Score（注意力分数）。假设我们现在计算第一个词Thinking的 Attention Score（注意力分数），需要根据Thinking这个词，对句子中的**其他每个词都计算一个分数**。这些分数决定了我们在编码Thinking这个词时，需要对句子中其他位置的每个词放置多少的注意力。

这些分数，是通过计算 "Thinking" 对应的 Query 向量和其他位置的每个词的 Key 向量的点积，而得到的。如果我们计算句子中第一个位置单词的 Attention Score（注意力分数），那么第一个分数就是 q1 和 k1 的内积，第二个分数就是 q1 和 k2 的点积。

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/59ee8dec83e584b860869ef701a3a4e3.png)

第 3 步，就是把每个分数除以（是 Key 向量的长度）。你也可以除以其他数，除以一个数是为了在反向传播时，求取梯度更加稳定。

第 4 步，接着把这些分数经过一个 Softmax 层，Softmax可以将分数归一化，这样使得分数都是正数并且加起来等于 1。

这些分数决定了在编码当前位置（这里的例子是第一个位置）的词时，对所有位置的词分别有多少的注意力。很明显，在上图的例子中，当前位置（这里的例子是第一个位置）的词会有最高的分数，但有时，关注到其他位置上相关的词也很有用。

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/00994ceb6bf9e66db19611c496463364.png#pic_center)

第 5 步，得到每个位置的分数后，将每个分数分别与每个 Value 向量相乘。这种做法背后的直觉理解就是：对于分数高的位置，相乘后的值就越大，我们把更多的注意力放到了它们身上；对于分数低的位置，相乘后的值就越小，这些位置的词可能是相关性不大的，这样我们就忽略了这些位置的词。

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e976d386a1aad85c2efb7fc965099c27.png#pic_center)



#### 自注意力机制理解

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/86e992ddc9aa426cab226972c0d88b6f.png)

自注意力机制（Self-Attention）是Transformer模型中的核心组成部分之一，它允许模型在处理序列数据时，能够捕捉序列内部的长距离依赖关系。自注意力机制的核心思想是让模型在处理序列的每个元素时，考虑该元素与序列中其他所有元素的关系。具体来说，它通过计算元素之间的相似度，来决定它们之间的权重，从而实现信息的聚合。在Transfoemer中的注意力评分函数使用的是缩放点积注意力。

图中可以看出，当我们输入一个句子’我爱吃梨‘时，首先将其编码为4x512维，然后其会分别通过三个全连接网络进行映射得到Q,K,V(图中省略了经过全连接层的过程），接着Q乘以K的转置得到4x4的权重图，权重图的每一行会做softmax操作最终得到4x4的注意力权重图，在这个注意力权重图中，第1行的4个值分别代表了句子中的’我’字与’我‘，’爱‘，’吃‘，’梨‘这4个字的关联程度，其与自身的关联程度最大（标注红色），其他几行同理。接着注意力权重图与V相乘得到最终结果。在输出的最终结果中，其第一行的每一个元素都是由其他位置的词向量的对应维度进行加权和得到的。(换句话说，每一行的值是由所有行按照注意力权重加权得到的，即每一行都或多或少的包含了其他行的信息。）
 因此，总的来说，Transformers中的自注意力机制使得模型能够同时考虑输入序列中的所有位置，允许模型根据输入序列中的不同位置之间的关系，对每个位置进行加权处理，从而捕捉全局上下文信息。

参考：[Transformer多头自注意力及掩码机制详解_掩码多头注意力机制-CSDN博客](https://blog.csdn.net/m0_64148253/article/details/140424469)

#### 掩码自注意力机制（masked multi head attention）

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/00370e15d470400fbeb4a88347f73e17.png)

**填充mask**

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2485b7b9ce374c8baca372500ce5c3b3.png)

**因果mask**

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2485b7b9ce374c8baca372500ce5c3b3.png)

填充mask: 我们在给transformer输入句子时通常是一次性输入好几个句子（batch)，每个句子的长度不相同，为了transformer能够更好的一次性处理这些长度不同的句子，我们通常要对句子进行填充，使这些句子的长度相同（比如在句子末尾填充0）。但是在计算注意力的时候，这些填充的部分不应该参与计算，所以我们要在注意力权重进行softmax之前要把填充部分进行mask（就是把填充部分变成负无穷），使填充部分的注意力权重在经过softmax后无限接近0。
因果mask: 在解码器训练的过程中，不能让模型知道未来时间步的信息，否则就相当于告诉了模型的最终答案是什么。例如我们给解码器输入’I like eating pears’，当我们在计算’like’这个词与与其他词的注意力权重时，因为解码器是一个单词一个单词预测的，在预测’like‘这个词时是不应该知道后面的单词，所以与’like‘后面的单词不应该产生关联性。

原文链接：https://blog.csdn.net/m0_64148253/article/details/140424469

![img](https://pic3.zhimg.com/v2-c0f64bb363bc16ce1bc091c76399564e_1440w.webp?consumer=ZHI_MENG)

多头注意力由多个自注意力模块组成，模型关注不同位置的能力。

在多头注意力机制中，我们为每组注意力维护单独的 WQ, WK, WV 权重矩阵。将输入 X 和每组注意力的WQ, WK, WV 相乘，得到 8（默认值） 组 Q, K, V 矩阵。

流程如下：![img](https://pic4.zhimg.com/v2-7e43ec9d42e79b6f134ddfc6604409b3_1440w.jpg)

首先将输入 ![\bm{X}](https://www.zhihu.com/equation?tex=%5Cbm%7BX%7D&consumer=ZHI_MENG) 分别传递到 ![h](https://www.zhihu.com/equation?tex=h&consumer=ZHI_MENG) 个不同的Self-Attention中，计算得到 ![h](https://www.zhihu.com/equation?tex=h&consumer=ZHI_MENG) 个输出矩阵 ![\bm{Z}](https://www.zhihu.com/equation?tex=%5Cbm%7BZ%7D&consumer=ZHI_MENG) 。上图是 ![h=8](https://www.zhihu.com/equation?tex=h%3D8&consumer=ZHI_MENG) 的情况，此时会得到 8 个输出矩阵 ![\bm{Z}](https://www.zhihu.com/equation?tex=%5Cbm%7BZ%7D&consumer=ZHI_MENG) 。

得到8个输出矩阵 ![\bm{Z}_0\sim \bm{Z}_7](https://www.zhihu.com/equation?tex=%5Cbm%7BZ%7D_0%5Csim+%5Cbm%7BZ%7D_7&consumer=ZHI_MENG) 后，Multi-Head Attention将它们拼接在一起（Concat），然后传入一个Linear层，得到Multi-Head Attention最终的输出矩阵 ![\bm{Z}](https://www.zhihu.com/equation?tex=%5Cbm%7BZ%7D&consumer=ZHI_MENG) 。



### 输入

文本嵌入



### 位置编码

精品博客：[一文通透位置编码：从标准位置编码、旋转位置编码RoPE到ALiBi、LLaMA 2 Long(含NTK-aware简介)-CSDN博客](https://blog.csdn.net/v_JULY_v/article/details/134085503)



#### onehot



#### 正弦编码（Sinusoidal）

positional encoding的公式如下

![PE_{(pos,2i+1)} = cos\left ( \frac{pos}{10000^{\frac{2i}{d_{model}}}} \right )](https://latex.csdn.net/eq?PE_%7B%28pos%2C2i&plus;1%29%7D%20%3D%20cos%5Cleft%20%28%20%5Cfrac%7Bpos%7D%7B10000%5E%7B%5Cfrac%7B2i%7D%7Bd_%7Bmodel%7D%7D%7D%7D%20%5Cright%20%29)

![PE_{(pos,2i)} = sin\left ( \frac{pos}{10000^{\frac{2i}{d_{model}}}} \right )](https://latex.csdn.net/eq?PE_%7B%28pos%2C2i%29%7D%20%3D%20sin%5Cleft%20%28%20%5Cfrac%7Bpos%7D%7B10000%5E%7B%5Cfrac%7B2i%7D%7Bd_%7Bmodel%7D%7D%7D%7D%20%5Cright%20%29)

pos相当于是**每个token**在**整个序列中的位置**，相当于是0, 1, 2, 3...(看序列长度是多大，比如10，比如100)

![d_{model}](https://latex.csdn.net/eq?d_%7Bmodel%7D)代表位置向量的维度(也是词embedding的维度，transformer论文中设置的512维) 

![i](https://latex.csdn.net/eq?i)是**embedding向量**的**位置下标**对2求商并取整(可用双斜杠![//](https://latex.csdn.net/eq?//)表示整数除法，即求商并取整)，它的取值范围是![[0,...,\frac{d_{model}}{2}]](https://latex.csdn.net/eq?%5B0%2C...%2C%5Cfrac%7Bd_%7Bmodel%7D%7D%7B2%7D%5D)，比如

| **位置向量的第多少维** (0 2 4等偶数维用sin函数计算) | **![i](https://latex.csdn.net/eq?i)**                        | **![2i](https://latex.csdn.net/eq?2i)**           | **![2i+1](https://latex.csdn.net/eq?2i+1)**                  |
| --------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------- | ------------------------------------------------------------ |
| 0                                                   | ![i = 0 // 2 = 0](https://latex.csdn.net/eq?i%20%3D%200%20//%202%20%3D%200) | ![2i = 0](https://latex.csdn.net/eq?2i%20%3D%200) |                                                              |
| 1                                                   | ![i = 1 //2 =0](https://latex.csdn.net/eq?i%20%3D%201%20//2%20%3D0) | ![2i = 0](https://latex.csdn.net/eq?2i%20%3D%200) | ![2i+1 = 1](https://latex.csdn.net/eq?2i&plus;1%20%3D%201)   |
| 2                                                   | ![i = 2 // 2 = 1](https://latex.csdn.net/eq?i%20%3D%202%20//%202%20%3D%201) | ![2i = 2](https://latex.csdn.net/eq?2i%20%3D%202) |                                                              |
| 3                                                   | ![i = 3 // 2 = 1](https://latex.csdn.net/eq?i%20%3D%203%20//%202%20%3D%201) | ![2i = 2](https://latex.csdn.net/eq?2i%20%3D%202) | ![2i+1 = 3](https://latex.csdn.net/eq?2i&plus;1%20%3D%203)   |
| 4                                                   | ![i = 4 // 2 = 2](https://latex.csdn.net/eq?i%20%3D%204%20//%202%20%3D%202) | ![2i = 4](https://latex.csdn.net/eq?2i%20%3D%204) |                                                              |
| 5                                                   | ![i = 5//2 = 2](https://latex.csdn.net/eq?i%20%3D%205//2%20%3D%202) | ![2i = 4](https://latex.csdn.net/eq?2i%20%3D%204) | ![2i + 1 =5](https://latex.csdn.net/eq?2i%20&plus;%201%20%3D5) |
| 6                                                   |                                                              |                                                   |                                                              |

相当于
![2i](https://latex.csdn.net/eq?2i)是指向量维度中的**偶数维**，即第0维、第2维、第4维...，第510维，用**sin函数**计算
![2i+1](https://latex.csdn.net/eq?2i&plus;1) 是向量维度中的**奇数维**，即第1维、第3维、第5维..，第511维，用**cos函数**计算

##### 解释

当对![pos = 0](https://latex.csdn.net/eq?pos%20%3D%200)上的单词「我」进行位置编码时，它本身的维度有512维

- 维
  ![PE_0 = [sin(\frac{0}{10000^{\frac{0}{512}}}),cos(\frac{0}{10000^{\frac{0}{512}}}), sin(\frac{0}{10000^{\frac{2}{512}}}),cos(\frac{0}{10000^{\frac{2}{512}}}), sin(\frac{0}{10000^{\frac{4}{512}}}), cos(\frac{0}{10000^{\frac{4}{512}}}),..., sin(\frac{0}{10000^{\frac{510}{512}}}),cos(\frac{0}{10000^{\frac{510}{512}}})]](https://latex.csdn.net/eq?PE_0%20%3D%20%5Bsin%28%5Cfrac%7B0%7D%7B10000%5E%7B%5Cfrac%7B0%7D%7B512%7D%7D%7D%29%2Ccos%28%5Cfrac%7B0%7D%7B10000%5E%7B%5Cfrac%7B0%7D%7B512%7D%7D%7D%29%2C%20sin%28%5Cfrac%7B0%7D%7B10000%5E%7B%5Cfrac%7B2%7D%7B512%7D%7D%7D%29%2Ccos%28%5Cfrac%7B0%7D%7B10000%5E%7B%5Cfrac%7B2%7D%7B512%7D%7D%7D%29%2C%20sin%28%5Cfrac%7B0%7D%7B10000%5E%7B%5Cfrac%7B4%7D%7B512%7D%7D%7D%29%2C%20cos%28%5Cfrac%7B0%7D%7B10000%5E%7B%5Cfrac%7B4%7D%7B512%7D%7D%7D%29%2C...%2C%20sin%28%5Cfrac%7B0%7D%7B10000%5E%7B%5Cfrac%7B510%7D%7B512%7D%7D%7D%29%2Ccos%28%5Cfrac%7B0%7D%7B10000%5E%7B%5Cfrac%7B510%7D%7B512%7D%7D%7D%29%5D)

- 当对$pos = 1$上的单词「爱」进行位置编码时，它本身的维度有512维

  ![img](https://i-blog.csdnimg.cn/blog_migrate/e03c8426caaa47279adb2d34784668a8.png)

![PE_1 = [sin(\frac{1}{10000^{\frac{0}{512}}}),cos(\frac{1}{10000^{\frac{0}{512}}}), sin(\frac{1}{10000^{\frac{2}{512}}}),cos(\frac{1}{10000^{\frac{2}{512}}}), sin(\frac{1}{10000^{\frac{4}{512}}}), cos(\frac{1}{10000^{\frac{4}{512}}}),..., sin(\frac{1}{10000^{\frac{510}{512}}}),cos(\frac{1}{10000^{\frac{510}{512}}})]](https://latex.csdn.net/eq?PE_1%20%3D%20%5Bsin%28%5Cfrac%7B1%7D%7B10000%5E%7B%5Cfrac%7B0%7D%7B512%7D%7D%7D%29%2Ccos%28%5Cfrac%7B1%7D%7B10000%5E%7B%5Cfrac%7B0%7D%7B512%7D%7D%7D%29%2C%20sin%28%5Cfrac%7B1%7D%7B10000%5E%7B%5Cfrac%7B2%7D%7B512%7D%7D%7D%29%2Ccos%28%5Cfrac%7B1%7D%7B10000%5E%7B%5Cfrac%7B2%7D%7B512%7D%7D%7D%29%2C%20sin%28%5Cfrac%7B1%7D%7B10000%5E%7B%5Cfrac%7B4%7D%7B512%7D%7D%7D%29%2C%20cos%28%5Cfrac%7B1%7D%7B10000%5E%7B%5Cfrac%7B4%7D%7B512%7D%7D%7D%29%2C...%2C%20sin%28%5Cfrac%7B1%7D%7B10000%5E%7B%5Cfrac%7B510%7D%7B512%7D%7D%7D%29%2Ccos%28%5Cfrac%7B1%7D%7B10000%5E%7B%5Cfrac%7B510%7D%7B512%7D%7D%7D%29%5D)

 然后再叠加上embedding向量，可得

![img](https://i-blog.csdnimg.cn/blog_migrate/2dadd500f2f2a35d7815d4223c3b3779.png)

其他位置同理

##### 代码实现

```python
“”“位置编码的实现，调用父类nn.Module的构造函数”“”
class PositionalEncoding(nn.Module):
    def __init__(self, d_model, dropout, max_len=5000):
        super(PositionalEncoding, self).__init__()  
        self.dropout = nn.Dropout(p=dropout)  # 初始化dropout层
        
        # 计算位置编码并将其存储在pe张量中
        pe = torch.zeros(max_len, d_model)                # 创建一个max_len x d_model的全零张量
        position = torch.arange(0, max_len).unsqueeze(1)  # 生成0到max_len-1的整数序列，并添加一个维度
        # 计算div_term，用于缩放不同位置的正弦和余弦函数
        div_term = torch.exp(torch.arange(0, d_model, 2) *
                             -(math.log(10000.0) / d_model))
 
        # 使用正弦和余弦函数生成位置编码，对于d_model的偶数索引，使用正弦函数；对于奇数索引，使用余弦函数。
        pe[:, 0::2] = torch.sin(position * div_term)
        pe[:, 1::2] = torch.cos(position * div_term)
        pe = pe.unsqueeze(0)                  # 在第一个维度添加一个维度，以便进行批处理
        self.register_buffer('pe', pe)        # 将位置编码张量注册为缓冲区，以便在不同设备之间传输模型时保持其状态
        
    # 定义前向传播函数
    def forward(self, x):
        # 将输入x与对应的位置编码相加
        x = x + Variable(self.pe[:, :x.size(1)], 
                         requires_grad=False)
        # 应用dropout层并返回结果
        return self.dropout(x)
```

max_len相当于模型上下文长度

模型在训练和推理时，计算位置编码一般都是直接计算出$max_len * d_model$大小的矩阵，其中不足的会进行padding，超出的会进行截断

注：

计算div_term使用的是等价运算

代码对应的公式为

![img](https://i-blog.csdnimg.cn/blog_migrate/75e7ae395d7aaf62581d1f65f08e83bc.png)

其中的中括号对应的是一个从 0 到 ![d_{\text{model}} - 1](https://latex.csdn.net/eq?d_%7B%5Ctext%7Bmodel%7D%7D%20-%201) 的等差数列(步长为 2)，设为![i](https://latex.csdn.net/eq?i)

且上述公式与这个公式是等价的

![img](https://i-blog.csdnimg.cn/blog_migrate/0e8b55fabfae6a5d9f996cc390626040.png)

为何，原因在于![a^x=e^{(x\cdot ln(a))}](https://latex.csdn.net/eq?a%5Ex%3De%5E%7B%28x%5Ccdot%20ln%28a%29%29%7D)，从而有![10000^{-\frac{i}{d_{model}}}=e^{(-\frac{i}{d_{model}}\cdot log(10000))}](https://latex.csdn.net/eq?10000%5E%7B-%5Cfrac%7Bi%7D%7Bd_%7Bmodel%7D%7D%7D%3De%5E%7B%28-%5Cfrac%7Bi%7D%7Bd_%7Bmodel%7D%7D%5Ccdot%20log%2810000%29%29%7D)

##### 性质

- 具有**相对位置表达能力**：Sinusoidal可以学习到相对位置，对于固定位置距离的k，PE(i+k)可以表示成PE(i)的线性函数。
- 两个**位置向量的内积只和相对位置 k 有关**。
- Sinusoidal编码具有**对称性**。
- 随着k的增加，内积的结果会直接减少，即会存在**远程衰减**。

证明：[(26 封私信 / 80 条消息) 再论大模型位置编码及其外推性（万字长文） - 知乎](https://zhuanlan.zhihu.com/p/675243992)

##### 为什么选择参数base=10000

可以从sin/cos的周期性角度进行分析。分析周期可以发现，维度i的周期为 ，其中0 <= i < d，因此周期T的范围是 。

固定d为100，维度i=10，绘制不同base下的position embedding，如下图所示：

![img](https://pic4.zhimg.com/v2-c383efe41543f46a7a045f1b8e03ef8b_1440w.jpg)

个人理解：

$PE_{(pos,2i+1)} = cos\left ( \frac{pos}{10000^{\frac{2i}{d_{model}}}} \right )$,式中$10000^{\frac{2i}{d_{model}}}$可以看作角频率，角频率越慢，周期越长，

base越大，周期越大。而周期越大，在position从0~100范围内，只覆盖了不到半个周期，这使得重复值少；而周期小的，覆盖了多个周期，就导致不同位置有大量重复的值。

##### 正弦编码是否真的具备外推性？

似乎Sinusoidal只和相对位置有关。但是实际的Attention计算中还需要与attention的权重W相乘，即${P}{E_{t}^{T}}{W_{q}^{T}}{W_{k}}{P}{E_{t+k}} $，这时候**内积的结果**就**不能**反映相对距离



#### 旋转位置编码（RoPE）



##### 数学原理

**RoPE如何解决内积只和相对位置有关呢？**

论文中提出为了能利用上 token 之间的相对位置信息，假定 query 向量 ![\bm{q}_m](https://www.zhihu.com/equation?tex=%5Cbm%7Bq%7D_m&consumer=ZHI_MENG) 和 key 向量 ![\bm{k}_n](https://www.zhihu.com/equation?tex=%5Cbm%7Bk%7D_n&consumer=ZHI_MENG) 之间的内积操作可以被一个函数 ![g](https://www.zhihu.com/equation?tex=g&consumer=ZHI_MENG) 表示，该函数 ![g](https://www.zhihu.com/equation?tex=g&consumer=ZHI_MENG) 的输入是词嵌入向量 ![\bm{x}_m](https://www.zhihu.com/equation?tex=%5Cbm%7Bx%7D_m&consumer=ZHI_MENG) ， ![\bm{x}_n](https://www.zhihu.com/equation?tex=%5Cbm%7Bx%7D_n&consumer=ZHI_MENG) 和它们之间的相对位置 ![m-n](https://www.zhihu.com/equation?tex=m-n&consumer=ZHI_MENG) ：

$$\left<{f}_q({x}_m,m),f_k({x}_n,n)\right>=g({x}_m,{x}_n,m-n)$$

目标就是**找到**一个等价的位置编码方式，从而使得上述**关系成立**。（左边算是q和k向量的内积，而这恰好是transformer计算自注意力机制的核心一步，右边等式则意味着m与n的相对位置。如此一来，该等式便把“q和k的内积”与“它们的相对位置”给串起来了）



最后提出了一个满足上述关系的 ![f](https://www.zhihu.com/equation?tex=f&consumer=ZHI_MENG) 和 ![g](https://www.zhihu.com/equation?tex=g&consumer=ZHI_MENG) 的形式如下：

![f_q(\bm{x}_m,m)=\left(\bm{W}_q\bm{x}_m\right)e^{im\theta} \\ f_k(\bm{x}_n,n)=(\bm{W}_k\bm{x}_n)e^{in\theta} \\ g(\bm{x}_m,\bm{x}_n,m-n)=\text{Re}\left[(\bm{W}_q\bm{x}_m)(\bm{W}_k\bm{x}_n)^{*}e^{i(m-n)\theta}\right] \\\tag8](https://www.zhihu.com/equation?tex=f_q%28%5Cbm%7Bx%7D_m%2Cm%29%3D%5Cleft%28%5Cbm%7BW%7D_q%5Cbm%7Bx%7D_m%5Cright%29e%5E%7Bim%5Ctheta%7D+%5C%5C+f_k%28%5Cbm%7Bx%7D_n%2Cn%29%3D%28%5Cbm%7BW%7D_k%5Cbm%7Bx%7D_n%29e%5E%7Bin%5Ctheta%7D+%5C%5C+g%28%5Cbm%7Bx%7D_m%2C%5Cbm%7Bx%7D_n%2Cm-n%29%3D%5Ctext%7BRe%7D%5Cleft%5B%28%5Cbm%7BW%7D_q%5Cbm%7Bx%7D_m%29%28%5Cbm%7BW%7D_k%5Cbm%7Bx%7D_n%29%5E%7B%2A%7De%5E%7Bi%28m-n%29%5Ctheta%7D%5Cright%5D+%5C%5C%5Ctag8&consumer=ZHI_MENG)

这里面 Re 表示复数的实部。



理解细节查看：（通过欧拉公式理解）

[十分钟读懂旋转编码（RoPE）](https://www.zhihu.com/tardis/bd/art/647109286)

[(26 封私信 / 80 条消息) 一文看懂 LLaMA 中的旋转式位置编码（Rotary Position Embedding） - 知乎](https://zhuanlan.zhihu.com/p/642884818)



简洁推导：

假设Ra表示角度为a的旋转矩阵，那么R具有如下性质：

1. Ra^T = R(-a)
2. Ra Rb = R(a+b)


回到旋转位置编码，我们可以去证明 <RaX, RbY> = <X, R(b-a)Y> ，证明如下：
<RaX, RbY>
= (RaX)^T RbY
= X^T Ra^T RbY
= X^T R(b-a) Y
= <X, R(b-a)Y>

##### 性质







#### 相关基础

##### 复数

[复数（向量）的矩阵表达_复数的矩阵形式-CSDN博客](https://blog.csdn.net/qq_25814297/article/details/129419585)

[复数的矩阵表示 - Invo1 - 博客园](https://www.cnblogs.com/invo/p/18243532)

简单来说

复数有：
$$
c=a+bi
$$
对应的，拓展到矩阵
$$
C= AE + BI
$$
其中A为实部，E为单位矩阵，B为虚部，I为虚数矩阵

这个矩阵又是怎么来的呢？
$$
i^2 = -1\\
I^2= -E  && I^3 = -I && I^4 = I
$$
所以推出虚数矩阵(以二维为例)
$$
I= \begin{bmatrix}
0&1\\
-1&0\\
\end{bmatrix}\\
\Rightarrow 
a+bi \sim \begin{bmatrix}
a&b\\
-b&a\\
\end{bmatrix} 
=\begin{bmatrix}
Re(z)&Im(z)\\
-Im(z)&Re(z)\\
\end{bmatrix}
$$

##### 欧拉公式

![e^{i x}=\cos x+i \sin x](https://latex.csdn.net/eq?e%5E%7Bi%20x%7D%3D%5Ccos%20x&plus;i%20%5Csin%20x)



#### 外推性

[(26 封私信 / 80 条消息) 再论大模型位置编码及其外推性（万字长文） - 知乎](https://zhuanlan.zhihu.com/p/675243992)

虽然RoPE理论上可以编码任意长度的绝对位置信息，并且sin/cos计算就能将任意长度的相对位置信息表达出来，但是实验发现RoPE仍然存在外推问题，即测试长度超过训练长度之后，模型的效果会有显著的崩坏，具体表现为困惑度（Perplexity，PPL）等指标显著上升。



位置线性内插（Position Interpolation，PI）
传统的RoPE算法在超出最大距离后，PPL就会爆炸，因此直接推广的效果一定是很差的，因此Position Interpolation绕过了推广的限制，线性内插的核心思想是通过**缩放位置索引**。

$f^{ \prime} \left( x,m \right)=f \left( x, \frac{mL}{L^{ \prime}} \right)$

其中 x 是 token embedding，m 是位置。对于一个超过之前最大长度 L 的样本 (长度为 L’)，通过缩放将所有位置映射回 L 区间，每个位置都不再是整数。通过这种方式在长样本区间训练 1000 步，就能很好的应用到长样本上。

总结下来就是把token位置映射到原区间【a',b'】-》【a,b】



NTK-Aware Scaled RoPE

线性内插虽然效果不错，但是插值方法是线性的，这种方法在处理相近的token时可能无法准确区分它们的顺序和位置。

公式如下：

$\begin{split}&{A}_{t,s}=\mathrm{Re}\left[\sum_{i=0}^{d/2-1} \tilde{q}_{t}^{ \tiny{(i)} }\tilde{k}_{s}^{ \tiny{(i) } ^{*}}e^{\mathrm{i}(t-s)\theta_{i}}\right]\quad\theta_{i}=\left(10000\cdot \alpha\right)^{-2i/d}\end{split}$











## 训练和推理

训练

[从零开始实现Transformer（模型搭建+数据处理+训练流程） - 知乎](https://zhuanlan.zhihu.com/p/651736596)



推理

在训练的时候，模型解码器的输入和输出都是一整个句子，通过对输入的句子加入因果mask实现并行训练。那如果已经训练好了一个Transformer模型，使用它做机器翻译推理的时候，我们只有编码器的输入，那么解码器的输入输出是什么呢？

encoder输入：
我爱吃梨 (Transformer编码器只需要编码一次就可以)

| 解码器推理次数 | decoder输入：              | decoder输出：              |
| -------------- | -------------------------- | -------------------------- |
| 1              | ‘BOS’                      | i                          |
| 2              | ‘BOS’ i                    | i love                     |
| 3              | ‘BOS’ i love               | i love eatting             |
| 4              | ‘BOS’ i love eatting       | i love eatting pears       |
| 5              | ‘BOS’ i love eatting pears | i love eatting pears ‘EOS’ |

 通过上面的例子可以看出，在推理过程中transformer的输出采用的是自回归的方式，即将transformer解码器的输出当作输入然后重新去做推理，然后得到下一个输出，直到输出’EOS’。在推理时，会先给解码器一个’BOS’的字作为第一个输入，然后开始自回归输出。这也就是为什么训练时要在输入中加入’BOS’。


## 代码

[Transformer 详解(上) — 编码器【附pytorch代码实现】_transformer 遮掩-CSDN博客](https://blog.csdn.net/qq_43462005/article/details/115640339)

[手撕Transformer！！从每一模块原理讲解到代码实现【超详细！】_手撕 transformer-CSDN博客](https://blog.csdn.net/xiaoh_7/article/details/140019530)





## KV缓存(加速推理)

[为什么Transformer推理要做KV缓存？_推理结果可以做缓存吗-CSDN博客](https://blog.csdn.net/dsxaigc/article/details/141870406)

[大模型推理加速：看图学KV Cache - 知乎](https://zhuanlan.zhihu.com/p/662498827)



![img](https://pic2.zhimg.com/v2-655b95ebfb7808563bead28bc89bb459_1440w.jpg)







## 输入序列长度问题

transformer支持可变长度的文本序列输入，但是即使可变，在同一个batch里面也是相同的长度，不同batch长度可以不同

同一个batch内,长度不足的需要padding操作

[(16 封私信 / 80 条消息) Transformer是如何处理可变长度数据的？ - 知乎](https://www.zhihu.com/question/445895638/answer/2306274526)

[一个小问题：深度学习模型如何处理大小可变的输入 - 知乎](https://zhuanlan.zhihu.com/p/379022662)

可变长度和模型结构相关

FCNN可以处理不同尺寸

## 上下文

当前输入+输出同时占用上下文窗口，即**LLM 的 Context Window 指模型在单次推理过程中可处理的全部 token 序列的最大长度**，包括：

1. 输入部分（用户提供的提示词、历史对话内容、附加文档等）
2. 输出部分（模型当前正在生成的响应内容）

，以llama为例，其输出会添加到输入prompt，然后预测下一次的输出。

![706.png](https://h5cdn.dewu.com/efe/ctoo-open-blog-admin/10569101/706.png)

我们来解读下上面 Llama 各层的结构与作用，首先从输入文本开始。会经过下面各层：

- Input Embedding：将 Input 文本转化为向量表，通过 nn.Embedding 实现。
- Llama Decoder Layer：Decoder 采用多层 Llama Decoder Layer。每一层包括自注意力（Llama Attention）和前馈网络（Llama MLP）。自注意力用于捕捉文本中的长程依赖关系。前馈网络进行非线性映射。
- Llama RMSNorm：一种规范化方式，用于正则化每层的输，起到预处理的作用。
- lm_head：一个线性层，将 Decoder 最后一层的输出映射到词典大小的维，以进行后续的语言模型 Logits 计算。
- Llama Attention：多头自注意力机制，用于建模文本中的依赖关系。将输入表示切分为多个头，然后在每个头内做点积注意力运算。
- Llama MLP：采用 Gated Linear Units 的多层前馈网络。进行非线性变换来捕捉复杂模式。

![782.png](https://h5cdn.dewu.com/efe/ctoo-open-blog-admin/10569101/782.png)

通过上面对 Llama 结构的解析，我们看到在 Llama Attention 中有一个叫做 RoPE（旋转位置编码）的层，主要用于对输入进行位置编码，让模型学到输入文本中每个 Token 的位置关系，从而更好地理解输入。RoPE 层能处理序列的长度决定了 Llama 的上下文长度，要扩展 Llama 的上下文长度，需要对 RoPE 层进行改造和扩展。下面我们先简单介绍下 Llama RoPE 层的工作原理。

Llama 的 Attention 计算过程如下：

- 输入会经过线性变换，得到 Query(Q)、Key（K）和 Value（V）矩阵。
- 对 Q 和 K 应用 RoPE 位置编码。RoPE 包含旋转的 Sin 和 Cos 编码，会根据每个 Token 的位置对其表示进行旋转。
- 用旋转后的 Q 和 K 计算点积，得到注意力权重 Attention Score，经过 Softmax 计算后得到 Normalized Attention Weight。
- 再把 Attention Weight 与 V 相乘，并进行加权求和，得到 Attention 的输出。
- 输出再经过一个线性变换，继续输出给下一层作为输入。

这样通过 RoPE 位置编码、加权平均，Llama 的 Attention 可以高效稳定地提取文本序列的上下文语义信息。

### 拓展上下文长度（外推）

要扩展大模型的上下文长度，**就需要扩展 RoPE 层**，也就是扩展其 COS 和 SIN 矩阵，让RoPE支持更长序列的输入。

RoPE 中的 COS 和 SIN 矩阵维度（seq_length, embed_dim），其中 seq_length 就是模型支持的最大序列长度，embed_dim 是词嵌入维度。矩阵中的每个值表示一个位置上的正弦或余弦编码。为了支持更长的上下文，需要重新计算更大尺寸的 COS 和 SIN 矩阵。

对于未训练过的大模型，只需要直接更改其配置文件中的 max_position_embeddings 即可实现 RoPE 层的扩展，然后再进行训练。但是对于已经训练过的模型，如果直接修改其配置，会导致模型的效果急剧下降！

参考：[如何扩展大模型的上下文长度](https://tech.dewu.com/article?id=104)  ！！！！！！！！！！！！！！！！！！！！！！！

总之，需要修改编码策略，或者微调



[大模型核心概念科普：Token、上下文长度、最大输出，一次讲透 - 知乎](https://zhuanlan.zhihu.com/p/27598806064)



### 上下文限制原因

根本原因：**训练时的位置范围是有限的，模型只在这个范围内见过位置编码！**

虽然 RoPE 公式无界，但：

| 阶段         | 限制来源                                                     |
| ------------ | ------------------------------------------------------------ |
| **训练阶段** | 模型只在 `[0, max_position_embeddings - 1]` 范围内学习了位置编码与语义的关联 |
| **推理阶段** | 若输入长度 > `max_position_embeddings`，模型会使用**从未见过的位置索引**，导致注意力混乱 |

> 🧠 类比：就像一个人只学过 0~999 的数字加法，突然让他算 10000+20000，虽然“加法规则”通用，但他没练过，容易错。



`max_position_embeddings` 在代码中到底用在哪？

1. **初始化 RoPE 的频率基底（θ）时作为参考尺度**

虽然 RoPE 公式不显式包含 `max_position_embeddings`，但在**设置频率基底**时，它会影响波长分布的设计。

例如，标准 RoPE 中：
$$
θ_i=10000−2i/d , i = 0,1,…,d/2−1
$$


这里的 `10000` 实际上是根据**预期最大位置**（如 2048 或 4096）经验设定的。
 如果最大位置变大，低频分量可能不足以覆盖长距离依赖 → 出现“高频震荡”或“相位混叠”。

> 🔧 因此，`max_position_embeddings` 隐式决定了 **RoPE 的频率分辨率是否足够覆盖训练序列长度**。



2. **某些实现会预计算 RoPE 缓存（cache）**

为了加速推理，很多框架（如 Hugging Face Transformers、vLLM）会**预先计算好 `[0, max_position_embeddings)` 的所有 RoPE 矩阵**，存为 buffer。

```python
# 伪代码
self.max_seq_len_cached = config.max_position_embeddings
self.cos_cached = cos[:self.max_seq_len_cached]
self.sin_cached = sin[:self.max_seq_len_cached]
```

如果输入长度超过这个值，就会越界报错（除非动态扩展）。

> ⚠️ 注意：这不是 RoPE 本身的限制，而是**工程优化带来的限制**



### 总结

| 问题                                       | 回答                                                         |
| ------------------------------------------ | ------------------------------------------------------------ |
| **RoPE 公式有长度限制吗？**                | ❌ 没有，数学上支持任意长度                                   |
| **为什么要有 `max_position_embeddings`？** | ✅ 因为**训练数据长度有限**，模型只在这个范围内学会了位置感知 |
| **它是硬性限制吗？**                       | ⚠️ 默认是（因缓存和训练分布），但可通过**外推技术突破**       |
| **实际影响在哪？**                         | 训练分布、RoPE 频率设计、推理缓存上限                        |
| **如何突破？**                             | 使用 NTK scaling、YaRN、PI 等方法动态调整 RoPE               |