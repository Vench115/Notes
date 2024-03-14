# Methods

## Geometry representation

通用的形式化语言形式化语言，比如Lean，那么目前仍然需要大量的准备工作(groundwork)来描述大多的IMO几何题目。作者没有直接解决这个挑战，因为它需要深厚的专业知识，以及大量的在定理证明方法论之外的研究。

为了规避这个障碍，作者转而采用了一个更加专业的(specialized)的语言，用于GEX, JGEX, MMP/Geometer 以及 Geologic中，为带有类人的、非退化性的、以及拓扑假设，提供逻辑和图形环境的一系列工作。

这个语言的例子展示在Fig. 1 d,f。 由于更加严谨的形式化，整个IMO几何问题中的75%能够适用于这种表示方法。在这类几何环境中，每个证明步骤都能够被逻辑以及数值验证的，而且能够被一个人类读者验证，就好像它是IMO竞赛选手写的一样，多亏了这个语言高度接近自然语言的语法。

为了覆盖解释性(expressive)更强的代数(algebraic)和算数(arithmetic)推理，作者也增加了整数、分数、以及几何常量到这个语言中。作者并没有更加进一步地得到几何表示的完善解法，因为它是一个完全不同，而且极具挑战性的研究主题，这需要数学形式化社区中大量的研究(investment)。

## Sampling consistent theorem premises

采样固定的定理前提

作者开发了一个图像构造语言(constructive diagram builder language)，它与 JGEX 中使用的类似，是用于每次在前提下构造一个对象(object)，而不是随意地(freely)采样很多包含不同对象的前提(premises)，因此可以避免生成前提的自相矛盾的几何。

Extended Data Table 1中又一个完善的，关于构造动作(actions)的列表。这些动作包括构造操作，构造可以以一个特定的方式创建与其他对象相关的点，也就是共线的、内心/旁心(incentre/excentre)等等。同样的构造还可以将一个数字作为它的参数，例如：“构造点X使得给定一个数字α，令∠ABX=α“。

我们可以用更加复杂巧妙的(sophisticated)动作来扩展这个列表，来描述更具解释性的几何情况(scenarios)的几何，这就能同时提升综合数据的多样性，以及测试集的覆盖程度(coverage)。

在ref. 32中可以找到一个更加普遍、更具解释性的图像构建语言。但是作者使用的是一个更简单的语言，这个(形式化)语言已经足够来描述IMO-AG-30中的问题了，而且也能与符号引擎DD工作得很好。

## The symbolic deduction engine

符号推理引擎的核心功能(functionality)是推理出新的真命题(true statements)，在给定了定理前提的情况下。引擎能够通过几何规则，比如“如果X，则Y”这样的规则，来进行推理(deduction)，其中X和Y都是几何命题(statements)的集合，例如“A,B,C是共线的”。

由于这个原因，作者使用了结构化的DD(Deductive Database)的方法，因为这种方法能够在仅仅几秒钟内找到推理闭包(deduction closure)，而且是在一个标准的、未部署加速器的(non-accelerator)硬件上。

为了进一步增强推理能力，作者也赋予(built into)AlphaGeometry通过AR(Algebraic Rules 代数)进行推理的能力。AR可以让证明中包含角度/比例/距离的追踪(chasing)。详细的AR例子在Extended Data Table 2展示。

这样的证明步骤在几何证明中是普遍存在的(ubiquitous)，然而并没有被几何规则所覆盖。作者扩展了在GeoLogic中实现的高斯消元过程(Gaussian elimination process)，为了能够在几秒之内，找到符合所有可能的线性算子(operators)。

作者的符号推理引擎是DD和AR的精密的融合(intricate integration)，作者也就用它来扩展已知真命题的联合闭包(closure)，直到扩展停止(halt)。这个过程通常在几秒钟之内，最多到几分钟之内就停止了，而且是在一个标准的不使用加速器的硬件上。

## Algebraic reasoning

代数推理

## 1

在几何定理证明的研究领域中，对于代数推理还没有一个完善的解决方法(treatment)。例如，在iGeoTutor中，Z3(ref. 33)被用来解决算数推断(arithmetic inferences)，但是代数运算(manipulations)还没有被触及(covered)。

DD(ref. 17)通过在一些有限的推理规则中解释代数推理来解决这个问题，因此，这种方法就不能解决更复杂的运算了，也就造成算数推论没有被解决(covered)。

目前，最普遍的解决方法是类似于ref. 34中的一个过程，但只能用于角的定理发现(angle-only theorem discovery)，而且只能在GeoLogic中实现角和比例。

作者扩展了这个设计(formulation)来副高所有关于点之间的角、比例和距离的推理，也包含了带有几何图形常量，比如 $\pi$ 或者 $\frac 1 2$ 的算数推理。在Extended Data Table 2中有非常具体的代数推理的例子。

## 2

从更高的维度来说，作者首先将输入的线性方程转化为他们系数的矩阵。特别地，作者创建了一个系数矩阵 $A \in R^{M \times N}$ ，其中N是变元(variables)的个数，M是输入方程的个数。

在几何中，任何等式都是 $a-b=c-d \Leftrightarrow a-b-c+d=0$ 的形式。例如，角度等式 $\angle ABC = \angle XYZ$ ，可以被表示为 $s(AB) - s(BC) = s(XY) - s(YZ)$ ，其中 $s(AB)$ 是AB和x方向之间的角度，以 $\pi$ 为模。

同样地，比例 $AB:CD = EF:GH$ 可以被表示为 $\log {(AB)} - \log {(CD)} = \log {(EF)} - \log {(GH)}$ ，其中 $\log{(AB)}$ 是线段AB长度的对数。对于距离，每个变量都是一个(点，线)对，代表具体线段上的一个具体的点。

## 3 (化最简型矩阵)

因为所有的等式都是 ”$a-b-c+d=0$“ 的形式，作者用每个等式填充矩阵的每一行，用 ”$+1,-1,-1,+1$“ 的值来填充与变量 $a,b,c,d$ 相关的列。在A上运行高斯消元，并返回一个每一列第一个元素都是1的新矩阵，本质上代表了每个变量都是一个剩余元素的线性组合。

举个例子来说，作者有 "$a-b=b-c, d-c=a-d, b-c=c-e$" 作为输入的等式，使用高斯消元过程，就可以返回如下的结果。

$$
\begin{pmatrix}
 a & b & c & d & e \\
 1 & -2 & 1 & 0 & 0 \\
 -1 & 0 & -1 & 2 & 0 \\
 0 & 1 & -2 & 0 & 1
\end{pmatrix}
\rightarrow^{GE}
\begin{pmatrix}
 a & b & c & d & e \\
 1 & 0 & 0 & -1.5 & 0.5 \\
 0 & 1 & 0 & -1 & 0 \\
 0 & 0 & 1 & -0.5 & -0.5
\end{pmatrix}
\Rightarrow
\left\{\begin{matrix}
 a=1.5d - 0.5e \\
 b=d \\
 c=0.5d + 0.5e
\end{matrix}\right.
$$

## 4

从这个结果中，作者可以确切地(deterministically)且彻底地推理出所有新的等式，只要通过检查：$如果x_1 = x_2 或 x_1 - x_2 = x_2 - x_3 或 x_1 - x_2 = x_3 - x_4$ ，其中 $\{ x_1, x_2, x_3, x_4\}$ 是所有变量的任意4-置换(4-permutation)。

例如，在上述的高斯消元中，AR会从这三个输入的等式中推理出 $b=d$ 。为了解决几何常量，例如 ”$0.5 \pi$“ 或者 ”$\frac 5 {12}$“，作者将 ”$\pi$“ 和 ”1“ 作为默认的变量记入所有的系数矩阵。

## Deductive database implementation

推理数据库实现

不同于原先的(original)DD的实现，作者使用了图数据结构来捕捉(capture)几何的对称信息(symmetries)，而不是使用标准的(canonical)字符串的形式。由于使用了图的数据结构，作者可以捕捉到不仅仅是函数参数(function arguments)的对称置换(symmetrical permutations)，还可以捕捉到等式的传递性(transitivity)、共线性(collinearity)、以及共圆性(concyclicity)。

这种图的数据结构将它自己融入(bakes itself)进了一些推理规则中，在DD中使用的那些几何规则列表中，这些推理规则已经被明确地规定了(explicitly stated)。因此，这些从原始列表中获得的推理规则，并没有被用在探索中的任何地方，而是隐含地使用了，而且在有需要时，也就是当最终的证明被序列化成文本时，就能够被明确地转译(spelled out)。

### Traceback to find minimal proofs

回溯来找到最少的证明序列(minimal proofs)。

每个推理步骤需要带有(coupled with)一个回溯算法，这个算法返回最近先前命题的最小集合，这对于推理出结论命题的步骤来说是非常重要的。

对于提取出主要文本中的证明图谱和最小前提来说，这是最为核心的构建要素。一个最小-前提-提取的算法对于避免多余的辅助构造来说是非常重要的，因为这些构造会导致不必要传递中的证明(proof through unnecessary transitivity)。例如，“a=b” 和 “b=c” 可能不是很必要，如果 ”a=c“ 并不能直接从其他推理链条中获得的话。

## Traceback for geometric-rule deduction

为了几何规则推理的回溯

为此，作者记录了等式转移图(equality transitivity graph)。例如，如果 “$a=b, b=c, c=d$” 而且 “$a=d$” 被推理出来了，结果就是顶点 $a,b,c,d$ 被连接到了同样的 “等式顶点(equality node)” e上，作者就在e中保持一个图，图上有边 \[(a,b), (b,c), (c,d), (a,d)\] 。这使得回溯算法可以进行广度优先搜索，来找到 $a,b,c,d$ 中任意两对变量之间矩阵转换的最短路径。

然而，对于共线性和共圆性，这种表示会更加的复杂。在这些例子中，超图 $G(V,E)$ 带有3-边或4-边被用作等式的转移矩阵。现在，回溯等价于为目标顶点(三个共线顶点或四个共圆顶点)集合 $S$ ，找到一个最小生成树(minimum spanning tree)，也就是下述公式中的MST，顶点的权重是图的超边 $e^{'}$ 的并集的基(cardinality)：

$$
MST(S) = \min_{T \subset E} |{ \cup_{e^{'} \subset T } w(e^{'}) }|
\space s.t. S \subset T
$$

上述的优化是一个NP-hard问题，因为它是顶点覆盖问题的决策版本的一种简化(a reduction from the decision version of vertex cover)。作者在这个例子中简单地使用了一个贪心算法，来找到一个尽力而为的(best-effort)最小生成树。

## Traceback for algebraic deduction

通过高斯消元法的回溯，可以被看作等价于一个混合整数(mixed integer)的线性规划问题。给定输入等式的稀疏矩阵 $A$ ，这个矩阵由前述的内容所构造的，还有一个带有系数向量 $b \in R^N$ 的目标等式。作者通过定义非负整数决策向量 $x,y \in Z^M$ 来决定对于 $b$ 的前提的最小集合，并且解决下述的混合整数线性规划问题：

$$
x,y = \min_{x,y} \sum_i (x_i+y_i)
\space s.t. A^T (x-y) = b
$$

对于等式的直接父节点的最小集合可以被b表示，就是第i个等式 (A中的第i行)，相关的决策变量 $(x_i - y_i)$ 是非零的。

## Integrating DD and AR

融合推理数据库与代数规则

DD和AR被交替(alternately)用于扩展他们的联合推理闭包。DD的输出，包含用推理规则推理出的新命题，这些输出被输入到AR中，反之亦然。

例如，如果DD推理出了“AB平行于CD”，AB和CD的斜率在AR的稀疏矩阵A中，被更新成相等的变量(系数矩阵A定义在Algebraic reasonning部分)。

也就是说，与斜率AB相关的列会被置为1，与斜率CD相关的则是-1，这一行会被加入系数矩阵A。高斯消元以及混合整数线性规划在AR执行后会再次运行，生成新的等式作为下一次DD迭代的输入。这个循环会一直重复，直到联合推理闭包停止扩张。

>Both DD and AR are deterministic processes that only depend on the theorem premises, therefore they do not require any design choices in their implementation.

DD和AR都是确定的(deterministic)过程，并仅仅只依赖于定理前提，因此他们在实现上不需要任何的决策设计。

## Proof pruning

证明剪枝

尽管任何顶点的直接祖先顶点的集合是最小的了，但这并不能保证完全回溯的依赖子图 $G(N)$ 和必要的前提P是最小的。

这里作者将极小性(minimality)定义成一个性质(property)，即 $G(N)$ 和 $P$ 不能在不损失任何结论的可达性(conclusion reachability)的情况下，被进一步剪枝。

在不进行最小化的情况下，作者可以得到许多带有无意义的(vacuous)辅助构造的综合证明步骤，这些步骤与实际证明关系不大，也可以被完全弃用(discarded)。

为了解决这个问题，作者使用了彻底的试错方法(exhaustive trial and error)，丢弃辅助点的每一个子集，并在更小的前提子集上重新运行DD+AR，来验证目标的可达性。最后，算法将整个尝试中能够获得的最小证明返回。这个证明-剪枝步骤是通过综合数据生成，以及在测试时间中每一个成功的证明搜索中同时完成的。

## Parallelized data generation and deduplication

并行数据生成以及数据去重

作者在一个拥有大量并行CPU的硬件上，运行了子集的合成-数据-生成过程，每个都用一个不同的随即种子进行初始化。在100,000个CPU核心上运行这个程序72小时之后，作者得到了大约5亿(500 million)个合成证明例子。

作者重新格式化了证明命题，得到了它们最简洁的(canonical)形式，以避免它对于例子本身以及对于测试集的，浅层数据去重(shallow deduplication)。

最终，作者获得了1亿(100 million)条独立的(unique)定理-证明例子。其中，一共有九百万个例子包含了至少一个辅助构造。作者在合成的数据中并没有发现IMO-AG-30中的题目。

JGEX中收录的一个几何问题集中，包含的大多是中等难度的题目以及众所周知的定理，而作者在这个问题集上发现了接近20个问题也在合成数据集中。这说明训练数据覆盖了相当数量的几何领域的基本常识，但是更加复杂的(sophisticated)定理的空间仍然更加巨大。

## Language model architecture and training

语言模型架构和训练(细节)

作者使用Meliad library来进行transformer训练，而且只用它的基本设定。Transformer有12层，嵌入的维度(embedding dimension)是1024维，使用了八头注意力以及一个4096维、使用ReLU激活函数的相互注意力(inter-attention)的密集层。

总之，除了模型输入和输出端的嵌入层，Transformer有1.51亿(151 million)个参数。作者定制的分词器(tokenizer)使用 "word" 模型，使用SentencePiece训练的，同时它的词汇表有757个单词。

作者限制了最大的上下文长度是1024个token，并且使用T5风格的相对位置编码(relative position embedding)。序列封装(Sequence packing)也被用到了，因为作者用的序列中超过90%都是低于200个长度的。

在训练的过程中，在attention前以及dense层后，dropout概率被设置成5%。一个4x4片的TPUv3由于其硬件加速器的特性而被使用。对于预训练，作者用每个核心16个batch-size来训练transformer，并使用了cosine的学习率曲线，在1亿步之内从0.01下降到0.001。

对于微调，作者在另外一百万步之内保持了最终的学习率0.001。对那些不使用预训练的方法，作者将学习率在一百万步之内从0.01下降到0.001。作者没有使用任何的超参数优化(hyperparameter tuning)。这些超参数的值要么选定为一个非常大的整数值(round number)比如训练的部署，或者由Meliad代码库中默认提供。

## Parallelized proof search

### 1

因为语言模型解码过程会返回k个不同的序列，来描述k个备选的辅助构造，作者在这k个选择中进行集束搜索(beam search)，并且用的是每个技术的得分作为它的值函数。

这个设定在集束间是高度可并行的，使得存在并行计算资源时可以有极大的速度提升。在作者的实验中，他们使用k=512的集束搜索，最大的迭代数是16，而且每个顶点的分支系数(branching factor)是解码的batch size，也就是32。这在作者的transformer大小之下，是最大的能够适配GPU V100的显存的推理时间batch size。

如果将这些因子扩大(scale up)，来检查更大比例的搜索空间，”有可能“进一步改善AlphaGeometry的结果。

### 2

对于每个问题，作者使用了四个GPU核心的集群，其中每个都存储一份transformer语言模型的副本，为的是在不同的集束中拆分工作载荷，同时也有一个10000CPU核心的集群来存储符号解题器，分担全部30道题目上的全部集束。

以这种方法，一个较早结束的问题可以贡献出它的计算资源，给那些运行时间更长的问题。作者记录了符号解题器在每一个问题上运行时间，在设计上来说，这个时间应该在所有集束上大致保持一致。

作者利用了这点，以及语言模型的解码速度来推断每个问题所需要的并行性(parallelism)，分别来说，这些并行性就是在Extended Data Fig. 1中展现了不同时间限制下的模型，在IMO上所需要的数量。

## The effect of data and search

数据和搜索的影响

作者用更小比例的原始数据集来训练AlphaGeometry，20%、40%、60%和80%，从中发现，甚至是只要%20的训练数据，AG仍然能解决21道问题，也超过了最强的求解18题的基准线(DD+AR+human-designed heuristics)，具体在Extended Data Fig. 6c,d 中展示。

作者还发现，集束的大小设置为8，也就是64倍削减于原始集束大小512，AG仍然解决了21道题。同样地，如果将搜索深度从16削减到只有2，而保持512的集束大小的话，也能得到同样解决21题的结果。

## Evaluation on a larger test set

在更大的测试集上进行评估

作者在一个更大的、包括231道几何问题的测试集上，评估了AG和其他肌醇方法，具体在ref. 17中展现。这个集合覆盖了IMO竞赛以外的、范围更广的来源：比如课本的习题和练习题、地区奥赛、以及著名的几何定理；一些甚至步经典的IMO题目还要复杂，例如(密克)五点共圆定理、莫利定理、或者Sawayama and Thébault's theorem。

结果记录在Extended Data Fig. 6b中。

不同方法的总体排名与在Table 1中的结果保持一致，也就是AG解决了几乎所有的问题(98.7%)。最强的基准 DD+AR+human-designed heuristics 解决了92.2%，然而先前最好的方法解决了75%。

### AlphaGeometry framework and applicability to other domains

#### 1

>The strength of AlphaGeometry’s neuro-symbolic set-up lies in its ability to generate auxiliary constructions, which is an important ingredient across many mathematical domains.

AG框架及其对于其他领域的适用性。AG的神经-符号设定的强大之处，在于其生成辅助构造的能力，这在很多其他数学领域都是非常重要的要素。

在Extended Data Table 3中，作者在其他四个数学领域中给出了例子，其中也说明辅助构造对于解题是非常关键的。在Extended Data Table 4中，作者给出了有关几何证明和不等式证明的逐行对比，题目是IMO 1964 P2，其中强调了他们都能适配相同的框架。

#### 2

作者的工作展现了语言模型是可以从合成数据中，学着去提出(come up with)辅助构造的，其中问题的声明和辅助构造一起都是随机生成的，然后被回溯算法分割(separated)，以分辨依赖性差异(dependency difference)。具体地，AG这个框架需要以下的要素：

1. 领域中对象和定义的实现(implementation)
2. 一个随机前提采样器(sampler)
3. 工作在实现(1)中的符号引擎
4. 给予符号引擎的一个回溯步骤(procedure)

#### 3

利用这四个要素和在正文描述的算法，我们就可以生成针对任何领域的合成数据了。正如在作者论文中所展现的，构建每一块要素都有着工程上(engineering)不平凡的(non-trivial)挑战。

例如，最新的组合数学的形式化就处于非常初期的阶段(nascent)，这也就在要素1、2上带来了挑战。同时，构建强大的、针对不同领域的符号引擎需要深度的领域专业知识，这就在要素3、4要素上带来了巨大的挑战。作者认为可以将这种框架应用于更广阔的领域，作为未来的工作，而且他们也期待能够解决这些挑战的更深入的创新。

## Transformer in theorem proving

定理证明中的Transformer

自动定理证明的研究已经有非常长的历史了，可以追溯道20世纪50年代，
在21世纪10年代，深度学习成长成了一个对于自动定理证明来说全新的、非常强大的工具，展现了在前提选择(premise selection)和证明辅助指引(proof guidance)、还有SAT解题上的巨大成功。

从另一个角度来说，transformer在大量不同的任务上都展现了出色(outstanding)的推理能力。第一个成功将transformer语言模型应用于定理证明的是GPT-f。其后来的扩展更进一步地沿着该路线开发，第一次使得机器可以解决一些奥赛等级地题目。

在证明-搜索算法以及在线训练上的创新，也改善了基于transformer的方法，在代数和数论上也解决了总共十道IMO题目。然而，这些进步，在一个大量的人类证明例子、以及由人类设计和组织(curated)的独立的题目命题是可以预测的。

### Geometry theorem proving

几何定理证明，在一个完全不同的(separate)空间中进化(evolve)。它的研究(literature)被分成了两个分支，一个是计算机代数方法，一个是搜索方法。

前一种被广泛认为已经解决，因为吴法的引入，这种方法理论上可以决定任何一个等式类型的几何声明的真值(true value)，其中还含有先前工作中提出的代数工具。尽管计算机代数由很强的理论保证，但是它的性能在实际情况中会有所限制，由于方法所需要的大量的时空复杂度。更进一步地，计算机代数地方法论并不能引起AI研究的兴趣，AI研究反而寻求利用搜索方法来证明定理，(作者认为)这是一个更加类似人类的、而且一般用途的程序。

搜索方法也最早从20世纪50年代就开始了，而且在20世纪持续地发展。

>With the introduction of DD10,17, area methods61 and full-angle methods30, geometry solvers use higher-level deduction rules than Tarski’s or Hilbert’s axioms and are able to prove a larger number of more com plex theorems than those operating in formal languages.

在引入DD、面积法、全角法之后，几何问题求解器可以利用比Tarski's或者Hilbert's公理更加高级(higher-level)推理规则，而能够比那些在形式化语言中工作的方法，证明更多更加复杂的定理。

>Geometry theorem proving falls behind the recent advances made by machine learning because its presence in formal mathematical libraries such as Lean or Isabelle is extremely limited.

然而，如今的几何定理证明，仍然依赖人类设计的启发式搜索方法，来进行辅助构造。几何定理证明落后于最新的，由机器学习带来的进步，是因为它存在于形式化数学程序库，例如Lean或Isabelle中的工具是极其有限的。

### Synthetic data in theorem proving

合成数据长期以来都被认为，并作为一个重要的定理证明中的要素所使用。最新的机器学习方法利用了专家迭代(expert iteration)来生成合成证明的工具(curriculum)。然而他们的方法，仅仅只能生成一个用于预先定义问题几何的综合证明，这个几何也被人类所设计和选择。

作者提出的方法，从另一个角度来说，同时生成了综合的问题和证明，而且是从零开始。Aygun等类似的使用后验知识回放(hindsight replay)来生成综合证明，提供了难度平滑的定理，来避免学习作者提出的工作。

然而，AlphaGeometry并不是在现有的人类所构想的猜测(conjectures)训练的，而且也不是从那些在目标定理上尝试的证明上学习。因此，他们的方法(与作者的方法)是正交的，所以未来可以用于进一步地改进AG。

与作者的工作最相似的是Firoiu等人的工作，他们使用了一个前向提议器(proposer)，通过深度有限的探索方法来生成综合数据，而且纯粹在这些综合数据上训练了一个神经网络。

而作者的工作，从另一个角度来说，使用了广度优先的探索，这对于获得最小证明和前提来说是必须的，因此引入了新的符号和假设，而前向建议器是不能这样建议的(forward proposer cannot propose)。
