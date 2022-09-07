## 🎉A Simple Machine Translation Trial

基于经典的Seq2Seq网络架构，使用RNN、Attention机制、Transformer等网络或技术实现一个简单的English-to-Chinese机器翻译任务。

### 📖Data Source

http://www.manythings.org/anki/

Data sample:

```
hi .	嗨 。
hi .	你 好 。
run .	你 用 跑 的 。
stop !	住 手 ！
wait !	等 等 ！
wait !	等 一 下 ！
```

Data rows: `29155`

#### Data Preprocess

英文分词：使用word-level Tokenize。

中文分词：使用Jieba分词库实现word-level Tokenize。

分词效果：

![image-01](https://github.com/Ci-Yelson/A-Simple-Machine-Translation-Trial/blob/main/img/data_sample.png)

为减小词汇表大小，在实现中对出现次数小于2次的词元进行了移除。

分词后生成的词汇表大小：`eng_vocab_size: 4486, cmn_vocab_size: 6515`。

### 🎏Models

使用通用的“编码器-解码器”Seq2Seq网络架构。

#### Baseline RNN

使用RNN作为Baseline，其中编码器和解码器均使用GRU Cell实现。

#### Add Attention Mechanism

在原RNN Encoder-Decoder的基础上加入Additive Attention Mechanism。

$$
Attention(Q,K,V) = \text{dropout}\bigg(\operatorname{softmax}\left(\mathbf{W}_{v} \tanh \left(\mathbf{W}_{q} \mathbf{Q}+\mathbf{W}_{k} \mathbf{K}\right)\right)\bigg) \mathbf{V}
$$

在Decoder的每个时间步中，Decoder上一时间步的最终层隐状态作为查询Q，Encoder在所有时间步的最终层隐状态作为键K和值V。

#### Transformer

使用了基于全注意力机制（Multi-Head Scaled Dot-Product Attention）的Transformer模型。

> 在实现中，RNN模型的`embed_size=512, hidden_size=1024, num_layers=2`；Transformer模型的`hidden_size=512, num_heads=4, num_layers=2`。
>
> 更具体的参数设置参见代码实现。

### 🔖Training Results

#### Training loss:

![image-02](https://github.com/Ci-Yelson/A-Simple-Machine-Translation-Trial/blob/main/img/loss.png)

其中，bidRNN为在编码器部分使用了双向结构的RNN。

#### BLEU Scores

使用BLEU Scores对模型的翻译效果进行评估。

![image-03](https://github.com/Ci-Yelson/A-Simple-Machine-Translation-Trial/blob/main/img/metrics.png)



可以看到的是：

- 在训练loss方面，由于transformer相对RNN缺少对序列信息的假设，因此在训练的收敛程度上，transformer低于RNN。
- 在BLEU Scores方面，带有注意力机制的模型的得分表现比没有注意力机制的要好很多。同时注意到，虽然tansformer相对RNN收敛程度较差，但其BLEU得分却是最高的。
- 在训练耗时方面，可以发现，由于transformer的并行化优势，其训练耗时相对RNN会短很多。



### 🅰️Attention Weights Visualize

#### RNN with Attention Mechanism

![image-04](https://github.com/Ci-Yelson/A-Simple-Machine-Translation-Trial/blob/main/img/attention_sample.png)

#### Transformer Attention

![image-05](https://github.com/Ci-Yelson/A-Simple-Machine-Translation-Trial/blob/main/img/transformer_enc_attention.png)

![image-06](https://github.com/Ci-Yelson/A-Simple-Machine-Translation-Trial/blob/main/img/transformer_dec_self_attention.png)

![image-07](https://github.com/Ci-Yelson/A-Simple-Machine-Translation-Trial/blob/main/img/transformer_dec_inter_attention.png)

### 📑References

> - [《动手学深度学习》](https://zh-v2.d2l.ai)
> - [Attention Is All You Need](https://arxiv.org/abs/1706.03762)
> - [CS231n Assignment 3](https://cs231n.github.io/assignments2022/assignment3/)
