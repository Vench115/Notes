# 0

总结AG2的改进，用何方法，效果如何？

# Abstract

1. 改进了形式化语言体系，包括：移动对象、线性方程组等等，提升了对于IMO几何问题的覆盖率。
2. 采用更好的语言模型，Gemini，以及一个全新的知识共享机制，凯爹之前讲过，他们叫他搜索树的共享知识集成。
3. 加强的符号引擎，以及更好的综合数据生成，更高质量的数据训练的更好的模型，提升了求解率。

# 1

- IMO是非常难的竞赛，但是与TG观点相悖的是，他们认为几何从形式上比较统一，所以最容易理解，也适合用于推理的研究。
- AG1性能限制：形式化语言的覆盖范围、引擎效率、语言模型能力限制。
- 而AG2就是针对上述几项限制，进行的改进。
- 他们又提出了一个非构造性，或者说不可构造问题的概念，我觉得也是我们需要注意的。

# 2

- 主要提到的就是升级AG专用的语言。这里，我们也需要理解：AG的谓词和他们用于产生数据集的动作列表不是一个东西。
- 一个是“求xxx”，主要用于让语言模型直接生成计算角度和比例的指令。其实还是挺想不到的，如此强大的系统，居然连这样基本的能力都没有。当然可能他们使用了不一样的形式。
- 还有一个是用于“捕捉”线性方程专用的谓词。
- 明确表示拓扑/非退化条件检查的谓词，看到他们也注意到了点序问题，这个我认为凯爹的设计是走在前面的。
- 还有非退化条件，就是 `noverlap a b` 特别表示不重合的点，当然也有表示等价点，也就是 `overlap a b`，这个后续在我们的方程组求解点的过程中，也是同样可以体现的。
- 这里比较让人惊讶的一点，是AG1只能使用最多2个谓词来定义一个点，这可能由于他们对于构造性问题的背景设置。他们也提到，这限制了AG1的解题能力。而在AG2中，则放宽了这个约束，能够让图形的构造不再平凡，并进行自动化生成。
- 但是即便是这样，他们也没有完整覆盖IMO的几何问题。

# 3

- 在这个板块，他们也终于提到了自动形式化，他们居然是直接用了few-shot prompt的方法，来进行自动形式化。问询模型5次，然后再次调用Gemini，将所有结果整合，表现稳定。
- 自动图形生成。AG2允许一个或多个点，被任意数量谓词定义，可以覆盖非构造性问题。
- 自动图形生成，引出了非构造性问题解析，点初始化方法，使用点坐标优化损失函数，每一步都非常重要。
- 如果不能得到按照谓词条件的圆，那么点会通过正态分布进行采样。
- 高斯-牛顿-列文伯格方法

# 4

- DDAR更新：处理重合点；更快的算法；更快的实现
- 对于重合点问题的重新阐述，也就是简化交点的一种等价转换。
- 在算法中，他们也是对于每个点的组合都应用每一条规则，在他们的形式化体系下，显然是更加费时的环节。候选搜索步骤，多项式复杂度；子句匹配步骤，复杂度达到 $O(N^8)$ ，可见一斑。
- 硬编码搜索降低复杂度水平，使得一些基本的数值推理直接发生在AR引擎中。
- 还有对于相似三角形、圆内接四边形的搜索。对于相似三角形，AG会遍历所有的三元组；对于圆内接四边形，AG也是遍历所有的对等等方法。
- pybind11绑定C++实现python代码，性能提升非常明显。

# 5

- 有意避开人类精心构造的问题，避免“数据污染的”风险。
- 使得定理分布扩展到超越人类的范畴，这里对于定理的定义可能是不太相同的。他们的定理可能不是我们说的规则，跟TG一样，代表的是某些证明的情况。
- 具体更大、更复杂、更好的数据分布。
- 更多种类的定理，轨迹型问题，也就是点位置不确定。例子中给出的，则是定义点的位置来源(依赖)。
- 贪婪丢弃算法：在第6章中介绍。

# 6

- 多个不同的语言模型，进行树搜索，也就是搜索树的共享知识集成。
- 其实是一个算法上常用的思想，就是将已经探索过的节点存储起来，然后方便其他的搜索树进行探索。
- 对于每个节点都生成一个辅助点；对每个节点都生成多个辅助点；对于提示词的改进，使符号均匀分布；更深但是更窄的树；更浅但是更宽的树。
- 更多的系统设计细节，用于搜索树之间的协作，以及对于计算资源的合理分配。

# 7

- AG1的定制Transformer，无监督训练，包含两个阶段。
- AG2用了Gemini的训练管线，直接简化到一个训练阶段，就是在所有的数据上进行无监督的训练，感觉也像是因为数据太多了，不如直接在所有数据上进行训练，也是现代语言模型对无监督学习的一种工程诠释吧。
- 训练不同大小的模型：训练定制化的分词器；微调定制化的，数学专用的Gemini模型；从零开始多模态训练。
- 三个新的评估数据集，包含完整证明，以及对于困惑度损失的细节解释。
- 推理设置，需要更高的温度，以及多重采样，因为更低的温度并不能够生成足够多样的辅助构造。
- 加强LM和DDAR之间的推理，用s1表示DDAR能够直接推得的结论；s2表示目标谓词正确时，能够由DDAR推得的结论；s3表示数值上正确的结论集合。也就是三个不断扩大的集合，然后这些集合都会被序列化，并且拼接起来输入语言模型。

# 8

- 主要的下游指标是求解率，然后为什么是50道，是因为有一些几何问题的形式化特性，会被形式化成两道题。
- 效果很好，求解了50道中的42道，超越了金牌选手的平均水平。
- 更多的采样数、更大的束搜索并不会帮助求解更多题目。
- 超越人类的创造性。
- 两道解决不了的问题，包含了比较高级的问题求解技巧。理论上可以不使用技巧，但是就需要更长的推理时间、更长的证明过程、更多的辅助构造，弥补DDAR的不足。这种情况对于我们来说，还是比较容易解决的。

# 9

- 有趣且关键的结论。
- 语言模型在生成辅助构造方面相当强大，可以在不依赖符号引擎的情况下，直接生成完整的证明。
- 分词器和领域专用的几何语言，都不起决定性作用。使用特定语言和自然语言进行训练，得到的结果也是相似的。
- 从零开始训练，和微调两种方式，学习到的技能略有不同，整合起来可以提高整体的求解率。
- 改进空间：可变数量的点、非线性方程、不等式问题，以“完全求解几何问题”。
- 其次，将问题分解为子问题，应用强化学习方法，或许可以弥补。
- 最后，自然语言输入，没有幻觉，稳定输出解答，通过有监督的微调，可以提高自动形式化的能力。
