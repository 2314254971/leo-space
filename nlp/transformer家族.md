[一文读懂GPT家族和BERT的底层区别——自回归和自编码语言模型详解 - 知乎](https://zhuanlan.zhihu.com/p/625714067)





[自学大语言模型之Bert和GPT的区别-CSDN博客](https://blog.csdn.net/qq_38915354/article/details/131054219)





[AutoEncoding与AutoRegressive：区别，联系和应用-CSDN博客](https://blog.csdn.net/ove_z/article/details/134076577)





AE

特征提取：自编码器可以用作特征提取器，将高维输入数据转换为低维表示。这些低维表示可以用于其他机器学习任务，如分类、聚类、降维等。

数据去噪：自编码器可以通过学习从损坏或噪声数据中重构原始数据的能力来进行数据去噪。在训练过程中，自编码器被要求将损坏或噪声数据作为输入，并尽可能准确地重构原始数据。



AR

适用的任务
语言建模（主要）：AR模型可以用于语言建模，即根据前面的单词或字符预测下一个单词或字符。比如可以聊天的ChatGPT

时间序列预测：例如股票价格预测、天气预测、交通流量预测等。通过分析过去的观测值，AR模型可以预测未来的观测值。

机器翻译：AR模型可以用于机器翻译任务，将源语言的序列翻译成目标语言的序列。在这种情况下，AR模型可以根据源语言的部分序列来逐步生成目标语言的序列。





造成上述区别的原因感觉和模型结构以及训练方式有关，

个人猜测：

AR主要使用decoder结构，使用masked mha，也就是说利用**前文**不断预测生成**后文**；

AE使用encoder结构，mha提取的是**上下文**信息，适合提取语义；



NLP任务大致可以分为NLU和NLG两种；

Bert在NLU任务上效果很好，但是天生不适合处理生成任务。

原因在于Bert的预训练过程是使用的MLM，和生成任务的目标并不一致。

生成任务目标是每次蹦出来一个词，只能看到当前位置之前的词汇。

而Bert采用的是双向的语言模型，除了mask的单词，两个方向的词汇都可以被看到。

所以对Bert的一个改进思路就是让它在具有NLU能力的时候，同时兼备NLG能力。

比如每次在最后放个[MASK] token预测就完事了。但这会有两个问题:

1. 模型预训练时的[MASK]基本上很少出现在最后，这会导致training和inference产生不一致。
2. 用Encoder-only模型进行自回归解码效率会比较差，因为没办法复用[KV cache](https://zhida.zhihu.com/search?content_id=658627075&content_type=Answer&match_order=1&q=KV+cache&zhida_source=entity)（由于双向注意力的存在，所以前面的token要attend到新生成的token），新来的token会影响前面token的embedding。（不过某种程度上讲这可以使得模型可以通过后续生成的词来对前面的词进行纠错）

解决这两个问题最简单的方法就是在预训练的时候添加预测最后一个token的任务，并且从attention的角度避免新解码的token对前面的token产生影响，然后你会发现这就变成了一个类似[GPT-2](https://zhida.zhihu.com/search?content_id=658627075&content_type=Answer&match_order=1&q=GPT-2&zhida_source=entity)的[Decoder-only](https://zhida.zhihu.com/search?content_id=658627075&content_type=Answer&match_order=1&q=Decoder-only&zhida_source=entity)模型。



参考：[(17 封私信 / 80 条消息) Encoder-Only为什么不适合NLP生成式任务？ - 知乎](https://www.zhihu.com/question/648617914)

