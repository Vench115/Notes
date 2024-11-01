>GEX for Geometry Expert, mentioned in Alpha Geometry.

[A Deductive Database Approach to Automated Geometry Theorem Proving and Discovering | Journal of Automated Reasoning (springer.com)](https://link.springer.com/article/10.1023/A:1006171315513)

- Shang-Ching Chou
- Xiao-Shan Gao
- Jing-Zhong Zhang

# Abstract

作者努力构建了一个几何推理数据库(A Geometry Deductive Database)，能够用于寻找一个几何构型(Geometric Configuration)中的不动点(Fixpoint)。该系统能够找到构型中，利用一个固定的几何规则集合所推导出的所有性质(Properties)。为了控制数据库的大小，作者提出了一个结构化推理数据库(Structured Deductive Database)的构想。

作者的实验显示，这种技术能够上百倍地减少数据库的大小。作者也提出了基于数据的搜索策略，来改进前向搜索法(Forward Chaining)的效率。作者也在几个问题上取得了明显的进步，包括：

- 如何选择好的几何规则
- 怎样增加辅助点
- 怎样自动构造数值图作为模型

该程序已经被160个非平凡的几何构型所验证。对于这些几何构型，该程序不仅找到了它们中，大多数人们熟知的性质，而且常常给出意想不到的结果，有些甚至可能是新的结果。同时，由程序生成的证明大体上都是比较短的，而且完全是几何学的证明。

# 1. Introduction

## 1.1 Intro

该论文的目标是开发一个几何推理数据库，其能够被用于证明或发现非平凡的几何定理，方法是通过探索前向链接法的全部能力。对于一个给定的几何构型，程序能够找到其有关于一个固定的几何定理或公理的不动点；换句话说，它能够找到构型的所有性质，这些性质是能够利用这些公理所推理得出的。

程序背后的基本思想，例如不动点和否定句，来自于推理数据库理论。作者的主要贡献如下：

1. 一般情况下，作者提出的结构化推理数据库和基于数据的搜索策略的思想，可以用于改善搜索的效率；
2. 在集合推理的设定下，作者展示了怎样选择一个好的规则集合、怎样增加辅助点、以及怎样自动地构造数值图形。

作者用了160个几何构型，从众所周知的几何定理，例如质心定理、垂心定理、Simson定理，到近期于美国数学月刊(American Mathematical Monthly)中问题板块新提出的问题。该程序不仅找到了这些构型中广为人知的性质，也尝尝给出许多意想不到的结论，有些有可能是全新的结论(见 Example 6.2)。如作者所知，160条定理中大多数不能用之前基于综合的路径所证明。作者提出的程序的强大能力主要基于下述的改进。

在传统的推理数据库中，每个n元的谓词与一个n维的关系有关。既然几何谓词中的大多数都满足特殊的性质，例如传递性(transitivity)和对称性(symmetry)，那么，传统的表示数据库的方法并不适用于构建一个几何的推理数据库，原因有如下两点：

1. 过于庞大的数据库
2. 信息的重复表示

平均来说，用于160个已经测试过的几何构型的数据库，如果使用传统的表示方法，其大小为242117。作者通过使用一些简单地数学结构，例如：序列和等价类来表达数据库中的实体，以解决这个问题。最后，用于160个构型的、结构化数据库的平均大小为221。所以，结构化的数据库是近百倍地小于传统的数据库。

作者提出的基于数据的搜索策略，是与之前的、基于规则的搜索策略相对的。在基于数据的搜索中，作者保持了一个“新数据”的列表，以及对于每个新数据，系统搜索的规则集合（极强的数据库）来寻找并应用这些规则，从而使用那些数据。

由于重复地对相同的实体应用一条规则，而产生的冗余的推理，如果使用了基于数据的策略，这些冗余会被自动地消除(eliminated)。在这个方面，基于数据的策略与半随机(semi-naive)的策略是类似的。

然而，基于数据的策略也使用了组合的规则以及动态的数据库，来更新策略，以更好地改善效率。同时，基于数据的搜索策略在结构化的数据库中，也尤其的高效。因为运用这样一个数据库，一个实体能够代表大量的信息。例如，一个实体可以是“十个三角形互相相似”。

所有前述的、基于综合方法的工作，都运用了关于全等三角形的相关几何规则，作为其基本的几何规则。不额外增加关于辅助点的方法，这些规则可以被用于证明有限数量的高中等级(high-school-level)的定理，那些定理只包含直线。在几何中大多数的基本结论，例如质心定理，垂心定理，以及Simson定理都炒过了这些规则的范畴。

在Section 2，作者会讨论三个关于选择一个几何规则的集合的考量：

1. 证明步骤的数量
2. 辅助点
3. 顺序关系

作者也会展示怎样通过选择比较好的规则，来解决一些相关的问题。

作者讨论了怎样使得在几何问题中增加辅助点，或Skolemization(斯科勒姆化)变得实际可行。大约二十条有关于增加辅助点的规则被实现，而且160个被程序解决的构型中的39个需要辅助点，这首次实现了一个非平凡的、增加辅助点的规则集合。

几何的数值图声明，被用作模型，来处理规则中的负面信息(negative information)。作者提出的程序能够自动地构建图，对于一个线性构造的几何声明的类来说（见Section 2.3）。在所有之前的使用图作为模型的工作中，图都是由用户自己构建的。

>斯科勒姆化：数理逻辑和计算机科学中的一种技术，用于消除存在量词，将逻辑公式转换为等价的斯科勒姆标准形。

## 1.2 Related Work

对于自动几何推理，主要分为三种方法：

- 基于综合推理的方法
- 基于代数计算的方法（Wu's method & Gröbner basis method）
- 几何变量方法（例如面积法等等）

综合方法与改论文的方法非常接近。大多数对于自动几何定理证明的方法搜会使用后向链式方法。在一篇论文中，Nevins使用了前向链式搜索和后向链式搜索的组合，主要集中在前向链式搜索中。但是不动点却没有达到。所有的综合方法，除了Nevins之外，都使用了数值图作为模型。

在Section 5中，增加辅助点有更详细地讨论。

作者的程序主要使用了全角法作为中心理念，能够自然地解决有关于圆的问题。

...

# 2. The Geometric Rules

为了选择一个“更好的”几何(推理)规则的集合，作者需要考虑以下的问题。

***Auxiliary Points***

由于大多数综合的几何推理系统，都会使用霍恩子句(Horn clauses)作为规则，那么该系统就**没有能力**去生成辅助点。因此，很重要的是，我们至少可以用挑选过的几何规则来证明很大一部分的几何定理。

关于全等三角形的规则，被很多先前的工作用在综合的、自动几何定理证明上，并没有上述的性质。大多数广泛使用的定理，包括一些基本的结论，例如垂心定理和质心定理，并不能在不增加辅助点的情况下，仅通过这些规则进行证明。作者所使用的规则在这个方面改进了很多：160个用作者的程序所证明的、不添加辅助点的几何定理中，大部分不能被之前的程序，基于全等三角形进行证明。

***Order Relations***

大多数只涉及等式的、初等几何定理的证明，与所涉及到的点的相对顺序位置是无关的。这样的几何定理属于所谓的无序几何(unordered geometry)。这种思想起源于吴文俊的自动推理代数方法。在无序几何中，这些定理的证明可以非常简单。然而，这些定理的常规证明，都包含顺序关系（或不等式）；因此他们不仅非常复杂，而且也不严密(strict)。基于全等三角形的方法并不适用于无序几何。

在使用这种方法的工作中，推理中所需要的顺序关系要么来自于数值图，要么来自于(人工)输入。因此，这些程序仅仅只能用于证明几何定理中的特殊情况。从另一个角度来说，作者所使用的规则是用于无序几何的。

例如图1中，定理“一个平行四边形的对角线互相平分”的一个证明，是基于三角形ABO和CDO的全等性的，相应的，也就会基于∠OAB和∠OCD是平行线段AB和CD所得到的内错角。在这个语句中，我们隐含地假设了点B和点D是在线段AC的异侧。但是这件事，在大多数机器生成的证明中，并不是被逻辑上严格证明的。如果一个用于无序几何的规则集合被使用了，例如这篇文章中提到的这个，那么该定理的一个证明并不需要点B和点D在线段AC的异侧这个事实。

## 2.1 The Geometric Rules

一条规则被称为一个确定的霍恩子句，当它含有如下的形式时：

$$
\begin{matrix}
 Q(x) :- P_1(x), \dots, P_k(x) \ \text{meaning} \\
 \forall x [(P_1(x) \wedge \dots \wedge P_k(x)) \Rightarrow Q(x)]
\end{matrix}
$$

其中，$x$ 是存在于几何谓词 $P_1, \dots, P_k$ 以及 $Q$ 中的点。同样地，$(P_1(x) \wedge \dots \wedge P_k(x)) \Rightarrow Q(x)$ 以及 $Q(x)$ 被分别称为规则的“规则体”和“规则头”(body and head)。在推理基础的领域，只有不带有函数符号的、确定的霍恩子句，在作者的程序中是被允许的。因而，代数计算是不被允许的，因为代数计算实际上就是函数符号，而且可能很轻易地导致无限长的推理序列，从而使得抵达不动点变得不可能。

作者使用了如下的谓词：

- points
- coll (collinear)
- para (parallel)
- perp (perpendicular)
- midp (midpoint)
- cyclic
- circle
- eqangle
- cong (congruent of segment)
- eqratio
- simtri (similar triangle)
- contri (congruent triangle)

中心思想就是等角(eqangle)。此处的角并不是常规所说的角，而是全角(full-angle)。直观来说，一个全角，$\angle [u,v]$ 是从线 $u$ 到线 $v$ 的角度。需要注意，$u$ 和 $v$ 并不是常规对于角的定义中的射线。如果两个角 $\angle [l,m]$ 和 $\angle [u,v]$ 之间，存在一个旋转 $K$ 使得 $K(l) \parallel u$ 而且 $K(m) \parallel v$，那么这两个角就是相等的。如果A,B和C,D是在 $l$ 和 $m$ 上分别不同的点，那么 $\angle [l,m]$ 也可以标记成 $\angle [AB,CD], \angle [BA,CD],\angle [AB,DC],\angle [BA,DC]$ 。

全角法的引入极大地简化了角度相等的谓词。例如，我们有如下关于平行线和角度的规则。(Figure 2)

$$
R1. \ AB \parallel CD \  \text{if and only if} \ \angle [AB,PQ] = \angle[CD,PQ]
$$

如果使用常规的角度，我们需要指定八个角之间的关系，而且我们需要使用顺序关系（不等式）来区分不同情况。例子：……其中说明了平行所导致的角相等，可能会有点在异侧的情况，也有可能会在推导过程中引发分支问题。

在不同的顺序关系下，同一条定理的不同图形，这种关系可能会难以处理，得到的结果也会有所不同。而运用全角法，不同的情形可以被统一地处理。同时，也要注意此处运用的全角法是与之前不同的，之前使用了代数计算以及后向搜索作为推导工具，而此处只是角全等的概念被使用了。

程序使用大概70条规则（见附录）。规则中的一些描述了几何谓词的基本性质，有一些比较重要的如下列出。需要注意的是，作者也用 $\angle [AB,CD] = \angle [PQ,UV]$ 以及 $AB=CD$ 来表示全角和分段相等(segment congruences, 段同余)。尽管在常见的教科书中，没有这种公理，但是这些规则都是基础的几何事实，也能够毫不费力地证明。

在常见的教科书和之前综合的方法路径中，三条关于角全等的定理，$s.s.s, \  s.a.s, \  a.a.s$ 都是关键的推理规则。在作者的程序中，只有 $a.a.s$ 被使用(Rule 14)。边角边的规则，在使用全角法的情况下，并不正确。边边边的规则是正确的，但是在一般情况下，并不能生成关于全角的新的性质。

在一个推理数据库视角下，这些规则都是高度复杂的：所有的谓词都是相互递归的(mutually recursive)，而且大多数规则都不是线性的。作者提到，从某种意义上说，这些规则也不完备，因为一条用作者的谓词所描述的定理可能并不能用他们提出的规则进行证明。

## 2.2 Nondegenerate Conditions

>非退化情况

在某些规则中，它们的本体是包含否定的。严格地说，这些规则并不是霍恩子句。作者使用 *negation by failure* 的准则(criteria)来处理这个问题，也就是说，作者假设 $\neg P$ 是成立的，如果 $P$ 不能由程序推导得出。这可能会导致不一致性，因为作者所使用的规则并不完备，因此如果证明 $P$ 失败并不代表 $\neg P$ 是成立的。作者通过两个步骤来解决这个问题。

首先，无论何时被使用，一个否定的条件会被加入到命题的假设中，并且被称为几何命题(geometry statement)的非退化情形（nondegenerate (ndg) condition）。向几何声明中加入非退化情形是被允许的，原因如下：教科书中，对于大多数几何定理的原始描述，都会隐含地假设一些必要的情形，也就是由作者的方法所找到的一部分的非退化情形。对于一些由之前综合证明器所证明的定理，必要的非退化情形是遗漏的。更多关于非退化情形的内容可以在几篇文章中找到。

第二，假设我们已经有了对于问题声明的一个精确的数值图。当我们在一个规则中遇到一个否定谓词 $\neg P$ 时，证明器会检查 $P$ 在图中是否正确。仅当 $P$ 在数值图中是错误的时候，这条规则才可以被使用。所以，作者就避免了对于“平凡正确的”声明的可能的证明，也就是那些假设可能不一致的声明。

## 2.3 Constructing exact numerical models

>构造精确的数值模型

使用精确的数值图作为语义(semantic)模型，对于大多数之前的、关于综合的、机械几何定理证明来说，已经成为了基石。早期的，起源于数值图的证明器有两个优势：

1. 图像被用作过滤器，在后向搜索过程中，拒绝那些与它本身的数值表示不一致的目标
2. 更重要的是，数值图被用于决定点和线段之间的顺序关系

第一个优势对于后向搜索非常重要，而且在前向搜索中也并非毫无用处。第二个优势与生成独立于图的证明问题相关。在这篇文章中，图被用于处理霍恩子句中的否定信息(negative information)。

在先前的工作中，图是由使用者准备好的。但在作者的程序中，图是自动地为一类线性构造的几何声明而生成的。一个几何声明(statement)，应该是线性构造的，如果声明中的点可以被列成一个序列，从而，序列中的每个点都可以从序列中先前的点中构造出来。更加准确地说，一个几何的声明是线性构造的，如果其中的点能够被以下的构造所描述：

- 取一个自由点
- 取一条线上的一个任意点
- 取两条线的交点
- 取一条线和一个圆或两个圆之间的交点，当另一个交点已经被构造好时

例如Figure 1中的构造顺序：……

广泛使用的几何声明中的大多数是线性构造的。例如，文献中的512条几何定理中，80%都是这一类的构造。

对于一个线性构造的几何声明来说，每个点的坐标都能被表示成有理表达式(rational expressions)，这个式子是用先前点的坐标所表示的。因此，通过对声明中的点的坐标分配随机的数字，我们就可以轻易地计算出所有点的坐标（有理数），从而，对于该声明来说，也能计算出一个精确的数值模型。

如果一个几何声明不是线性的，那么构造出它的图像，我们通常需要代数来表示坐标。同样的，如果一个声明是可约的(reducible)，那么该声明就需要不止一个图像。作者提出的程序不能对每个这样的声明构造出一个精确的数值模型。

# 3. Structured Database

## 3.1 Structure of the Database

在传统的表示一个关系数据库的方法中，每个n元的谓词都与一个n维的数组相关。因为大多数我们使用的几何谓词都满足特殊的性质，通过传统方法建立的数据库会导致非常大的数据库。这一部分的主要目标，是设计一个结构化的数据库，其大小可以被有效地控制。主要思想是通过数据库的结构，来表示那些仅仅包含一个谓词的性质。换句话说，我们把关于谓词的一些规则融入了数据库的结构当中。以下，是三条准则：

1. 对于谓词要使用规范的(canonical)形式。一个几何性质能够被多种谓词形式所表示。例如，谓词coll满足以下的规则：
	1. $coll(A,B,C) :- coll(A,C,B)$
	2. $coll(A,B,C) :- coll(B,A,C)$
	因此，从共线中，我们可以获得五个“新的”事实，也就是点序的不同组合。为了节省空间、加快搜索速度，我们通过给几何声明中的点分配一个顺序，从而将这些谓词表示为规范的形式。有了这样的一个顺序之后，谓词就可以被唯一地表示。更进一步的，通过使用这种规范的形式，上述的规则在推理步骤中并不明显需要。这会在推理过程中，减少规则的数量。
2. 使用等价类(equivalent classes)来表示一些谓词。我们可能会用序列来表示特定的传递的几何谓词。例如，多个点在同一条线上的事实，可以被一个点的序列表示。在谓词的形式中，我们却需要 $n(n-1)(n-2)$ 个不同的形式来表示这个事实。
3. 对于等价类使用有代表性的元素。一些谓词本身就是关于等价类的几何关系。从这个意义上，我们会使用一个典型的元素来表示这个等价类。例如，一条线上有很多点，这条线平行于包含另一些点的线，能够被直接表示成 $l_1 \parallel l_2$ ，如果我们用 $l_1$ 和 $l_2$ 来表示这两条线的话。如果用谓词 $\text{para} (A_i, A_j, B_k, B_l)$ 来表示的话，我们会需要 $2n(n-1)k(k-1)$ 个谓词。

接下来，作者给出了数据库的结构。在最顶层，事实满足：每个谓词由下述的一个结构列表所表示。

coll. para. perp. eqangle. ……

DEFINITION 3.1：数据库中，由上述内容所描述的一个元素，被称为相关谓词的一个“数据(data)”或一个“事实(fact)”。如果 $d$ 是一个谓词 $P$ 的事实，那么我们也称 $P$ 为 $d$ 的谓词。传统意义上的事实，例如 $coll(A,B,C)$ 和 $para(A,B,C,D)$ 对于固定点(concrete points) $A, B, C, D$ 来说，被称为简单事实。

我们也需要知道如何跟踪推理路径，对数据库中一个给定的事实做出一个证明。为此，当更新数据库中的一个事实时，我们会将旧的事实复制到一个新的位置，并更新新的事实，保持旧事实完好无缺。对于每一个事实，我们也需要保留信息，比如引理以及用了什么条件来得到这个状态。数据库中，一个事实的实际结构如下所示：

$$
[TYPE,LEMMA,COND,DATA,LINK]
$$

其中，TYPE 表示这个事实是否是“旧的”，因为它已经被更新了；LEMMA 包含了被用于获得这个事实的规则或公理；COND 包含了用于获得该事实的事实；DATA 是结构化形式的事实；LINK 指向数据库中的下一个数据。

上述的结构并没有为了大小控制而优化。通过引入更复杂的结构，我们可以进一步缩减数据库的大小。但是，数据库结构越复杂，处理它的程序就越复杂。这里我们需要考虑数据库大小与实现难度的权衡(tradeoff)。从以下事实来看，我们知道上述的结构已经可观地缩减了数据库的大小。

对于160个由作者的证明器所解决的几何构型，如果上述的结构被使用，数据库平均的大小是221。使用谓词形式，平均大小会是242117，或者大一百倍。对于许多如例6.3的构型，如果没有使用上述的结构，我们并不能在合理的(reasonable)时间中达到不动点。

## 3.2 Generating proofs

在达到不动点后，我们可以对数据中的每个事实生成一个证明。这个任务并不简单，主要是因为我们使用了一个没有复杂结构的数据库。一个直白的推理步骤的输出并不容易理解。

我们已经在Section 3.1.1中提到，对于数据库中的每个事实，我们都省略了那些用于获得该事实的引理和条件的名字。所以，一个证明步骤有如下的形式：

$$
(R): \ C:- P_1, \dots, P_k
$$

其中，C是一个简单的事实，而 $P_i$ 要么是事实或简单事实。C总是一个简单事实，原因是：在第一步，C是一个用户想要证明的几何声明，而接下来的步骤中，这由下述的追踪步骤所保证。

接下来的三个策略被用于上述的追踪步骤中：

1. 找到缺失的条件。由于结构化数据库，一些条件是被隐含地假设的，因此在数据库中，这些条件是缺失的。在追踪过程中，我们需要将它们补全，来生成易于理解的证明。最常见的缺失条件是共线(collinear)。
2. 找到合适的条件。如果一个谓词是由一个序列表示的，它的事实可能会非常大。在追踪过程中，我们需要从中找到正确的、最简的事实。
3. 避免冗余。在推理输出一个事实 $P_i$ 之前，我们需要检查它是否之前已经被输出过了。如果是的话，我们只需要输出之前输出过的事实即可。

# 4. Search and Control Strategies

## 4.1 Data-Based Search

既然我们想要达到不动点，广度优先的前向搜索对于我们来说是一个很自然的选择。

从一个已知的几何声明假设集合 $D_0$ 中，根据规则集合 $R$ 不断进行推理，然后得到新的已知条件集合 $D_k$ 。不断重复这个过程，直到我们达到不动点。

$$
D_0 \overset{R}\subset D_1 \overset{R}\subset \dots \overset{R}\subset D_k \  \text{(Fixpoint)}
$$

因为一个几何声明的假设集合是有穷的，而且我们使用了一个有限的规则集合，而且不含有函数符号，这意味着不动点总是可以达到的。同时，不动点是唯一的，而且不依赖于搜索策略和规则应用的顺序，这些也非常清楚。

一般来说，广度优先搜索是极其昂贵的。然而，我们对于几何推理的实现却表现优异。这源自几个原因，作者使用了结构化的数据库，来大量地减小数据库的大小，而且减少了规则的数量，因为他们已经被融入进数据库的结构中了。而且，作者使用C语言的实现，是专用于几何领域的。

一个关于基于数据搜索的有趣的事实是，规则应用的顺序是由 `new-fact-list` 中的事实的顺序决定的：规则是根据 `new-fact-list` 顶部的事实种类所选择的。用户可能会给谓词分配一个重要程度的顺序。那些被越常使用的谓词，例如中点、平行、和垂直，会有很高的顺序。在搜索的过程中，程序会根据这个顺序，排列新的事实列表，以至于那些有很高排序的事实会成为列表中的第一条数据。用这种方法，规则被应用的顺序可以自动确定。

另一个有关于基于数据的搜索的改进是，构建组合的规则 (form combined rules)。也就是把几个规则组合起来，看做一个规则使用，以加快搜索的效率。

## 4.2 Avoiding redundant deductions

当一个推理生成已经存在于数据库中的事实时，这个推理就是冗余的。冗余的推理过程对于加快搜索来说，是一个主要的障碍……

主要有三类冗余的推理。首先，对同一事实重复地使用相同的规则，会产生同样的结果。这类冗余可以通过半朴素(semi-naive)的搜索，或作者提出的基于数据的搜索来解决。

第二，一些冗余的推理在逻辑上是有保证的。对于一个给定的规则集合，得到一个极小的、逻辑等价的规则集合的问题是不可判定的(undecidable)。既然作者使用的规则集合是固定的，那么我们就尝试通过几何直觉来去除那些冗余的规则。基于图像的标准规则可以被认为是这些方法的特殊情况。

第三，两个逻辑上不等价的推理可能给出相同的事实，如果输入的事实满足特定条件的话。我们称这类冗余为有条件的冗余(conditional redundancies)。看起来没有通用的方法来控制这些有条件的冗余。

在作者的程序中，启发式搜索方法被设计成解决这类冗余。

1. 在搜索前检查结论。如果结论已经存在于数据库中，那么显然先检查结论，并不做任何事是更快的。
2. 避免同义反复(Tautologies)。如果规则的组合是一致的，那么这些规则的连续的推论会给出相同的事实。程序中会一直记录推出事实的引例，也就能很轻易地检测并删除这类冗余。
3. 避免空事实。如果一个事实无法推出任何新的信息，那么作者将其称为空事实。这种设计也是一种自然的，避免数据库过度增长的方法。
4. 基于图的标准形式。许多产生冗余的规则都与图有关。既然这样，我们就可以删除一些规则来避免冗余。

# 5. Constructing Auxiliary Points and Skolemization

逻辑上，构造新的点与存在量词(existential quantifiers)的斯科勒姆化(Skolemization)有关。甚至在第一个几何定理证明器被开发之前，A. Robinson 就表示，在一个证明中，那些被需要的辅助点和线可以被构造为，问题的Herbrand Universe的元素。基于相似的想法，Reiter提出了一种能够生成新的点的推理方法。但是上述的想法都没有被实现。

有一点非常明确，构造辅助点可能会导致无限的几何实体，而且会阻止我们抵达不动点。而且，引入新的点可能会极大地增加数据库的大小。作者使用了两个策略来控制增加新的点，以达到预期的效果。

作者提出的第一种策略是将增加新的点的过程，从抵达不动点的过程中剥离。程序严格地按照下述内容运行。对于一条几何定理，我们首先在不添加辅助点的情况下，找到定理相关构型的一个不动点。如果该命题的结论已经存在于数据库中，那么程序就会终止。否则，程序就会试着去构造一个辅助点，将与辅助点相关的事实(facts)添加到最新事实列表中(new-fact-list)，并且尝试寻找一个新的不动点。程序会一直重复这个过程，直到结论已经出现在新的数据库中，或者当前已经不存在新的辅助点。

如果上述的策略没有被使用，我们可能会遇到不太舒服的情况，就是问题声明的结论，不能在不增加辅助点的情况下被推理得到，但是在寻找第一个不动点的过程中，许多辅助点仍然会被构造。构造许多无关点可能会强烈地拖慢搜索速度。

作者提出的第二个策略就是使用两种启发式搜索方法，来控制构造过多的辅助点：

1. 在一个辅助点被增加，并且一个新的不动点被抵达后，我们会检查关于原始图像的新的性质是否被找到。如果找到了，我们就会保留这个辅助点；否则，该辅助点会被删除。
2. 递归的辅助点是不被允许的。换句话说，我们构造一个辅助点，仅仅可以使用原始声明中存在的点。

第二个条件保证了程序一定会终止。

构造辅助点的规则实际上是Section 2给出的规则的改进，也就是说，一个全称量词被改为了一个存在量词。下述的内容是四个构造辅助点的规则。A1和A2与S1和R3相关。

……

总共，作者提出了大概二十条关于构造辅助点的规则。大多数使用实验性增加辅助点的“方法”都被选做规则(rules)。这些规则是不完整的。作者提出的程序所解决的160个集合构型中，39个是需要构造辅助点的。它们中的两个例子列在例子6.4和6.5中。

在两篇论文中，两个构造被考虑了：构造新的点和在两个存在的点中构造新的线。在作者的程序中，如果超过两个点在这些线上，或者他们在以下谓词中被使用了，比如：para, perp, and eqangle，那么，连接所有可能点对(pair of points)的线会被存储进数据库中。换句话说，在程序中，图中连接任意点对的线都被认为存在，而且是不需要被增加的。

# 6. Conclusion

## 6.1 Implementation

在这篇文章中汇报的方法，实际上是作者的几何推理系统中的证明方法的实现之一，Geometry Expert。

对于一个给定的几何声明(Geometry Statement)，程序的工作流程如下：

1. 首先，生成一个数值图作为模型，并把假设设置为新事实列表(new-fact-list)。随后，如果新的事实存在，那么就进行前向搜索(Forward Chaining)；如果不存在，那么就检查是否达到一个不动点，而且结论是成立的。
2. 如果达到不动点，而且结论合法成立(Conclusion Valid)，那么程序就会终止。如果没有，那么就检查是否需要添加辅助点。
3. 若不需要添加辅助点，那么就使用决策步骤，例如吴法或者面积法；如果需要，那么回到步骤1。

## 6.2 Applications

1. 自动定理证明和搜索
	- 作者提出的程序可以被用于证明一个给定的几何定理，这件事非常明了。一个更有意思的应用是，发现“新的”有关于几何构型的事实。
	- 在前向搜索中获得的任何东西，都可以被视作一个“新的”事实。作者的实验表明，他们的程序能够发现很多已知的结果，而且常常会有意想不到的结论。
	- 比如说(Figure 6)中，与垂心定理相关的简单构型为例。作者的程序发现了最常提到的性质：
		1. 三条高是相等的
		2. 全角∠EG,CG = 全角∠CG,FG
	- 程序也找到了105个非常关键的不同比例，而且是在这么一个简单的构型中。在Example 6.2，程序甚至发现了一些新的结果。
2. 用于其他推理方法的基础(Basis)
	- 作者计划开发一个几何推理系统，同时拥有综合的和代数的方法。对于一条几何定理，系统首先会利用综合方法，来形成一个数据库。
	- 如果结论存在于数据库中，那么一个对于它的总和证明就可以被生成。否则，系统会利用决策步骤来证明定理，比如面积法，而且数据库可能会帮助这些决策方法来产生更多优雅的(elegant)证明。
3. 几何教学(Geometry Education)
	- 从一个几何声明的假设开始，前向推理来证明结论，是几何中基本的证明方法之一。
	- 作者的程序把这个过程机械化了，并且给了它更多的能力。通过增加一个美观的界面(nice interface)，该程序就能够教一位学生怎样用前向搜索来证明一个特殊的定理；或者怎样去探索出一个给定构型中的有趣的性质。
	- 对于其他应用，因为最新的程序能够在几秒之内，生成一个构型中所有的性质，一个几何老师就可以利用它来选择学生的练习或者例子，对于一本几何教科书来说。

## 6.3 Experiment results and examples

作者给出了几何构型和定理的来源，出自几篇文章中。大概有600条定理可以用吴法或者面积法解决。作者也能够找到这600条定理的不动点，但是它们之中仅160个可以用这种方法证明。

一些非常知名的定理，例如Pappus's theorem和Pascal's theorem超越了作者程序的范畴，尽管在程序中可以达到不动点。对于Pappu's theorem来说，不动点就是原始假设的集合。对于Pascal's theorem，不动点包含89个事实。因此，尽管作者的方法可能是最强的综合方法，他们的推理数据库方法仍然要比代数方法更弱一些。

从另一个方面来说，作者的方法有如下的优势：

1. 它能够被用来自动地发现定理
2. 生成的定理是更加智能的
3. 对于一些定理，推理数据库方法能够生成优雅的证明，而代数方法却不能证明它们，因为需要极其大量的计算机内存和计算时间。

利用作者接受的几何规则，人们可以找到这些定理的“短证明”（可读证明）。

有最大数据库的谓词是eqratio。如果不含有这个谓词，数据库的大小会急剧地缩小。所以一个启发式搜索方法可能是：首先，在不考虑谓词eqratio的情况下，获得一个不动点；如果结论不在数据库中，那么就包含eqratio再获得一个新的不动点。
