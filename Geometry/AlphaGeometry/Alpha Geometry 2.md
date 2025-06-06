[Gold-medalist Performance in Solving Olympiad Geometry with AlphaGeometry2](https://arxiv.org/abs/2502.03544)

# 0. Improvements

- 全方位改进
- 有趣的结论

# Abstract

作者团队推出了Alpha Geometry 2，一个由[[Alpha Geometry]]提出的Alpha Geometry的极大改进版本，目前，该版本已经在求解奥林匹克级别的几何问题中，超越了平均金牌选手的水平。为了达到该目标，作者首先扩展了原本的AG语言，来求解(tackle)更难的问题，包括对象的运动，以及包括角度、比例、和距离的线性方程组问题。这一步，以及其他内容的增加，极大地改进了AG语言在IMO 2000-2024几何问题上的覆盖率，从66%提升到了88%。

AG2的搜索过程也有了很大的改进，通过使用Gemini架构，来达到更好的语言建模(language modeling)，以及一个全新的知识共享(knowledge-sharing)机制，集合了多重搜索树。

除了进一步加强符号引擎和综合数据生成，作者也大力地提高了AG2对过去25年里所有几何问题的整体求解率，从先前的54%提升到了84%。

AG2也是在IMO 2024中达到银牌等级的，求解系统的一部分。最后，同样重要的是，作者团队报告了整个过程，针对使用AG2作为一个完全自动化系统的一部分，来直接从自然语言输入中可靠地(reliably)求解几何问题。

# 1. Introduction

## Background

IMO对于全世界的高中生来说，都是一个享誉盛名的数学竞赛。IMO问题以它们的高难度而被人所熟知，而且求解这些问题需要对于数学概念的深度理解，以及创造性地应用它们的能力。几何，四大IMO问题范畴之一，是几类问题中形式最统一的一个，因此也最容易理解(approachable)。这类问题也非常适合基本的推理研究。

## Approaches

自动化求解几何问题，主要有两种技术路径。一种，是用代数方法“痛击”(bashing)问题，利用比如“吴法”；面积法；或者Grobner基方法。另一种，则依赖于综合的技巧(synthetic techniques)，例如：推理数据库、或全角法。作者更关注于后者，作为一种更加与人类相似的方法路径，也适合于将研究知识转移到其他领域。在作者先前的工作中，作者提出了Alpha Geometry (AG1)，一个神经-符号系统，展现了完全统治(mastering)该领域的一大步，达到了在2000-2024全部IMO几何问题上54%的求解率。AG1结合了一个语言模型和一个符号引擎，来有效地解决这些极具挑战性的问题。

## AG1 Limitations

尽管它取得了成功，AG1在几个关键领域还是展现出了局限性。它的性能被它领域专用语言的范围、符号引擎的效率、以及初始的语言模型的能力所限制。因此，当考虑到从2000年至今的所有IMO几何问题，AG1仅能达到54%的求解率。

## Improvements

该文章则提出了AlphaGeometry2(AG2)，是对AG1的大幅升级，突破了这些限制，而且增强了性能。AG2利用了(leverages)一个更强大的，基于Gemini的语言模型，它是在一个更大更多样的数据集上进行训练的。

作者也提出了一个显著更快，且更加鲁棒的符号引擎，也加入了很多优化，例如：一个简化的规则集合，以及更强的对双点(重合点)问题(double points)的处理能力。

不仅如此，作者团队扩展了领域语言，以覆盖范围更广的几何概念，包括轨迹定理(locus theorems)以及线性方程。

为了进一步改善性能，作者开发了一个全新的搜索算法，能够探索更宽范围的辅助构造策略，并运用一个知识共享的机制，来扩展并加速搜索过程。

最终，作者在构建一个完全自动化而且可靠的系统上取得了进展，该系统能够以自然语言的方式求解几何问题。为了实现该目标，作者使用Gemini将问题从自然语言翻译成AG的语言，并且实现了一个新的自动化图形生成算法。

## AG2 Key Improvements

- Expanded Domain Language (扩展的领域专用语言)：覆盖轨迹型的定理、线性方程、以及非(不可)构造性的(non-constructive)问题声明。
- Stronger and faster Symbolic Engine (更强更快的符号引擎)：优化的规则集合，新增的对于双点的处理，以及更快的用C++的实现。
- Advanced Novel Search Algorithm：利用多重搜索树，且利用知识共享。
- Enhanced Language Model：利用Gemini架构，在一个更大更多样的数据集上训练。

# 2. More general domain language

首次提出是在AG1中，AG1使用一个简单的领域专用的语言，包含了九个基本的“谓词”，在Table 1中列出。当这些谓词足够覆盖所有2000-2024 IMO中66%的几何问题时，AG1的语言并不允许(支持)讨论线性方程、点/线/圆的移动、或一些常见问题，比如“求角度……”。下表展示了作者的AG2如何解决这些和其他挑战。

| Name             | Meaning                                                       |
| :--------------- | :------------------------------------------------------------ |
| cong a b c d     | $AB = CD$                                                     |
| perp a b c d     | $AB \perp CD$                                                 |
| $\dots$          | $\dots$                                                       |
| aconst a b c d x | Angle Between AB and CD is equal to x, where $x \in [0, 180)$ |
| rconst a b c d y | AB:CD = y where y is a constant                               |

上述Table 1是AG1的谓词。

首先，AG2新增了两个谓词来允许求解问题类型：“求x”：

1. `acompute a b c d` 表示：求解AB和CD之间的角度。
2. `rcompute a b c d` 表示：求解AB/CD的比例。

在一些几何问题中，包括出现在IMO 2024中的那道题目，存在几何量的线性方程(角度，距离)，而这些是AG1不能捕捉到的。为了表达这些想法，AG2增加了下述的三个谓词：

1. `distmeq a1 b1 a2 b2 ... an bn t1 t2 ... tn y` 表示：$t_1 \log(A_1 B_1) + t_2 \log(A_2 B_2) + \dots + t_n \log(A_n B_n) + y = 0$
2. `distseq a1 b1 a2 b2 ... an bn t1 t2 ... tn` 表示：$t_1 A_1 B_1 + t_2 A_2 B_2 + \dots + t_n A_n B_n = 0$
3. `angeq a1 b1 a2 b2 ... an bn t1 t2 ... tn y` 表示：$t_1 d(A_1 B_1) + t_2 d(A_2 B_2) + \dots + t_n d(A_n B_n) + y = 0$，其中 $d(AB)$ 是无向线段AB和水平线之间的角度。

另一类AG1不支持的类别是所谓的轨迹问题，主要描述的是如点、线、圆的对象的运动。AG2通过一个新的谓词语法捕捉这种运动。Table 2列出了11个轨迹的例子，以及相关的谓词和它们的语法。此处，作者利用了新的标记 `*` 来充当固定点的占位符。

> Table 2 | 11类轨迹型命题，以及它们在AG领域语言下，相关的谓词语法。

进一步地，在AG2的证明中，作者引入了明确的谓词，来表示拓扑 / 非退化条件的几何图检查。

1. `sameclock a b c d e f` 表示：方向 $A \to B \to C$ 与 $D \to E \to F$ 有同样的顺时针方向。
2. `noverlap a b` 表示：A和B是不同的点。
3. `lessthan a b c d` 表示：$AB<CD$ ，是一个在 $SSA$ 的三角全等定理中使用的声明。

AG2也能通过引入一个新的谓词，来证明点是相同的，`overlap a b` (点A和B是重合的点)，其中任何涉及A点的谓词也能够被用于B点，反之亦然。在推理闭包中，重合点可以通过成为同一个圆的圆心来定义；因此，作者引入了另一个谓词 `cyclic_with_center` 来捕捉这种情况。此处，`cyclic_with_center a1 a2 ... an x` 表示 $a_1 = a_2 = \dots = a_x$ 是圆的圆心，直到 $a_{x+1 \dots a_n}$ (以免 $x=0$，它也等价于 `cyclic`)。

> Notice that, when describing a problem, AG1 uses at most 2 predicates to define a point, i.e. each point is defined as the intersection between at most two objects (line or circle). This limits AG1 to only constructive problems - problems where all points can be straightforwardly constructed by following their definition order and taking the intersection of two well-defined objects.

注意到，当描述一个问题时，AG1使用最多2个谓词来定义一个点，也就是说，每个点被定义为至多两个对象(线或圆)的交点。这限制了AG1，使其仅能解决构造型问题，这些问题中，所有的点都是由它们的定义顺序，以及两个已经明确定义的对象的交点来构造的。

在AG2中，作者放宽了这个约束，来覆盖更多的问题，那些问题中能够由最少三个谓词来定义，使得图形的构造不再平凡(non-trivial)。作者用于自动化该过程的方法将在下一节中讨论。

在该部分中描述的所有更新，提高了AG语言的覆盖率，根据2000-2024年所有IMO的几何问题，从66%到88%。剩余的12%包含3D几何、不等式、非线性方程，以及可数的但很多的点(也就是那些含有n个点的问题，而n是一个任意的正整数)。所有被AG1和AG2覆盖或没覆盖的问题，都可以在Figure 8中找到。那些不能被覆盖的问题都被标为“Not attempted”。

# 3. Automated problem formalization and diagram generation

## Automated formalization

AG以及其他类似的神经-符号系统的一个很大的弱点，是需要将输入的问题，从自然语言人工翻译成一种(几何)领域内专用的语言。例如，一道简单的以自然语言描述的几何问题：“给定一个三角形ABC，边AB=AC，证明角B等于角C。”，会被翻译成：`triangle a b c; a b = a c ? eqangle b a b c c b c a` ，这才是AG专用的语言。

将这个过程自动化之后，就是所谓的形式化(formalization)，这是一个非常热门的研究领域。这相比于人类语言之间的翻译，是一个复杂得多的问题。翻译只是为了保持原意，而形式化则频繁地需要将原问题重新公式化(re-formulating)成一种可以替换的形式，而且有时候还需要剔除原本题目声明中，那些存在二义性的细微差别(disambiguating the nuances)。

自动形式化(auto-formalization)，因而需要极强的背景知识，以及本身就需要的问题求解能力。考虑到最近的基础模型开始展现出这种能力，作者也就使用了一个这类的模型，Gemini，来自动为AG完成形式化的工作。

作者开始是手工将几十个几何问题翻译成AG语言，然后利用这些例子来撰写一个少样本提示词(few-shot prompt)，要求Gemini将一道给定的几何问题，从自然语言翻译成AG语言。作者使用该提示词问询Gemini五次，然后再次调用Gemini，要求将这些结果合并为一个最终的答案。通过这种方法，作者得以形式化39道可形式化的IMO 2000-2024几何问题中的30道。对于更加简单的几何问题，它的表现非常稳定，而且几乎不会犯错。

## Automated diagram generation

另一个作者提出的主干管线中的人工部分，则是图形生成。在AG1中，每个点都由Table 1中至多两个基本的谓词所定义，因此问题都是构造性地定义的，而且图形也能被自动地生成。

> In AG2, we allow one or multiple points being defined simultaneously by an arbitrary number of predicates, allowing us to also cover non-constructive problems.

在AG2中，作者允许一个或多个点被任意数量的谓词同时定义，这就允许我们也能够覆盖那些非构造性的问题。

考虑一个非构造性问题的声明：“令ABC是一个含有内心I的三角形，使得$IA = 2IB$”，此处的I点不仅被定义为了内心，也就是两条内角平分线的交点，还被第三个谓词“$IA = 2IB$”所定义，而且并没有通用的能够构建这四个点的策略。既然AG2覆盖了那些非构造性的问题，图形构建变成了主干中一个重要的部分，而且通常需要人为干预。与Kruger (2021)的工作类似，作者提出了一个全新的算法，在给定非构造性问题要求的情况下，自动生成图形。

作者从初始化几何点开始，他们采用三种不同的初始化方法，在不同的尝试中轮流使用：

1. 随机 - 点是用正态分布随机选择的。
2. 按顺序构造。
3. 通过启发式选择的顺序进行构造。

为了构造出新的点 $X$，作者会查看每一个涉及 $X$ 点的谓词 $p(X, A_1, \dots, A_k)$，以及已经构造了的点 $A_1, \dots, A_k$，并找到点 $X$ 必须处于的一个圆或一条线上。例如，如果我们知道 $|A_1 A_2| = |A_3 X|$，是我们已经构造完成的，我们就知道 $X$ 必须在以 $A_3$ 为圆心的圆上。

如果我们不能得到一个这样的圆，或一条线，我们就会跳过这个谓词。如果没有这样的集合，那么点 $X$ 就会用一个正态分布进行随机采样。如果正好只有一个这样的集合，$X$ 就会从中采样得到。如果存在更多这样的集合，$X$ 就会从其中任意两个集合的交集中采样得到。作者为 $X$ 采样了10个例子，并且最小化下述的损失函数（为了改善探索，也添加了噪声）。

> After point initialization, we optimize their coordinates. Let $\bar x \in \mathbb R ^{2n}$ be a vector representing all coordinates of all points. We encode every exact constraint $c$ in the diagram, including the goal, as $f_c (\bar x) = 0$ with a nonlinear function $f_c$ , and each topological constraint as $g_c (\bar x) < 0$ , or $h_c (\bar x) \ne 0$. We numerically search for a suitable $\bar x$ in two steps.

在点初始化完成后，作者会优化它们的坐标。令 $\bar x \in R^{2n}$ 是一个向量，表示所有点的所有坐标。作者将图形中的所有约束 $c$，包括目标，都编码成 $f_c(\hat x) = 0$，其中 $f_c$ 是非线性函数，同时每个拓扑约束都编码成 $g_c (\bar x) < 0$，或者 $h_c (\bar x) \ne 0$。作者用两个步骤来从数值上搜索一个合适的 $\bar x$。

用于Adam优化器的整体损失，包括如下几项：

1. $\Sigma_{c \in C} \ f_c (\bar x)^2$ ，其中 $C$ 是所有确切的约束的集合，
2. $\Sigma_{c \in C} \ \text{softplus} (g_c(\bar x))$，其中 $C$ 是所有要求 $g_c (\bar x) < 0$ 的拓扑约束的集合，
3. $\Sigma_{c \in C} \ \text{softplus} (\text{min} (h_c(\bar x), -h_c(\bar x))$，其中 $C$ 是所有要求 $h_c (\bar x) = 0$ 的拓扑约束的集合，
4. 所有点坐标的 $L_2$ 范数的分数形式的非退化损失，
5. 对于所有不同点对 $A, B$ 的形如 $1 / (|AB|^2 + \epsilon)$ 的非退化损失。

作者对于10个初始的设定(setups)，同时运行固定步数的ADAM梯度下降优化。然后，作者会筛选出那些损失值低于给定阈值，而且所有拓扑约束条件都满足的图形。最后，作者从梯度下降优化切换到高斯-牛顿-列文伯格方法(Gauss-Newton-Levenberg)，以寻找一个联合欠定和超定部分的非线性方程组的数值解。

这种三阶段的优化方法与 Krueger (2021) 中介绍的方法论相似。最后一个阶段解决了在原始方法中，调整梯度下降优化时遇到的实际限制，即实现始终令人满意的误差范围，被证明是颇具挑战性的。

作者在形式化成AG语言的44道IMO题目上，对该方法进行了基准测试(见 Figure 8)，并且找到了其中43道题目的图形。作者在一个循环中运行三阶段的收敛程序，该循环在失败后能够重启，并生成另一个随机的初始构型。通过这种方法，43/44的问题能够在一个小时内，生成它们的图形。

# 4. Stronger and faster symbolic engine

所谓符号引擎，是AG的核心组件。作者称之为DDAR，也就是推理数据库(Deductive Database)和算数推理(Arithmetic Reasoning)。它是一个计算推理闭包的算法，也就是在给定一个核心初始结论(facts)集合的情况下，得到的所有推理结论(facts)的集合。DDAR根据一个固定的推理规则集合，来构建这个推理闭包，迭代地将新的结论(facts)加入到这个推理闭包中，直到没有更多(的结论)能够被添加其中。

DDAR既驱动训练数据生成，用于作者的语言模型，也在测试时证明搜索期间，指导了推理步骤的搜索。在这两种情况中，速度是关键。更快的数据生成允许更大且更激进的数据筛选(filtering)，同时更快的证明搜索又能促使更广泛的搜索，这个过程在一个给定的时间限制内，增加了找到一个解答的可能性。

在接下来三部分中，有三个DDAR的主要改进会被讨论。

- 处理“重合点”的能力
- 更快的算法
- 更快的实现

## Handling double points

当重新实现DDAR时，作者试着去保持与原算法大致相同的逻辑强度，只因实现的不同变得略长一些(例如：Thales定理被更加通用的圆心角定理所替换)。然而，DDAR1缺少了一个关键的功能，对于处理有难度的问题是很关键的：它不能接受两个点有相同的坐标，但却有不同的名称。

例如，想象一个问题是：两条线 $a, b$ 相交于点 $X$，需要证明 $X$ 处在一个确定的圆 $\omega$ 上。最合理的方法可能是通过一种重新阐述的方法(reformulation)——与其证明 $a, b$ 的交点在 $\omega$ 上，我们可以证明 $a, \omega$ 的交点在 $b$ 上。这两个问题是等价的，(后者)却更容易证明，因为我们可以在圆上移动角度。

如Figure 1所示，为了在推理“重合点”问题时，实现“重新阐述”，作者通过接下来的4个步骤来完成该工作：

- 构造一个新的点 $X'$ 作为 a, $\omega$ 的交点（我们暂时还不知道 $X'$ 会与 $X$ 点重合）。该辅助构造必须由一个语言模型预测出来。
- 证明 $X$ 在 $b$ 上。
- 既然 $X$ 和 $X'$ 都处在 $a, b$ 上，我们可以推出：$X=X'$ 。
- 因此，$X$ 在 $\omega$ 上。

## Faster algorithm

> The DDAR1 algorithm is processing a list of rules, and tries to apply each rule to all combinations of points.

DDAR1 算法处理一个规则列表，并且试着将每一条规则，运用到所有的点的组合上去。(identical to FG)

该过程包括一个候选搜索步骤，其时间复杂度与点的个数呈多项式关系；以及一个子句匹配步骤，其时间复杂度与每个前提的子句数量呈指数关系。理论上，在AG1中搜索相似角的候选角，最差情况下的复杂度是 $O(N^8)$，几乎是最耗时的步骤之一。

指数级增长的子句匹配，则是另一个十分耗时的(expensive)步骤。为了让搜索更加高效，作者提取了所有核心的规则，并对其应用进行了硬编码搜索，从而降低了对AR子引擎进行查询的次数，降低到最多立方次的(cubic)数量级水平。更进一步的，作者抛弃了用于角度和距离的，比较显而易见的规则(例如，关于垂直或平行线的)——所有类似的推理都自动地在AR引擎中发生。

DDAR中的两个主要耗时部分，一个是一次对相似三角形的搜索，另一个是一次对圆内接四边形的搜索。在AG2中，作者设计了一个改进的DDAR2算法。

> For similar triangles, we go through all triples of points, hash their "shape", and detect a similar pair if the shape is recognized twice.

对于相似三角形，系统会认真检查所有点的三元组，哈希存储它们的“形状”，并在形状被二次识别时，检测到相似的一对(三角形)。

对于圆内接四边形，系统会遍历所有的(点X，线段AB)对(pairs)，并将 $(A, B, \angle AXB)$ 的值哈希存储。如果类似的三元组重复了，我们就得到了一个圆内接四边形。通过线段AB、或 $\angle AXB$ 的“值”，此处的值指的是，一个由AR子模型计算得到的符号标准形式。该子模型会跟踪那些已知的，关于角度、距离、以及对数距离的线性方程，理解它的代数结果，并且能够将任何线性表达式简化成它的标准形式。

## Faster implementation

尽管新的算法已经显著地加速DDAR，作者仍然通过在C++中实现它的核心计算(高斯消元法，Gaussian Elimination)来进一步加速。新的C++库，通过pybind11导出到Python中，比DDAR1要快300倍。为了对速度的提升进行基准测试，作者选取了25道DDAR无法解决的IMO问题，然后在一台有AMD EPYC 7B13 64 core CPU的计算机上运行测试50次。尽管DDAR1平均在1179.57 $\pm$ 8.055秒内完成了，但是DDAR2要快得多，在3.44711 $\pm$ 0.05476秒内完成计算。

# 5. Better synthetic training data

使用一个语言模型来辅助符号引擎，是AG1成功的关键，在挑选出的30道IMO问题中，将解出率从14道题(纯推理证明)提升到25道。该语言模型是在海量的，由算法自动生成的合成数据上进行训练的。在AG2中，作者使用了相似流程。

与AG1类似，作者的合成数据生成方法，从采样一个随机图形开始，并使用符号引擎来从中推理出所有可能的结论。对于每一个推理出的结论，有一个回溯算法被用来提取出相关的前提、辅助点、以及那些能够证明出结论的推理步骤。

> Our data generation method deliberately avoids the use of human-crafted problems as initial diagram seeds, and strictly starts from random diagrams. This design choice eliminates the risk of data contamination and allows for the exploration of theorem distributions that may extend beyond established human knowledge.

作者的数据生成方法有意地避开了将人类精心构造的(human-crafted)问题，用于初始的图形种子，并严格地从随机图形开始。这种设计选择，消除了数据污染的风险，而且使得对定理分布的探索变得可能，因为定理可能会扩展到，超越已经构建完善的人类知识的程度。

该方法与TG [[TongGeometry]] 的方法不同，TG依赖于人类的专家知识，以及现有的问题图形来引导和过滤数据生成。在AG2中，作者坚持使用随机图形作为初始种子，并力争获得更好的合成训练数据。

## Larger, more complex diagrams and better data distribution

首先，作者扩大了数据生成的资源，并且做了更加仔细的数据分布的重平衡(re-balancing)。具体数据在Figure 2中有展示，对比了AG2与AG1：

- 以两倍大小探索随机图形，允许提取出更加复杂的问题。
- 生成至多2倍复杂程度的定理，也就是点和前提的数量(的两倍)。
- 生成至多10倍更加复杂的证明，也就是10倍多的证明步骤。
- 针对问题(question)类型，有更加平衡的数据分布。
- 针对是否需要辅助点的问题，有更加平衡的数据分布。

## More types of theorems

除了生成定理，来证明经典命题，如“$AB = CD$”之外，AG2的数据生成算法也能生成“轨迹型(locus)”的问题，也就是断言如：“当X在线/圆Y上移动时，Z在一个固定的线/圆T上移动”。在Section 2中有介绍，这些命题并没有被AG1的数据生成算法所支持，因为并没有运动或者运动依赖(movements dependencies)。在AG2中，通过函数 $P(.)$ 做随机图形生成时，系统会记录每个点X的移动依赖。函数定义如下：

- $P(A)$：控制A运动的点的集合，其中A是一个点或一个点的集合，以一个构造性问题的声明方式定义。两个P的例子列在Table 3中，而那些被检测为轨迹型命题的所有案例，都展示在Table 5中。

| If                                | Then                |
| --------------------------------- | ------------------- |
| `a midpoint b c, d=mid point a c` | $P(d)=\{ b, c \}$   |
| `a=on_line b c`                   | $P(a)=\{a, b, c \}$ |

Table 3，展示了P的两个例子：

- 第一行：既然d已经被单独地定义为a和c的中点，a被单独地定义为b和c的中点，那么，d点的移动来源就是b和c。即：`If a=midpoint b c, d=midpoint a c, Then P(d)={b,c}`。
- 第二行：因为a可以出现在线段bc上的任何位置，a自己就是移动来源的一部分。即：`If a=onl_line b c, Then P(a)={a,b,c}`。

## Faster data generation algorithm

作者也提升了数据生成算法的速度。回忆一下AG1中的算法，作者首先在随机图形上运行了推理闭包，并且“回溯”来获得最小的问题和最小的，能够证明闭包中每个结论的证明。为了获得AG1中的最小问题，作者不得不不知疲倦地(exhaustively)从题目中去除点的不同的子集，并重新运行DDAR来检查可证明性(provability)。这样的搜索可以以最小的基数来找到子集，但是作为一个指数级增长的搜索来说，对于更大数量的点，这种方法就不可行了。

因此，作者更换成了Figure 3中所示的一种贪婪丢弃算法(greedily discarding algorithm)，该算法仅用线性数量次的检查，即可判断一组点是否足以证明出目标。贪心算法能保证找到一个对于包含关系而言，最小的点集，只要检查是单调的(monotonic)。(如果 $A \subseteq B$, 那么 `check_provable(A)` $\Rightarrow$ `check_provable(B)`)。

实际上，作者也需要修剪过的集合，来在构造依赖下，保持闭包的状态(closed)，这样的话，我们就仍然可以进行一个随机的构造。如果我们将这个条件用于 `check_provable` 谓词中，它就不再单调了。该难题可以通过，按照逆拓扑顺序处理的，图3中的算法来解决（首先处理不依赖于任何其他点的点，最后处理构造的起始点）。

```python
def prune_points(points: set[Point], check_provable: Callable[set[Point]], bool):
	pruned = set(points)
	for p in reverse_topological(points):
		if check_provable(pruned - {p}):
			pruned = pruned - {p}
	return
```

> Figure 3：展示了一个基本的贪心算法，用于找到满足单调谓词检查的，最小的，点的集合。

# 6. Novel search algorithm

在AG1中，作者使用了一个简单的束搜索来发现证明。在AG2中，作者设计了一种全新的搜索算法，其中，几个不同配置的束搜索都并行执行，并且通过一个知识共享机制来互相帮忙(见Figure 4)。

> To improve the robustness of our system we use multiple different language models for each search tree configuration. We call this search algorithm Shared Knowledge Ensemble of Search Trees (SKEST).

为了提升系统的鲁棒性，作者采用多个不同的语言模型，用于搜索树的设置。作者把这个搜索算法称为：搜索树的共享知识集成。

这个算法是这样工作的：在每个搜索树中，一个节点与一个辅助构造的尝试相关，紧接着的是符号引擎的一次运行尝试。如果该尝试成功了，那么所有的搜索树都会终止。如果该尝试失败了，该节点会将符号引擎成功证明的结论，写入到一个共享的结论数据库中。

这些共享的结论会被过滤，从而，它们不再是特定于该节点的辅助点，而是只与初始问题相关。通过这种方式，这些结论对于相同搜索树中的其他节点来说，也能派上用场。以下列出了多种类型的搜索树，来确保搜索空间中的不同部分，都被有效地探索过了：

- “经典的”搜索树：与AG1中所使用的集束树搜索相同，一个语言模型会被要求在每个节点上，生成一个辅助点。
- 树会对每个节点都预测多个辅助点：语言模型在每个树的节点上，被允许生成它想要的，尽可能多的辅助点。回想一下，这种操作是可能的，因为作者的语言模型被训练成生成完整证明，从辅助点开始，紧接着是推理步骤。需要注意，尽管作者想要模型在一次查询中，生成所有必要的辅助点，实际上他们观察到，在给定先前生成辅助点的情况下，还是有必要去多次调用模型。允许模型生成多个辅助点，加速了找到一个解答的时间，而且还有效地增加了树的搜索深度。
- 树会统一地预测不同类型的辅助点。回想语言模型对于辅助点的输出，形如：`x00 a : cong a b c d (00) coll a e f (01)`，也就是：“构造点a满足 $ab=cd$，而且 a e f 是共线的”。通常要预测辅助点，作者会用起始符号 `x00` 来提示语言模型，并让其生成剩余部分。此处，取而代之的是，作者用 `x=00 a : cong`, `x=00 a : coll`, `x=00 a : cyclic`, `x=00 a : perp` 等等提示词来提示模型，迫使其对于前面四个符号是均匀分布的，然后再让语言模型生成剩余的部分。
- 更深但是更窄的树，例如集束大小64，深度10。
- 更浅但更宽的树，例如集束大小512，深度4。

## System design details

对于证明搜索，作者使用 TPUv4 来服务每个模型的多重副本，并让在相同模型中的不同搜索树，在它们自己的搜索策略下，对同一个服务器进行查询。除了异步地运行这些搜索树之外，作者还异步运行语言模型的工作进程与DDAR的工作进程。

语言模型的进程会将其探索过的节点的内容，全部写入一个数据库，然后DDAR进程会异步地整理这些节点，并进行尝试。DDAR进程之间会自我协调，保证它们会平均分配工作。单一DDAR进程池在不同问题间共享（如果多个问题同时被解决），因此，那些被更早求解的问题，会释放它自己的DDAR计算资源，用于那些正在被求解问题。

> Figure 4 | 作者的搜索算法的总览。作者应用了几个不同的搜索树，它们能够通过一个特殊的知识共享机制，来共享它们已经证明的结论(facts)。

# 7. Better language model

最后一个AG2的改进，是一个新的语言模型，在这部分，作者会讨论他们全新的训练和推理设置。

## Training setup

AG1的语言模型，是一个定制的Transformer，以无监督的方式(fashion)进行训练，主要有两个阶段：

- 在包含或不含辅助构造的问题上进行训练。
- 紧接着，只在包含辅助构造的问题上进行训练。

对于AG2，作者利用了Gemini训练管线，并简化训练过程到一个阶段：在所有数据上进行无监督训练。作者的新语言模型是一种基于Transformer架构的，稀疏的，混合专家(Mixture-of-Expert)模型，该模型以Gemini为基础进行构建，并使用Section 5中描述的AG2数据进行训练。作者使用三种训练设置，训练了多个不同大小的模型：

1. 在(几何)领域专用语言下，从零开始训练一个定制化的分词器(AG1设置)。
2. 在自然语言下，微调已经预训练过的，定制化的，数学专用Gemini模型(在附录A中有更多细节)。
3. 从零开始进行多模态训练，额外增加图像输入，该图像是一个给定几何问题的示意图。

除了一个大约3亿条定理的，超大规模合成训练数据集之外，作者还创建了三个评估集合：

1. 包含以及不包含辅助点的合成问题集合，“eval”。
2. 只包含辅助点的合成问题集合，“eval_aux”。
3. 已经被之前的AG所解决的IMO 2000-2024，几何题目的特殊集合，“imo_eval”。

所有这些数据集都包含完整的证明，而且在训练过程中，作者会计算它们的困惑度损失(perplexity loss)。然而需要注意的是，这些只是一些代理指标(proxy metrics)。首先，在推理阶段(如同AG1一样)，作者只使用语言模型建议的辅助点，但是困惑度是在整个证明上进行计算得到的。其次，求解一个给定的问题，可能存在多种方法，但是困惑度却是针对一种特定的解法计算得到的。

就如同AG1一样，作者主要的下游指标是IMO问题的求解率，在这个过程中，语言模型生成辅助点，然后通过Section 6中描述的束搜索运行DDAR。这些运行的结果会在Section 8中讨论。

作者使用 TPUv4 硬件所允许的最大的批量大小，来训练他们的模型。学习率变化是：先线性预热(warm-up)，再余弦退火。超参数是通过 Scaling Laws来确定的。在Figure 5中，作者展示了对于不同大小的Gemini模型的学习曲线，不同模型有不同大小的参数个数。跟预想的一样，增大模型大小，会降低模型在训练、评估、以及作者特制的IMO评估集上的困惑度损失。

## Inference setup

一个新的问题是通过Section 6中描述的搜索算法解决的，该算法使用多个搜索树，以及多个不同大小的语言模型。相比于AG1，作者使用top-k采样，温度t=0，k=32。需要注意，一个更高的温度，以及多重采样，对于求解IMO问题来说是非常必要的。

如果贪心解码(Greedy Decoding)的温度t设为0.0，k设为1，不使用树搜索，作者的模型只能解决26道需要辅助构造的问题中的2道。提升温度t至1.0，并使用k=32的采样(不使用搜索树)，能让模型求解26题中的9题。更低的温度 $t<1.0$ 并不能够生成足够多样的辅助构造(见Figure 6)，然而更高的温度，会使得语言模型更高次数地输出错误几何语言的语法。

### The analysis string

在AG1中，语言模型LM和DDAR之间的推理是很少的：DDAR采用LM提出的辅助构造，同时LM在DDAR成功找到一个解法时，就停止提出辅助构造。在AG2中，作者丰富了该神经-符号推理，通过让语言模型在提出辅助构造之前，了解DDAR做出的推理。也就是说，作者会将下述信息喂给语言模型：

- $S_1$：给定初始问题前提，可以由DDAR推理的结论集合。
- $S_2$：给定初始问题前提，并且假设目标谓词也正确时，可以由DDAR推理的结论集合。
- $S_3$：在数值上正确的结论集合(通过检查图形验证)。

通过定义我们可以注意到，$S_1 \subset S_2 \subset S_3$。一旦这三个集合被计算得到，作者就会将其序列化，并将它们连接(concatenate)到一个名为 _analysis string_ 的字符串中，当然，使用的是几何专用的语言。该字符串会被喂给语言模型，与初始问题的描述一起，如下所示：

$$
\text{<problem\_statement>} \ \text{serialized}(S_1) \ \text{serialized}(S_2-S_1) \ \text{serialized}(S_3-S_2)
$$

作为对比，输入AG1的格式仅为：`<problem_statement>`。

# 8. Results

作者主要的下游指标是：在IMO几何问题上的求解率。在IMO 2000-2024 之间，有一共45道几何问题，作者将其翻译成50道AG问题，称该数据集为IMO-AG-50。一些问题由于作者形式化的特性，被分为了两道。

Figure 8展示了主要的结果：AG2求解了50道题目中的42道IMO几何问题，因而首次超越了金牌选手的平均水平。更多的细节展示在Table 4中，表中对比了各种AG2设置与其他系统的区别，比如AG1和TG。作者也进行了一个额外的验证，在一个30道最难的IMO入围问题组成的数据集上，形式化成AG2的语言，这些题目从来没有在IMO上出现过。对于这些额外的结果，可以查看Appendix D。

在Figure 7中，作者展示了IMO的解决率作为训练时间（训练期间已处理的token数）的函数，针对一个与通过Section 6中描述的“经典”树搜索进行DDAR的语言模型相结合的情况。有趣的是，AG2在仅经过250个训练步骤，批量大小为256的情况下，就能解决50道题目中的27道，这大约相当于处理了2亿个标记(tokens)。作者还对推理设置如何影响整体性能，进行了消融研究（见Figure 9）。

> For a single search tree we find that the optimal configuration is beam size of 128, beam depth of 4 and 32 samples. More samples or a larger beam search does not help solve more problems.

对于单个搜索树来说，作者发现：最优的设置是128的束大小，束深度为4，采样数为32。更多的采样数，或更大的束搜索并不会帮助求解更多题目。

作者团队内的几何专家和IMO奖牌获得者，认为许多AG的解法展现出了超越人类的创造性(superhuman creativity)。在Appendix C中，作者提供了几个这样的例子，并且有详细的分析。

在所有未解决的IMO问题中，有2道尝试了但没有解决，以及6道不可形式化的问题。不可形式化的问题中，包括了不等式，以及可变的点的数量，这些都还暂时没有被AG2的语言所覆盖。

> Two of the remaining unsolved IMO problems (IMO 2018 P6, IMO 2023 P6) involve advanced geometry problem solving techniques such as inversion, projective geometry or radical axis, which are not implemented in our current DDAR.

两道未解决的问题，包含了高级的几何问题求解技巧，比如反演(inversion)、射影几何、或根轴，这些内容并没有在作者最新的DDAR中有所实现。

虽然从理论上讲，这类问题可以在不使用这些技巧的情况下得到解决，但这样的解决方案将需要更长的推理时间、更长的证明过程、以及更多的辅助构造，以此来弥补作者的DDAR中，缺乏上述机制所带来的不足，而这些不足阻碍了AG当前的问题解决能力。

# 9. Conclusions and Future work

这篇文章提出了AG2，一个对于AG的显著升级版，在几个关键领域，解决了之前的局限性，并提升了性能。AG2采用了更强的语言模型，该模型在更大且更多样的数据集上进行训练；一个更快更通用的符号引擎；一个扩展的几何专用语言；以及一种全新的证明搜索算法。这些改进使性能有了质的飞跃，AG2在IMO 2000-2024几何问题上的求解率达到84%，较其前身的54%解决率有了显著提升。

作者还介绍了几项与语言建模(language modeling)相关的研究：

- 首先，作者表明：他们的模型在生成辅助构造方面相当强大，而且还能生成完整的证明，这展示了现代语言模型在不依赖外部工具（如符号引擎）的情况下运行的潜力。

> Secondly, we discovered that for AlphaGeometry neither a tokenizer, nor a domain language used to train the model, plays a decisive role.

- 其次，作者发现对于AG而言，无论是分词器，还是用于训练模型的几何语言，都不起决定性作用。对于使用小词汇量的自定义分词器和通用的Gemini大型分词器，作者得到了相似的结果。在特定领域语言中，进行训练与在自然语言中进行训练得到的结果相似。
- 第三，作者比较了从零开始训练，和在数学数据集上预训练的语言模型进行微调这两种方式。他们发现：尽管是在相同的AG数据集上进行训练，这些模型学习到的技能略有不同，而且将它们整合到作者的新型搜索算法——搜索树的共享知识集成中，可以提高整体的求解率。

尽管在所有IMO 2000-2024几何问题上，取得了令人瞩目的84%的求解率，但仍有改进的空间：

- 首先，作者的领域语言不支持描述可变数量的点、非线性方程、以及涉及不等式的问题，若要实现“完全求解几何问题”，这些问题必须得到解决。
- 其次，AG2尚未解决所有的IMO和IMOSL (国际数学奥林匹克竞赛短名单)问题。作者假设，将问题分解为子问题，并应用强化学习方法或许能够弥补这一差距。
- 最后，在本文中，作者报告了构建一个全自动几何问题求解系统的进展。该系统以自然语言作为输入，并能可靠地输出解决方案，且不会出现任何幻觉。尽管初步结果良好，但作者认为，通过增加更多的形式化示例，并进行有监督的微调，自动形式化的能力还可以进一步提升。

# Third-Party Analysis

[AlphaGeometry2: Deepmind AI outperforms math Olympians at geometry tasks](https://the-decoder.com/alphageometry2-deepmind-ai-outperforms-math-olympians-at-geometry-tasks/)

> Another interesting finding is that language models pre-trained on mathematical datasets and then refined on AlphaGeometry data acquire slightly different abilities than those trained from scratch. Although both learn on the same data, they develop complementary strengths. By combining these models in a new search algorithm called SKEST (Shared Knowledge Ensemble of Search Trees), the solution rate can be further increased.

另一个有趣的发现是：先在数学数据集上预训练，然后在AG的数据上进行优化的语言模型，与从零开始训练的模型相比，获得了相比之下略微不同的能力。尽管两者都在相同的数据上进行学习，但它们发展出了互补的(complementary)优势。通过将这些模型组合在一个名为SKEST（搜索树的共享知识集成， Shared Knowledge Ensemble of Search Trees）的新搜索算法中，解题率可进一步提高。

> As far as can be seen from the work, the language models used have not yet been trained as reasoning models with the currently used RL methods - further performance improvements are therefore possible. It is therefore likely that the next version will rely more heavily on reasoning models and may reduce the role of the symbolic engine, at least experimentally.

目前，从这篇工作中所能看到的情况来看，作者使用的语言模型，并未用到最近被广泛采用的强化学习方法，作为推理模型进行训练——性能还有进一步提升的可能。因此，下个版本很可能会更多地依赖推理模型，并且至少在实验阶段可能会减少符号引擎的作用。

因此，这个用于展示神经符号人工智能性能的示例系统的工作，也反映了当前人工智能研究中的一个核心争论：深度学习模型能否可靠地进行推理？或者更确切地说：像大型LLM这样的生成式Transformer模型，能否学会可靠地推理？虽然AG2清晰地展示了神经-符号系统的优势，但该团队对大型语言模型作用的见解，并没有给出一个明确的答案。
