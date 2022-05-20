---
layout: post

---

"Attention is all you need."

- [Attention Is All You Need](https://arxiv.org/pdf/1706.03762.pdf).
- [Transformer论文逐段精读](https://www.bilibili.com/video/BV1pu411o7BE)
- [Transformer, Hung-yi Lee](https://www.youtube.com/watch?v=ugWDIIOHtPA&t=2401s)

### 1. Introduction

Recurrent models：

- factor computation along the symbol positions
- align the positions to steps in computation time ($h_{t-1}\rightarrow h_t$)
- preclude parallelization within training examples (sequential computation)

Attention mechanism：

- allow modeling of dependencies without regard to their distance in the input or output sequences

### 2. Background

ConvS2S and ByteNet are difficult to learn dependencies between distant positions and the number of operations grows required to relate signals from two arbitrary input or output positions grows in the distance between positions, linearly and logarithmically respectively.

CNN代替RNN、但使用卷积很难对长的序列进行建模：很多层卷积才能将隔得比较远的位置关联起来。而卷积能一层就把整个序列”看到“。

In the Transformer this is reduced to a constant number of operations. Multi-head attention is used to simulate the multi-channel output of CNN.

但卷积能输出多个通道（不同的特征、模式etc.），所以用多头注意力来模拟。

### 3. Model Architecture

<img src="/pic/transformer.png" style="zoom:50%;" />

Encoder maps an input sequence of symbol representations (x1,...,xn) to a sequence of continuous representations z = (z1,...,zn)

- where $z_t$ is the vector form of $x_t$

Decoder then generates an output sequence (y1, ..., ym)

- auto-regressive: 解码器中词是一个一个生成的，且“输入又是输出”
  - prevent positions from attending to subsequent positions
  - 因为在注意力机制中能看到**整个**句子，为了避免t时间能看到t时间以后的输入， 所以采用了masked多头注意力
  - 保证训练和预测的行为一致
- **NB:** m!=n

Layer normalization 对每一个**样本**做normalization，

- 全局的均值方差对不定长的句子（比如比训练句更长的测试句）来讲效果不是特别好，所以不用BN

<img src="/pic/layer_normalization.png" style="zoom:35%;" />

Scaled Dot-Product Attention

- "scaled": [为什么 dot-product attention 需要被 scaled？](https://blog.csdn.net/qq_37430422/article/details/105042303) “We suspect that for large values of d_k, the dot products grow large in magnitude, pushing the softmax function into regions where it has extremely small gradients”
- 矩阵乘法利好并行

