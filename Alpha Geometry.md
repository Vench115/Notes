[Solving olympiad geometry without human demonstrations | Nature](https://www.nature.com/articles/s41586-023-06747-5)

# Solving Olympiad Geometry without human demonstrations

- 通过解决数据稀缺问题，切入解决几何问题
- 通过解决数据问题，解决了辅助构造问题
- 通过解决辅助构造问题，解决了(很多)复杂几何问题
- 求解几何问题时，引入天才般的概念
	- 依赖性差异
	- 语言模型学习构造
	- 回溯求解最小依赖
	- 暴力美学的又一典范！

# Abstract

能够证明奥林匹克等级的数学定理，不可否认地代表了在人类等级自动推理方面的重要里程碑，由于它们的难度在大学前，世界上最有天赋的学生中享誉盛名。然而，近些年出现的机器学习的技术路径，并不适用于大多数的数学领域，因为这种方法需要将人类的证明步骤转化为机器可验证的格式，而这个过程代价极高。

这个问题在几何领域甚至更加严重，因为几何问题独特的转换(translating 形式化)要求带来了诸多挑战。这个问题所带来的结果就是，训练数据的严重缺失(scarcity)。

>We propose AlphaGeometry, a theorem prover for Euclidean plane geometry that sidesteps the need for human demonstrations by synthesizing millions of theorems and proofs across different levels of complexity.

所以，DeepMind团队推出了AlphaGeometry，一个用于欧式平面几何的定理证明器(prover)，它能够规避(sidesteps)对人类证明演示的需求。运用的方法简单来说，是通过合成出成百万上千万条，贯穿不同难度等级的定理和证明。

>AlphaGeometry is a neuro-symbolic system that uses a neural language model, trained from scratch on our large-scale synthetic data, to guide a symbolic deduction engine through infinite branching points in challenging problems.

AlphaGeometry是一个神经-符号(neuro-symbolic)系统，利用一个神经语言模型，在作者提出的大规模合成(synthetic)数据上，从零开始训练。并在具有挑战性的题目中，即那些无穷无尽的分支节点上，引导一个符号推理引擎，进行解题。

在一个最近的30道奥赛级别的测试题目集中，AlphaGeometry解决了25道，超过了先前最好的、只能求解10题的方法，而且AG能够接近IMO金牌选手的平均表现水平。

尤其需要说明，AlphaGeometry可以生成人类可读的证明，解决了IMO 2000和IMO 2015中所有的几何问题，并且能够接受人类专家的评估检验。同时，它也在2004年的IMO定理中，发现了一个更为普遍的版本。

# Main

## 1 (问题背景)

(能够)证明定理，意味着完全驾驭(mastery)了逻辑推理，以及一种能够在朝向目标的无限动作空间中，进行搜索的能力。同时，这也象征着强大的解决问题的能力。自20世界50年代起，追求更好的定理证明能力，已经成为了AI研究领域一个持续的焦点。

数学奥林匹克竞赛是世界上最驰名的定理证明竞赛，这项比赛可以追溯到1959年，与前述的AI研究几乎拥有同样长的历史，而在评定对于问题的求解能力上，这项比赛也扮演着相当重要的(instrumental)角色。能够在奥赛级别，做到匹敌顶级人类选手的表现，已经成为了AI研究领域一个重要的里程碑。

## 2 (面对的难题)

定理证明对于基于学习的方法来说非常困难，因为在大多数学领域中，能够将人类的证明，翻译成机器可验证语言的训练数据极其稀少。不仅如此，相比于其他奥赛领域，几何领域脱颖而出的原因，是它几乎没有已经用通用数学化语言(比如Lean)形式化好的证明例题(examples)，这也是由于几何问题中，非常独特的形式化(translation)难度。

从另一个角度来说，几何专用的语言，是被非常狭义地(narrowly 严密地)定义的，而因此不能完全表达(express)很多人类的证明方法，因为那些证明中使用了不局限于纯几何领域的工具，比如复数。

总的来说，这些困难造成了数据上的瓶颈，导致几何领域在最近的、利用人类演示的研究中落后(lag behind)。因此，近几年解决几何问题的方法，仍然主要依赖于符号的方法，以及由人类设计的、硬编码的(hard-coded)启发式搜索方法(heuristics 试探法)。

## 3 (提出的方法)

DeepMind团队提出了一种利用合成数据进行定理证明的方法，因而可以回避(sidestepping)翻译(形式化)人类提供的证明例子。作者团队专注于欧式平面几何领域，并且排除了比如几何不等式(geometric inequalities)以及组合几何(combinatorial geometry)的主题。

通过在一个随机定理前提(random theorem premises)的丰富(diverse)集合下，运用现有的符号引擎，作者提取了一亿条(100 million)合成的定理以及它们的证明，其中有许多含有超过200个证明步骤，四倍于奥赛定理的平均证明长度。

作者在综合证明生成上，更进一步地定义并且使用了**依赖性差异**(dependency difference)的概念，使得他们的方法能够生成接近一千万(10 million)条综合证明步骤。这些步骤能够构造辅助点，从而超越纯符号推理的范畴。

辅助构造(Auxiliary Construction)是外生项生成的(exogenous term generation)几何实例(instance)，(辅助构造)代表了定理证明中无穷无尽的分支系数(branching factor)，而且在别的数学领域，这也被广泛地认为是许多困难定理证明的关键挑战。

因此，DeepMind团队的工作展示了一种生成综合数据，并以此学习，来解决这个关键挑战的成功范例。通过这种解决方法，作者提出了一种普遍的指导框架，并在方法部分，也就是“AlphaGeometry framework and applicability to other domains”中，讨论了它在其他领域的适用性。

## 4 (实践)

作者在所有生成的综合数据上训练了一个语言模型，并微调使其能够在证明搜索的过程中，更关注辅助构造，最后将所有的推理证明步骤输入(delegating)一个专门的(specialized)符号引擎。

这种方法沿袭了许多领域中广泛应用的方法，也就是将例如 GPT-f 的语言模型，在人类的证明例子上训练后，就可以生成外生的(exogenous)证明项(terms)，作为如 nlinarith 或 ring 这些又快又准的符号引擎的输入，从而充分融合两类方法(using the best of both worlds)，达到两全其美的效果。

如Fig. 1中所示，作者提出的几何定理证明器AlphaGeometry，能够生成人类可读的证明，极大地超越了先前SOTA的几何定理证明机器程序，并且在一个由30道形式化自IMO的，经典几何问题所组成的测试集上，接近了IMO金牌选手的平均表现。(Fig. 2)

>Fig. 1：作者提出的神经-符号AlphaGeometry以及它如何解决一个简单的问题和解决IMO 2015 Problem 3的总览。
> 
>首行展现了AlphaGeometry如何解决一个简单的问题。
>
>a. 一个简单的例子以及它的图表。题目是：”让ABC是任意一个AB=AC的三角形，证明∠ABC=∠BCA“
> 
>b. AlphaGeometry通过运行符号推理引擎初始化它的证明搜索。推理引擎不知疲倦地从定理前提中推理出新的语句(statements 命题)，直到定理得证或者新的语句被耗尽。
> 
>c. 因为符号引擎证明失败，所以语言模型构造了一个辅助点，在符号引擎重新尝试之前扩展(grow)新的证明状态。这种循环一直持续，直到解法被找到。
> 
>d. 对于简单的例子来说，循环在第一个辅助构造“D为BC中点”后终止。证明包含两个其他步骤，这两个步骤都利用了中点的性质：”BD=DC“并且”B,D,C是共线的“，在图中步骤用蓝色高亮指出。
>
>底部的一行展现了AlphaGeometry怎样解决IMO 2015 P3。
>e. IMO 2015 P3问题声明和图表
> 
>f. IMO 2015 P3有三个辅助点构造。在两种解法中，作者让语言模型输出交错于(interleaved)符号引擎的输出，来反映出他们的执行顺序。此处需要指出，图f中IMO 2015 P3的证明，为了便于解释，是被大量缩减和编辑过的。它的完整版本在Supplementary Information中。

>Fig. 2 AlphaGeometry将集合定理证明器的最新能力，从低于人类水准提升至了接近IMO金牌选手的水准
>
>测试基准包括了自2000年至今的IMO官方问题，这些问题是可以被文中所使用的几何环境所表示的(形式化)。通过IMO竞赛成绩从0-7分重新调整到0-1分的方法，来评估人类在竞赛中的表现，目的是为了匹配机器求解失败/成功的二进制输出。
>
>例如，一个奥赛选手在7分中得到4分，那么在这个对比中，他的得分会被调整到0.57。另一方面，AlphaGeometry以及其他机器求解器的得分要么是0(求解失败)，要么是1(求解成功)。
>
>我们也需要注意，这仅仅是在经典几何领域，与人类的一个大致的比较，因为人类工作在自然语言的命题中，而机器是工作在更加狭义的(narrow)、领域专用的形式化模式中(translations)。
>
>更进一步地，一般的IMO竞赛也囊括了其他种类的问题，例如几何不等式或组合几何，甚至其他的数学领域，例如代数、数论、或者组合数学。

# Synthetic theorems and proofs generation

## 1 (定理前提采样)

~~作者提出的用于生成综合数据的方法在Fig. 3中展示。~~他们首先采样一个定理前提的随机集合，充当符号引擎的输入来生成其派生(derivations)。~~一个完整的用于采样的动作列表能够在Extended Data Table 1中找到。~~

在作者的工作中，他们在一个高度并行的环境中，采样了接近十亿(1 billion)条类似的前提，这个过程在Methods部分有所描述。

>Note that we do not make use of any existing theorem premises from human-designed problem sets and sampled the eligible constructions uniformly randomly.

需要指出，作者并没有使用来自人类设计的问题集中，任何现有的定理前提，而是统一地、随机地采样了符合条件的构造。

## 2 (综合数据生成)

其次，作者在采样的定理前提上，使用了一个符号推理引擎。这个引擎遵循前向推理的规则(如Fig. 3b所示)，快速地推理出新的真命题(true statements)，该操作会返回一个有向无环图，图中包含所有的可达(reachable)结论。有向无环图中的每个顶点都是一个可达的结论，顶点的边连接到它的父节点，这种连接归功于一个回溯算法(traceback algorithm)，在Methods中有详细描述。

该方法允许一个回溯程序(process)从任意一个顶点 $N$ 开始递归地运行，最后返回它的依赖性子图 $G(N)$ ，该子图的根节点是 $N$ ，叶子顶点则是已采样前提的一个子集。将这个子集记为P，作者就得到了一个合成训练例子(synthetic training example)：

$$
(premises, conclusion, proof) = (P, N, G(N))
$$

>Fig. 3 AlphaGeometry 合成数据生成过程
> 
>a. 首先采样出一个由随机定理前提构成的巨型(large)集合。
> 
>b. 利用符号推理引擎来获得一个推理结论(deduction closure 推论闭包)，这个过程能够返回一个命题的有向无环图。对于图中的每个顶点，作者再进行回溯来找到必要的前提以及依赖演绎(dependency deductions 赤字 缺项)的最小集合。例如，对于最右边的顶点”HA⊥BC“，回溯过程会返回绿色的子图。
> 
>c. 最小的前提以及相关的子图构成了一个综合的问题及其解答。在最下面的例子中，点E和D也参与了证明，尽管他们与HA和BC的构造无关；因此，这些例子作为辅助构造被语言模型学习。

## 3 (符号推理引擎)

在几何中，符号推理引擎是一个推论的数据库(Deductive Database)，它的能力是从前提中，通过几何规则有效地推理出新的命题。DD遵循的推理规则，是以确定的霍恩子句的形式。也就是形如 $Q(x) \leftarrow P_1 (x) , \dots, P_k (x)$ ，其中，$x$ 是点对象，而 $P_1 (x) , \dots, P_k (x)$ 以及 $Q$ 是谓词，例如”相等“以及”共线“。推理规则的完整列表可以在 ref. 10 中找到。

为了拓宽生成的综合定理以及证明的范围，作者同样在符号引擎中引入了另一个组件，其能够通过代数规则(AR, Algebraic Rules)推理出新的命题，这在Methods也有所描述。

代数规则AR，对于角度、比例、以及距离追踪(distance chasing)都非常重要，而这些方法在许多奥林匹克竞赛级别的证明中都需要用到。作者在Extended Data Table 2中囊括了具体的AR例子。DD以及AR的组合方法，包括了他们两者的前向推理以及回溯算法，在作者的工作中是一种新的贡献，而且代表了在几何领域进行符号推理的SOTA方法。

# Generating proofs beyond symbolic deduction

生成超越符号推理的证明(序列)

到目前为止，(作者方法中)生成的证明仅仅包括了那些，已经可以被高效的符号推理引擎DD+AR所达到的(reached)推理步骤。然而，为了解决奥赛级别的问题，最关键的缺失部分是生成新的证明项(terms)。

在上述的算法中，那些证明项构成了独立于 $N(conclusion)$ 的 $P(premises)$ 的子集。换句话说，这些证明项是结论命题和结论对象之间的依赖差异(dependency difference)。

>We move this difference from P to the proof so that a generative model that learns to generate the proof can learn to construct them, as illustrated in Fig. 3c.

作者提出的方法将这种“差异”从P(前提)试图移动(move 推断)到证明，因而一个学习生成证明的生成模型，可以学着去构造它们。

这种证明步骤可以进行辅助构造，而符号推理引擎并没有被设计成能够进行辅助构造。在一般的定理证明场景中，辅助构造是外生证明项(exogenous term)所生成的一个实例，这对于所有证明-搜索算法都是一个显著的挑战，因为它会在搜索树中引入无穷的分支点。

在几何定理证明中，辅助构造自从1959年该领域的开端(inception)以来，就是一个存在已久的研究课题。

>Previous methods to generate them are based on hand-crafted templates and domain-specific heuristics, and are, therefore, limited by a subset of human experiences expressible in hard-coded rules.

先前用于生成辅助构造的方法，都是基于手工标注的模板(templates)，以及专用于该领域的启发式搜索方法，因此，该方法“受限于”人类的那些可以用硬编码规则所解释的经验。

>Any neural solver trained on our synthetic data, on the other hand, learns to perform auxiliary constructions from scratch without human demonstrations.

从另一个角度来说，在作者的综合数据上进行训练的任何神经网络解题器(solver)，都是不借助人类的演示，从零开始学习，来进行辅助构造。

>Fig. 3 AlphaGeometry合成数据生成的过程
>a. 作者首先在随机定理前提上采样出一个极大的集合
> 
>b. 作者使用符号推理引擎来获得一个推理闭包(deduction closure)。这个操作会返回一个命题的有向无环图。对于图中的每个顶点，作者对他们都会进行回溯，来找到顶点的必要前提以及依赖缺项(dependency deductions)的最小集合。例如图中所示的“HA⊥BC”，回溯会返回绿色的子图。
> 
>c. 最小的前提以及相关的子图，构造出了一个综合的问题及其解答。在最下面的例子中，点E和D参与了证明，尽管他们对于HA和BC的构造是无关的。因此，他们能够被语言模型学习，作为辅助构造的方式。

# Training a language model on synthetic data

Transformer语言模型是一个强大的深度神经网络，能够学习且通过“下一个token预测”的方法来生成文本序列，助力大量的生成式AI技术方面的进步。

作者把 $(P,N,G(N))$ 序列化成一个文本字符串，其结构是’\<premises\>\<conclusion\>\<proof\>'。通过在这样的符号序列上进行训练，一个语言模型能够高效地学习生成证明，条件就是定理的前提以及结论。

# Combining language modelling and symbolic data

从更高的层面来说，证明搜索是一个循环，这个循环中语言模型和符号推理引擎交替运行(take turns to run)，如在Fig. 1 b,c中所示。证明搜索在定理结论被找到，或循环达到最大迭代次数后停止。

语言模型用问题命题的字符串进行初始化(seeded 规定初始随机值)，并在每一轮都生成一个额外的语句，以问题命题和之前的构造作为条件，来描述一个新的辅助构造，例如“构造点X，使得ABCX是一个平行四边形”。

语言模型每生成一个上述的构造，都会提供给符号引擎作为新的输入来工作，因此，它的推理闭包(closure)就会扩大，潜在地能够抵达结论(reaching the conclusion)。作者使用集束搜索的方法来探索由语言模型生成的、最优的k个构造(top k constructions)。具体的并行证明搜索算法在Methods中介绍。

# Empirical evaluation

一个奥赛级别的、用于几何问题的衡量指标(benchmark)

现有的奥林匹克数学的评价指标并没有完全覆盖几何领域，因为其更加关注于，通用语言中的形式化数学语言，而形式化过程(formulation)在表示(representing)几何上增加了巨大的困难(pose great challenges)。

解决这些挑战就需要深厚的专业知识、以及大量的研究投入，因此超出了作者工作的范畴，因为那些研究更加关注于定理证明的方法论(methodology)。由于这个原因，作者将2000年以来IMO竞赛中的几何问题，改编进一个更细分的(narrower)、专业的环境中，该环境专为用于交互式图形证明助手的经典几何打造，此处也在Methods中有详细描述。

在IMO竞赛题中所有非组合的、几何相关的问题中，75%能够被(形式化)表示，所以作者得到了一个由30道经典几何问题组成的集合。例如，几何不等式和组合几何，是不能被形式化的(translated)，因为他们的形式化相比于经典几何有明显的不同。

作者在Supplementary Information中提供了声明和形式化30道题目的完整列表。最终的测试集命名为IMO-AG-30，补充材料中强调了它的来源、形式化的方法、以及它最新的大小。

# Geometry theorem prover baselines

几何定理求解器在传统的观点中一般分为两类。

## 1 (基于代数的方法)

第一类是计算机代数方法，他们将几何命题视为点坐标的多项式方程。证明的过程，则是通过大型多项式的特殊变换来实现的。Grobner基和吴法是这类方法中比较有代表性的，因为其理论上，能够确保决定(decide 求出)IMO-AG-30中所有的真值，尽管这类方法不能生成人类可读的证明。

因为这些方法通常带来巨大的时间和存储复杂度，尤其是当处理IMO等级的问题时。所以作者在报告中展示的结果是，只要该方法能够利用已有实现之一，并且能够在48小时内解决的任何问题，都被看作是成功求解。

## 2 (基于搜索的方法)

AlphaGeometry是属于第二类方法的求解器，通常被描述为搜索的、或者公理的、或者有时是“综合的”(synthetic)方法。这些方法将定理证明的问题，视作一个逐步搜索的问题，其运用的是一个几何的公理集合。拜此所赐，他们通常会返回高度可解释的证明，对于人类读者来说也是非常易读的。

这类方法中的基准方法(baselines)普遍包括符号引擎，引擎中也通常有人类设计的启发式搜索方法。例如，Chou等人提供了18种启发式搜索的方法，例如：“如果OA⊥OB而且OA=OB，在OA射线的反方向上构造C，使得OC=OA”，除此之外还有75个用于符号引擎的推理规则。

大语言模型例如GPT-4，也能够被视作是这一类的方法。因为大语言模型已经展现了其在各种推理任务上，超凡的推理能力。

>When producing full natural-language proofs on IMO-AG-30, however, GPT-4 has a success rate of 0%, often making syntactic and semantic errors throughout its outputs, showing little understanding of geometry knowledge and of the problem statements itself.

然而，当作者试图用GPT-4在IMO-AG-30上生成全自然语言的证明时，GPT-4只有0%的成功率，在整个输出的过程中，它通常会犯语法或语义错误。这表明了模型本身对于几何知识，以及问题命题本身的了解甚少。

需要指出的是，GPT-4在IMO问题上的性能也会被它训练数据中的公共解答所污染(contaminated)。因此，一个性能更好的GPT-4仍然不能与其他求解器相提并论。

总的来说，基于搜索的方法在他们的证明性能上并没有理论上的保证，并且也被广泛认为弱于计算机代数方法。

## Synthetic data generation rediscovers known theorems and beyond

合成数据生成重新发现已知定理并超越

作者发现他们的综合数据生成能够重新发现(rediscover)一些相当复杂的(fairly complex)定理，包括一些几何学界所了解的引理(lemmas)。如Fig. 4中所示，尽管是合成数据生成是从随机采样的定理前提开始的。

这可以归因于利用了复合操作(composite actions)，如在Extended Data Table 1中所描述的，比如“作一个质心(centroid)”或者“作一个旁心(excentre)”，在作者大规模探索的设定之下，这偶然地采样了人们熟知的定理前提的超集。

为了学习了解合成证明的复杂性，Fig. 4直观地展示了综合证明长度的直方图，并列展示了在奥赛问题的测试集上发现的证明长度。

>Fig. 4 生成的综合数据的分析
>
>在生成的综合证明中，9%是带有辅助构造的。合成的训练证明中，大概仅有0.05%长于AG在测试集问题上的平均证明长度。
>
>最复杂的综合证明长度有惊人的247，其带有两个辅助构造。大多数的合成定理前提并不倾向于人类发现的定理那样对称(symmetrical)，因为这些前提并没有倾向于任何美学标准(? they are not biased towards any aesthetic standard)。

>Although the synthetic proof lengths are skewed towards shorter proofs, a small number of them still have lengths up to 30% longer than the hardest problem in the IMO test set.

尽管综合证明长度已经朝着更短的证明被歪曲(skewed 削减)了，他们之中少部分的证明长度，仍然比IMO测试集中最难的问题要长30%。

>We find that synthetic theorems found by this process are not constrained by human aesthetic biases such as being symmetrical, therefore covering a wider set of scenarios known to Euclidean geom etry.

作者也发现，由这个过程所发现的综合定理，并没有受限于人类的“审美偏见(aesthetic biases)”，例如对称，因此也就能够覆盖更大的、欧氏几何中的情况集合。(AlphaGo点33)

>We performed deduplication as described in Methods, resulting in more than 100 millions unique theorems and proofs, and did not find any IMO-AG-30 theorems, showing that the space of possible geometry theorems is still much larger than our discovered set.

作者也进行了数据去重，结果产生了超过一亿条独立的定理和证明，而且作者所生成的定理并没有发现IMO-AG-30中的任何定理，所以他们认为，可能的几何定理空间，比他们发现的集合还要大得多。(crazy)

## Language model pretraining and fine-tuning

作者首先在全部一亿条综合生成的定理上训练语言模型，包括那些纯符号推理的定理。然后，作者在一个证明的子集上进行微调，这些子集的证明需要辅助构造，并且大概占了总体训练数据的9%，也就是九百万条(9 million)证明，目的是为了在整个证明搜索过程中更好地关注它被分配的任务。

# Proving results on IMO-AG-30

结果验证

在Table 1中展示了十个不同的解题器(solvers)在IMO-AG-30基准上的性能评估，其中八个，包括AlphaGeometry，是基于搜索的方法。

## 1 (prompt GPT-4)

此外，用提示词让GPT-4以自然语言生成完整的证明，过程包括多轮的反思和修改，作者也结合了GPT-4和DD+AR作为另一个基准，来增强它的推理准确度。为了达到这个目标，作者将非常详细的指令以及少样本(few-shot)的例子加入到提示词中，帮助GPT-4成功地与DD+AR交互，只要能以正确的语法提供辅助构造即可。涉及GPT-4的基准的提示词细节包括在Supplementary Information中。

## 2 (AG的结果)

AlphaGeometry达到了最佳的结果，总共解决25道问题。先前最好的方法(吴法)解决了10道，然而，最强的基准DD+AR+human-designed heuristics解决了18道题，因为使用了这篇工作中所开发的代数推理引擎，以及由Chou等设计的人类启发式搜索方法。

为了匹配AlphaGeometry的计算测试时间，这个最强的基准利用250个并行的核心(workers)运行了1.5小时，每个核心都会尝试不同的、由人类设计的、并行启发式搜索算法所提供的辅助构造的集合，直到搜索成功或超时。

其他的基准，如吴法，或全角法(full-angle method)的测试结果(baseline)并没有被并行计算资源所影响，因为他们实现的是固定的(fixed)、逐步的算法，直到终止。

## 3 (资源影响)

当度量了由基本符号推理引擎(DD)所带来的效果提升之后，作者发现融合代数推理，能够多解决7道问题，而来到14道(DD+AR)，然而，语言模型的辅助构造惊人地多增加了另外11道解决的问题，结果来到总共25道。

>As reported in Extended Data Fig. 6, we find that, using only 20% of the training data, AlphaGeometry still achieves state-of-the-art results with 21 problems solved.

如Extended Data Fig. 6中所报告的，作者发现，仅仅使用20%的训练数据，AlphaGeometry仍然可以达到最好的结果，解题21道。

>Similarly, using less than 2% of the search budget (beam size of 8 versus 512) during test time, AlphaGeometry can still solve 21 problems.

类似地，在测试时间中仅仅使用**少于**2%的搜索成本(集束搜索大小8 vs 512)，AlphaGeometry仍然可以解决21道题。

在一个更大、更多样的、包含231道几何问题的测试集中，其覆盖了课本习题、地区奥赛、以及著名定理，作者发现Table 1中的基准仍然保持了同样的性能等级(rankings)。其中，AlphaGeometry解决了几乎所有的问题(98.7%)，而吴法解决了75%，同时，DD+AR+human-designed heuristics解决了92.2%，如Extended Data Fig. 6b所示。

## 4 (优异效果及分析)

>Notably, AlphaGeometry solved both geometry problems of the same year in 2000 and 2015, a threshold widely considered difficult to the average human contestant at the IMO.

值得注意的是，AlphaGeometry解决了2000和2015同年中的两题，这被广泛认为是普通人类竞赛者在IMO中的分水岭。

更进一步的，AlphaGeometry的回溯过程找到了一个，在形式化的(translated) IMO 2004 P1中未使用过的前提(premise)，如Fig. 5中所示，因此，模型靠自己发现了一个，形式化的IMO定理。

>Fig. 5 AlphaGeometry发现了一个比形式化的IMO 2004 P1更普遍的定理
>
>图片左侧由上至下，是IMO 2004 P1的自然语言版本，及其形式化的声明，以及AG的解法。
>
>归因于必要的用于提取最小前提的回溯算法，AG认为证明中有一个前提是不必要的：O不一定需要是BC的中点，而让点P,B,C是共线的。
>
>右侧上方是原版的定理图片；下方，则是更加普遍版本的定理图片，其中O不再是中点，而P仍然在线段BC上。需要指出的是，原版的问题需要点P在点B,C之间，而更加普遍的定理和解法并不能保证该条件。

作者在Supplementary Information中包含了AG对于所有IMO-AG-30中题目的解法，而且，他们还人工分析了一些比较有意思的(notable)AG解法以及求解失败的题目，展示在Extended Data Figs. 2-5中。

>Overall, we find that AlphaGeometry operates with a much lower-level toolkit for proving than humans do, limiting the coverage of the synthetic data, test-time performance and proof readability.

总的来说，作者发现AlphaGeometry是用相比人类更加”低级的工具包“来解题，受限于合成数据的覆盖范围、测试时间内的性能、以及可读性。(?)

## Human expert evaluation of AlphaGeometry outputs

人类专家评估

因为AG能够输出高度可解释的(interpretable)证明，作者使用一个简单的模板(template 脚本)，来自动地把AG的解答翻译成自然语言。为了获得专家对于AG解决IMO 2000和2015中所有问题的评估，也由于AG已经潜在地越过了IMO金牌的门槛，作者将AG的解答提交给了美国IMO竞赛教练，教练在数学奥赛上进行评分非常有经验，而且还撰写了(authored)奥赛几何板块的训练书籍。

AG的解答被认为应该拿到满分，因此能够通过近些年的IMO金牌门槛。作者也注意到IMO测试也同样评估人类在其他三个数学领域的表现，除几何之外，还有符合很多专为人类设计的约束(under human-centric constraints)，例如不允许使用计算器、或者4.5小时的时间限制等等。

那么作者也研究了AG在4.5小时或1.5小时时间限制下的表现，在Extended Data Fig. 1中有详细的结果报告。

## Learning to predict the symbolic engine’s output improves the language model’s auxiliary construction

学着预测符号引擎的输出，可以帮助语言模型进行辅助构造

原则上来说，在证明搜索的过程中，辅助构造的策略必须依赖于与之工作的、具体的推理引擎的细节。但作者发现，一个没有预训练的语言模型只能解出21道题。这说明，在那些由符号引擎DD+AR所生成的纯推理的证明上，进行预训练可以改善辅助构造的成功率。

从另一个角度来说，一个没有预训练的语言模型，也会降低它的性能，但没有那么严重(severely)，这种情况下也能够解决23道题，而完整版的AlphaGeometry可以解决25道。

## Hard problems are reflected in AlphaGeometry proof length

题目的难度同样反映在AG的证明长度上

Fig. 6度量了已解决问题的难度，用的是人类IMO竞赛选手的公开得分(public scores)，在度量的同时，作者也把这些题以AG相关的证明长度来作图。结果显示，三道人类得分最低的题，AG也需要极其(exceptionally)长的证明序列，以及语言模型的(辅助)构造，来帮助AG找到最终的题解。

然而，对于更容易的题目(平均人类得分>3.5)，作者观察到了在人类平均得分以及AG的证明序列长度之间没有关联性(correlation)。(p=-0.06)

# Conclusion

在证明欧氏平面几何定理上，AlphaGeometry是第一个超越了人类IMO竞赛选手的平均水准的计算机程序(computer program)，也胜过了强大的计算机代数和搜索方法的基准线。

>Notably, we demonstrated through AlphaGeometry a neuro-symbolic approach for theorem proving by means of large-scale exploration from scratch, sidestepping the need for human-annotated proof examples and human-curated problem statements.

值得注意的是，作者证明了，通过AlphaGeometry，一个用于定理证明的神经-符号方法，通过从零开始大规模地探索，可以规避人类标注的证明例题，以及人工构造问题声明的需要。

>Our method to generate and train language models on purely synthetic data provides a general guiding framework for mathematical domains that are facing the same data-scarcity problem.

作者提出的，仅仅在合成数据上，生成和训练语言模型的方法，提供了一个更为通用的指导性框架(general guiding framework)，可以用于那些同样面临类似的、数据稀缺(data-scarcity)问题的领域。
