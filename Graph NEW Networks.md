# Graph Convolution Network

[Convolutional Networks on Graphs for Learning Molecular Fingerprints (arxiv.org)](https://arxiv.org/abs/1509.09292)

使用了谱(Spectual)方法，将周围的信息聚合起来。

# G ResNet

[GResNet: Graph Residual Network for Reviving Deep GNNs from Suspended Animation (arxiv.org)](https://arxiv.org/abs/1909.05729)

图残差网络方法以及解决**Suspended Animation Problem**。

[Image Super-Resolution via Deep Recursive Residual Network](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8099781) and other papers

Introduces a recursive residual network for image resolution adjustment.

## Suspended Animation Problem

在文章中的第3部分，讲述 Suspended Animation Problem with GCN Model，作者提供了对于GCN模型的理论约束证明。

问题定义在文章的第4部分。

## Definition 1

不可约的、非周期性网络 (Irreducible and Aperiodic Network)：对于一个输入的图G，当且仅当任意两顶点间是可达的(accessible)，那么图G就是不可约的；同时，当且仅当G不是二分的，那么图G就是非周期性的。

## Lemma 1

对于一个无权输入图G，且图G是不可约的、有限的以及非周期性的，如果它的关联矩阵是非对称的，从任意一个起始分布向量 $x \in \mathbb{R}^{n \times 1}$ ，工作在图上的马尔可夫链有一个唯一的稳态分布向量：
同时，如果矩阵 $\hat A$ 是对称的，那么稳态分布向量就会在顶点上变成均匀分布，也就是说：

$$
\pi^* (i) = \frac 1 n
$$

# Graph Attention Network

[Graph Attention Networks PDF (openreview.net)](https://openreview.net/pdf?id=rJXMpikCZ)

GAT leverages masked Self-Attentional layers to address the shortcomings of GCN.

# Graph Transformers (GTN)

[Graph Transformer Networks (arxiv.org)](https://arxiv.org/abs/1911.06455)

# Recurrent GNN

Involve the **Recurrent Neural Network** for Deep Graph Representations.
