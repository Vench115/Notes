# Graph Convolution Network

[Convolutional Networks on Graphs for Learning Molecular Fingerprints (arxiv.org)](https://arxiv.org/abs/1509.09292)

使用了谱 (Spectual) 方法，借助于拉普拉斯特征值、切比雪夫等等方法，将周围的信息以有效的方式聚合起来。

# G ResNet

[GResNet: Graph Residual Network for Reviving Deep GNNs from Suspended Animation (arxiv.org)](https://arxiv.org/abs/1909.05729)

图残差网络方法以及解决**Suspended Animation Problem**。

[Image Super-Resolution via Deep Recursive Residual Network](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8099781) and other papers

Introduces a recursive residual network for image resolution adjustment.

## Suspended Animation Problem

在文章中的第3章节，讲述 Suspended Animation Problem with GCN Model，作者提供了对于GCN模型的理论约束证明。

问题定义在文章的第4章节，作者通过研究图卷积神经网络采用的谱方法算子之后，他们发现其中包含两个连续的步骤：

1. 一步马尔可夫链 (1-step Markov Chain or a Random Walk)
2. 全连接层 (Fully-Connected Layer)

因此，叠加多个谱方法的图卷积网络层，就等价于以交叉的方式，叠加多个一步马尔可夫链层和全连接层。考虑到对于向量维度调整的权重变量 $W^{(k-1)}$ 对于所有顶点来说是共享的，那么给定两个具有相似特征的顶点，由 $W^{(k-1)}$ 参数化的全连接层也会产生相似的特征。

换句话说，所有顶点所共享参数的全连接层，实际上就不会对马尔科夫链层的融合有明显的影响了。因此，作者单独对马尔科夫链进行讨论。作者发现，当且仅当前一层与后一层相同时，马尔科夫链的层会收敛，那么在更新前后的特征就是相似的。那么，这个过程就高度依赖于输入网络的结构了。

### Definition 1

不可约的、非周期性网络 (Irreducible and Aperiodic Network)：对于一个输入的图G，当且仅当任意两顶点间是可达的(accessible)，那么图G就是不可约的；同时，当且仅当G不是二分的，那么图G就是非周期性的。

### Lemma 1

对于一个无权输入图G，且图G是不可约的、有限的以及非周期性的，如果它的关联矩阵是非对称的，从任意一个起始分布向量 $x \in \mathbb{R}^{n \times 1} (x \ge 0 \space \And \space \left \| x \right \|_1 =1)$ 开始，工作在图上的马尔可夫链有一个唯一的稳态分布向量 $\pi^*$ ，它由 $\lim_{t \to \infty} \hat A^t x = \pi^*$ 得到，此处$\pi^* (i) = \frac{d(v_i)} {2 |\varepsilon|}$。

也就是说，如果马尔科夫链无限地传播下去，它会存在一个稳定地状态。

同时，如果矩阵 $\hat A$ 是对称的，那么稳态分布向量就会在顶点上变成均匀分布，也就是说：$\pi^* (i) = \frac 1 n$ 。

根据引理1，对于那些基于顶点特征输入的GCN模型，我们也可以从多重马尔科夫链层中得出相同的结论，这个结论会将顶点已经学习到的特征弱化为稳定的特征矩阵。

### Theorem 1

对于一个输入图G，图G是无权的、不可约的、有限的、且非周期性的，如果在GCN模型中存在一个嵌入的马尔科夫链层，它就会将顶点学习到的特征，从列-归一化的特征矩阵减少到稳定的特征矩阵。

更进一步的，如果G是无向图，那么稳定的特征会变为：$\Pi^* = \frac 1 n \cdot  1^{n \times d_x}$ 。

定理1表达了GNN的Suspended Animation Problem产生的原因，引理1和定理1的证明，作者在文末的附录中有说明。

## Suspended Animation Limit Analysis

### Definition 2

GCN在网络G上的Suspended Animation Limit被定义为最小的模型深度 $\tau$ ，以至于对于图G上任意顶点的列-归一化的特征矩阵输入X，都会有下述的不等式：

$$
\left \| GCN (X; \tau) - \Pi^* \right \|_1 \le \epsilon
$$

基于上述的定义，对于带有恒等全连接映射的GCN来说，对输入的网络都存在一个牢固的限制。

### Theorem 2

令 $1 \ge \lambda_1 \ge \lambda_2 \ge \cdots \ge \lambda_n$ 是由网络G定义的矩阵 $\hat A$ 的特征值，那么图G上GCN模型的相关Suspended Animation Limit就如下所示：

$$
\zeta \le O \left ( \frac {\log \min_i \frac 1 {\pi^*(i)}} {1-\max \{ \lambda_2, |\lambda_n| \}} \right )
$$

如果情况是，图G是d-regular的，那么在这个图G上的GCN模型的Suspended Animation Limit就可以被简化为：

$$
\zeta \le O \left ( \frac {\log n} {1-\max \{ \lambda_2, |\lambda_n| \}} \right )
$$

Suspended Animation Limit从上述的定理中推出，它大致地表示了图G决定了GCN的最大允许的深度。在由图G定义的矩阵 $\hat A$ 中所有的特征值之中，$\lambda_2$ 测定了图G的分离 (disconnected) 程度；而 $\lambda_n$ 则测量了图G的二分性 (bipartite) 的大小。

当图G是可约的(当 $\lambda_2 =1$ 时)，或者是二分图时，$\zeta \to \infty$ 而且模型不会遭受Suspended Animation Problem的影响。

同时，作者提到了另一篇文章中，其他作者提出的比较朴素的 (naive) 残差GCN的变体，而这种方法一样也会收到Suspended Animation Problem的影响。对于等变的全连接层映射，这种方法的公式也可以被简化成“懒” (lazy) 马尔可夫链层，这种残差块并不会帮助解决问题。

### Corollary 1

令 $1 \ge \lambda_1 \ge \lambda_2 \ge \cdots \ge \lambda_n$ 是由网络G定义的矩阵 $\hat A$ 的特征值，那么该GCN模型所关联的Suspended Animation Limit即为：

$$
\zeta \le O \left ( \frac {\log \min_i \frac 1 {\pi^*(i)}} {1-\lambda_2} \right )
$$

# Graph Attention Network

[Graph Attention Networks PDF (openreview.net)](https://openreview.net/pdf?id=rJXMpikCZ)

GAT leverages masked Self-Attentional layers to address the shortcomings of GCN.

# Graph Transformers (GTN)

[Graph Transformer Networks (arxiv.org)](https://arxiv.org/abs/1911.06455)

# Recurrent GNN

Involve the **Recurrent Neural Network** for Deep Graph Representations.


# All in One: Multi-task Prompting

[[2307.01504] All in One: Multi-task Prompting for Graph Neural Networks (arxiv.org)](https://arxiv.org/abs/2307.01504)
