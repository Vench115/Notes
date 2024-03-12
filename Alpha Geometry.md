[Solving olympiad geometry without human demonstrations | Nature](https://www.nature.com/articles/s41586-023-06747-5)

# Abstract

证明奥林匹克等级的数学定理，不可否认地代表了在人类等级自动推理方面的显著里程碑，由于它们的难度在大学前，世界上最有天赋的学生中享誉盛名。最近的机器学习的技术路径，然而并不适用于大多数的数学领域，因为这种方法需要将人类的证明步骤转化为机器可验证的格式，而这个过程代价非常高。这个问题在几何领域甚至更加严重，因为几何问题特殊的转换要求所带来的挑战。该问题所带来的结果就是，训练数据的严重缺失。

>We propose AlphaGeometry, a theorem prover for Euclidean plane geometry that sidesteps the need for human demonstrations by synthesizing millions of theorems and proofs across different levels of complexity.

所以，DeepMind团队推出了AlphaGeometry，一个用于欧式平面几何的定理证明器(prover)，它能够规避提供人类证明演示的需求。方法简单来说，是合成成百万上千万条，来自于不同难度层级的定理和证明。

AlphaGeometry是一个神经-符号(neuro-symbolic)系统，使用一个神经语言模型，在作者提出的大规模合成数据上，从零开始训练。并在具有挑战性的题目中，那些无穷无尽的分支节点上引导一个符号推理引擎，进行解题。

在一个最近的30道奥赛级别的测试题目集中，AlphaGeometry解决了25道，超过了之前最好的，只能解决10道的方法，并且AG能够接近IMO金牌选手的平均表现水平。

值得注意的是，AlphaGeometry能够生成人类可读的证明，解决了IMO 2000和IMO 2015中的所有几何问题，并且能够接受人类专家的评估检验。同时，它也在2004年的IMO定理中，发现了一个更为普遍的版本。

# Main

## 1

(能够)证明定理，意味着完全驾驭(mastery)了逻辑推理的能力，以及在朝着目标的无限大的动作空间中进行搜索的能力。同时，这也象征着强大的解决问题的能力。

自从20世界50年代，追求更好的定理证明的能力，已经成为了AI研究领域一个持续的焦点。数学奥林匹克竞赛是世界上享誉盛名的定理证明竞赛，类似地可以追溯到1959年，在问题求解能力的判断上，扮演着重要的(instrumental)角色。能够在奥赛级别，匹配顶级人类选手的表现，已经成为了AI研究领域一个重要的里程碑。

## 2

定理证明对于基于学习的方法来说非常困难，因为在大多数数学领域上，能够将人类的证明翻译成机器可验证语言的训练数据及其稀少。不仅如此，几何相比于其他奥赛领域更为突出的原因，是因为它几乎没有已经用通用数学化语言（如Lean）形式化好的证明例子，这也是由于几何上，非常独特的形式化难度。

几何专用的语言，从另一个角度来说，是被非常狭义地定义的，而因此不能完全解释很多人类的证明，因为证明中使用了不局限于纯几何领域的工具，例如复数。

总的来说，这造成了数据上的瓶颈，导致几何领域在最近的使用人类演示的研究中落后(lag behind)。因此，近几年解决几何问题的方法，仍然主要依赖于符号的方法，以及人类设计的、硬编码的启发式搜索方法。

## 3

DeepMind团队提出了一种利用合成数据进行定理证明的方法，因而可以回避(sidestepping)翻译人类提供的证明例子。他们专注于欧式平面集合领域，并且排除了比如几何不等式以及组合几何的主题。

通过在一个随机定理前提(random theorem premises)下使用现有的符号引擎，团队提取了一亿条(100 million)合成的定理以及它们的证明，其中有许多超过200个证明步骤，四倍于奥赛定理的平均证明长度。

作者在综合证明生成上，更进一步地定义并且使用了依赖性差异(dependency difference)的概念，使得他们的方法能够生成接近一千万(10 million)综合证明步骤。这些步骤能够构造辅助点，超越纯符号推理的极限。

辅助构造(Auxiliary Construction)是外生项(exogenous term generation)生成的几何实例，代表了定理证明中无穷的分支因子(branching factor)，而且在别的数学领域也能被广泛地认为是证明许多困难定理的关键挑战。因此，DeepMind团队的工作展示了一种成功生成综合数据，并通过学习来解决这个关键挑战的成功范例。通过这种解决方法，作者提出了一种普遍的指导框架，并在方法部分(AlphaGeometry framework and applicability to other domains)讨论了其在其他领域的适用性。

## 4

作者在所有生成的综合数据上与训练了一个语言模型，并微调使其能够在证明搜索的过程中更关注于辅助构造，并将所有的推理证明步骤授予一个专门的符号引擎。这种方法沿袭了许多领域中广泛应用的方法，也就是例如GPT-f的语言模型，在人类的证明例子上训练后，能够生成外生的(exogenous)证明条件(terms)作为例如nlinarith或者ring这些又快又准的符号引擎的输入，从而充分利用两种方法的优势，达到两全其美的效果。

作者的几何定理求解器AlphaGeometry，如图1中所示，能够生成人类可读的证明，极大地超越了先前SOTA的几何定理证明机器程序，并且在一个由30道翻译自IMO的经典几何问题组成的测试集上，接近了一位IMO金牌选手的平均表现。

>fig. 1，作者提出的神经符号AlphaGeometry以及它如何解决一个简单的问题和IMO 2015的问题3的总览。
> 
>最顶上的一行展现了AlphaGeometry如何解决一个简单的问题。
>a. 一个简单的例子以及它的图表。
> 
>b. AlphaGeometry通过运行符号推理引擎初始化它的证明搜索。推理引擎不知疲倦地从定理前提中推理出新的语句(statements)，直到定理得证或者新的语句被耗尽。
> 
>c. 因为符号引擎不能找到一个证明，语言模型构造了一个辅助点，在符号引擎退出之前发展新的证明状态。这种循环一直持续，直到解法被找到。
> 
>d. 对于简单的例子来说，循环在第一个辅助构造“D为BC中点”后终止。证明包含两个其他步骤，这两个步骤都利用了中点的性质：”BD=DC“并且”B,D,C是共线的“，步骤用蓝色高亮指出。底下一行展现了AlphaGeometry是怎样解决IMO 2015 P3问题的。
> 
>e. IMO 2015 P3问题声明和图表
> 
>f. IMO 2015 P3由三个辅助点构造。在两种解法中，作者让语言模型输出交错于符号引擎的输出，来反映出他们的执行顺序。此处需要指出图f中IMO 2015 P3的证明为了便于解释，是被大量缩减和编辑过的。它的完整版本在辅助材料中。

# Synthetic theorems and proofs generation

## 1

作者提出的用于生成综合数据的方法在Fig. 3中展示。他们首先在一个定理前提上采样一个随机的集合，作为符号引擎的输入来生成其派生。一个完整的用于采样的动作列表能够在Extended Data Table 1中找到。

在作者的工作中，他们在一个高度并行的环境中，采样了接近一亿条类似的前提，这个过程在Methods部分有所描述。注意，作者并没有使用到任何现有的、带有人类设计问题集的定理前提，并且统一地、随机地采样了符合条件的构造。

## 2

其次，作者在采样的前提上使用一个符号推理引擎。这个引擎遵循前向推理的规则(如Fig. 3b所示)快速地推理出新的真命题(true statements)，这个操作会返回一个有向无环图，图中包含所有的可读结论。有向无环图中的每个顶点都是一个可读的结论，顶点的边连接到它的父节点，这种连接是由于一个回溯算法，在Methods中详细描述。

这种方法允许一个回溯过程从任意一个顶点N开始递归地运行，最后返回它的依赖性子图G(N)，该子图的根节点是N，叶子顶点则是一个已采样前提的子集。把这个子集记为P，作者就得到了一个合成训练例子：(premises, conclusion, proof) = (P, N, G(N))。

>fig. 3 AlphaGeometry 合成数据生成过程
> 
>a. 作者首先从一个随机的定理前提中采样一个巨大的集合。
> 
>b. 作者利用符号推理引擎来获得一个推理结论(deduction closure)，这能够返回一个命题的有向无环图。对于图中的每个顶点，作者再采用回溯来找到必要前提以及依赖(dependency deductions)的最小子集。例如，对于最右边的顶点”HA⊥BC“，回溯过程会返回绿色的子图。
> 
>c. 最小的前提以及相关的子图构成了一个综合的问题及其解答。在最下面的例子中，点E和D参与了证明，尽管他们与HA和BC的构造无关；因此，这些例子作为辅助构造被语言模型学习。

## 3

在几何问题中，符号推理引擎是一个推理的数据库，它的能力是从前提中，通过几何地规则有效地推理出新的命题。DD(Deductive database)遵循推理规则，以有限霍恩子句的形式。也就是$Q(x) \leftarrow P_1 (x) , \dots, P_k (x)$ ，其中，x是点对象，然而$P_1 (x) , \dots, P_k (x)$以及Q是谓词，例如”相等“以及”共线“。推理规则的完整列表在ref. 10中可以找到。

为了拓宽生成综合定理以及证明的范围，作者同样在符号引擎中引入了另一个组件，其能够通过代数规则(AR, algebraic rules)推理出新的命题，其在Methods中描述。

AR对于角度、比例、以及距离追踪(chasing)都非常重要，而这些方法在许多奥林匹克竞赛等级的证明中都需要用到。作者在Extended Data Table 2中囊括了具体的AR例子。DD以及AR的组合方法，包括了他们两者的前向推理以及回溯算法，在作者的工作中是一种新的贡献，而且代表了几何领域进行符号推理的SOTA方法。

# Generating proofs beyond symbolic deduction

到目前为止，生成的证明仅仅包括了那些已经可以被高效的符号推理引擎DD+AR达到的推理步骤。然而，为了解决奥赛级别的问题，最关键的缺失的部分是生成新的证明项(terms)。

在以上的算法中，那些证明项构成了独立于N(conclusion)的P(premises)的子集。换句话说，这些证明项是结论命题和结论对象之间的依赖差异(dependency difference)。

>We move this difference from P to the proof so that a generative model that learns to generate the proof can learn to construct them, as illustrated in Fig. 3c.

作者提出的方法将这种“差异”从P移动到证明，因而一个用于生成证明的生成模型可以学着去构造它们。

这种证明步骤可以进行辅助构造，而这是符号推理引擎所不被设计去做的。在普遍的定理证明情景中，辅助构造是外生证明项生成的一个实例，这对于所有证明-搜索算法都是一个显著的挑战，因为它在搜索树中引入了无穷的分支点。

>Previous methods to generate them are based on hand-crafted templates and domain-specific heuristics (8–12), and are, therefore, limited by a subset of human experiences expressible in hard-coded rules. Any neural solver trained on our synthetic data, on the other hand, learns to perform auxiliary constructions from scratch without human demonstrations.

在几何定理证明中，辅助构造自从1959年该领域的开端(inception)以来，就是一个存在已久的研究课题。先前生成证明的方法，都是基于手工标注的模板(templates)，以及该领域中专用的启发式搜索方法，都因此“受限于”人类的那些可以用硬编码的规则所解释的经验。

在作者的综合数据上进行训练的任何神经网络解题器(solver)，从另一个角度来说，都是学着去从零开始，不按照人类的指导，进行辅助构造。

>Fig. 3 AlphaGeometry合成数据生成的过程
>a. 作者首先在随机定理前提上采样出一个极大的集合
> 
>b. 作者使用符号推理引擎来获得一个推理结论。这个操作会返回一个命题的有向无环图。对于图中的每个顶点，作者对他们都会进行回溯，来找到顶点的必要前提以及以来推理的最小集合。例如图中所示的，“HA⊥BC”，回溯会返回绿色的子图。
> 
>c. 最小的前提以及相关的子图，构造出了一个综合的问题及其解答。在最下面的例子中，点E和D参与了证明，尽管他们对于HA和BC的构造是无关的。因此，他们能够被语言模型学习，作为辅助构造的方式。

# Training a language model on synthetic data

Transformer语言模型是一个强大的深度神经网络，能够学习且通过“下一个token预测”的方法来生成文本序列，助力大量的生成式AI技术方面的进步。

作者把(P,N,G(N))序列化成一个文本字符串，其结构是’\<premises\>\<conclusion\>\<proof\>'。通过在这样的符号序列上进行训练，一个语言模型能过高效地学习生成证明，条件就是定理前提以及结论。

# Combining language modelling and symbolic data

从更高的层面来说，证明搜索是一个循环，这个循环中语言模型和符号推理引擎交替运行(take turns to run)，如在Fig. 1 b,c中所示。证明搜索在定理结论被找到，或者循环达到最大迭代次数后停止。

语言模型用问题命题字符串进行初始化(seeded)，并在每一轮都生成一个额外的语句，以问题命题和之前的构造作为条件，描述一个新的辅助构造例如“构造点X，使得ABCX是一个平行四边形”。

每次语言模型生成一个这样的构造，作为新的输入给予符号引擎，因此，它的推理结论(closure)就会扩大，潜在地能够达到结论。作者使用集束搜索的方法来探索由语言模型生成的、最高k个构造。具体的并行证明搜索算法在Methods介绍。

# Empirical evaluation

一个奥赛级别用于几何的评价基准。

现存的奥林匹克数学的评价指标并没有完全覆盖几何领域，因为其更加关注于在更一般目标的语言中的形式化语言，而形式化过程在表示几何上增加了巨大的困难(pose great challenges)。

解决这些困难就需要深度的专家以及大量的研究投入，也超出了作者工作的范畴，因为那些研究更加关注于定理证明的方法论。由于这个原因，作者采用了自2000年以来的IMO竞赛中的几何问题，并将其放入一个更细分的、专业的环境，用于传统的用于交互式的图证明辅助，此处也在Methods中有所描述。

在所有的非组合的、几何相关的问题，75%能够被(形式化)表示，能够得到一个30道经典几何的问题。例如，几何不等式和组合几何，是不能被翻译的，因为他们的形式化相比于经典几何是有明显的不同的。

作者在Supplementary Information中包括了声明和翻译30道题的完整列表。最终的测试集命名为IMO-AG-30，补充材料中强调了它的来源、翻译的方法、以及它最近的大小。

# Geometry theorem prover baselines

## 1

几何定理求解器在传统的观点中一般分为两类。

第一类是计算机代数方法，他们将几何命题视为点坐标的多项式方程。证明是通过大型多项式的特殊变换来实现的。Grobner基和吴法是这类方法中比较有代表性的，因为其理论上成功的保证，能够决定IMO-AG-30中所有的真值，尽管不能生成人类可读的证明。

因为这些方法通常具有巨大的时间和存储复杂度，尤其是当处理IMO等级的问题时，作者认为，只要能够利用任意一个已有的实现，并且能够在48小时内解决的问题都能被视为成功，这些结果作者也有所汇报。

## 2

AlphaGeometry是属于第二类方法的求解器，通常被描述为搜索、或者公理的，有时是“综合的”方法。这些方法将定理证明的问题，视作一个逐步搜索的问题，运用的是一个几何的公理集合。拜此所赐，他们通常会返回高度可解释的证明，对于人类读者是非常易读的。

在这类方法中的基准方法中(baselines)普遍包括符号引擎，引擎中也通常有人类设计的启发式搜索方法。例如，Chou等人提供了18种启发式搜索方法，例如“如果OA⊥OB而且OA=OB，构造C在OA射线的反方向上，使得OC=OA”，除此之外还有75种用于符号引擎的推理规则。

大语言模型例如GPT-4也能够被视作是这一类的方法。大语言模型已经展现了其在各种各样的推理任务上，超凡的推理能力。

>When producing full natural-language proofs on IMO-AG-30, however, GPT-4 has a success rate of 0% , ......

当尝试在IMO-AG-30上生成所有自然语言的证明时，然而，GPT-4仍然只有0%的成功率，在整个输出的过程中它通常会犯语法或语义错误。这表明了模型本身对于几何知识，以及问题命题本身的了解甚少。

需要指出的是，GPT-4在IMO问题上的性能也会被它训练数据中的公共解答所污染。而一个性能更好的GPT-4也因此仍然不能与其他求解器所兼容。

总的来说，搜索方法在他们的证明性能上没有理论上的保证，并且也被广泛认为是弱于计算机代数方法。

## Synthetic data generation rediscovers known theorems and beyond

作者发现他们的综合数据生成能够重新发现(rediscover)一些相当复杂的(fairly complex)定理，包括一些几何学界所了解的辅助定理(lemmas)。如Fig. 4中所示，尽管是从随机采样的定理前提开始。

这可以归因于利用了符合的操作，如在Extended Data Table 1中所描述的，例如“作一个质心(centroid)”或者“作一个旁心(excentre)”，这偶然地采样了众所周知地定理前提地超集，在作者大规模的探索环境，如其在Methods上所描述的。

为了学习了解合成证明的复杂性，Fig. 4直观地展现了综合证明长度的直方图，并列展示了在奥赛问题的测试集上发现的证明长度。

>Although the synthetic proof lengths are skewed towards shorter proofs, a small number of them still have lengths up to 30% longer than the hardest problem in the IMO test set.

尽管综合证明长度已经朝着更短的证明被歪曲(skewed)了，他们之中少部分的证明长度，仍然比IMO测试集中最难的问题要长30%。

作者也发现，由这个过程所发现的综合定理，并没有受限于人类的“审美偏见(aesthetic biases)”，例如对称，因此也就能够覆盖更大的、欧氏几何中的情况集合。

>We performed deduplication as described in Methods, resulting in more than 100 millions unique theorems and proofs, and did not find any IMO-AG-30 theorems, showing that the space of possible geometry theorems is still much larger than our discovered set.

作者进行数据去重，结果产生了超过一亿条独立的定理和证明，而且作者所生成的定理并没有发现IMO-AG-30中的任何定理，所以他们认为可能的几何定理空间，比他们发现的集合还要大得多。crazy

## Language model pretraining and fine-tuning

作者首先在所有的一亿条综合生成的定理上训练了语言模型，包括那些纯符号推理的定理。然后，作者在一个证明的子集上进行微调，这些子集的证明需要辅助构造，并且大概占了总体训练数据的9%，也就是九百万条证明，为了更好地在整个证明搜索过程中更好地关注它被分配的任务。

# Proving results on IMO-AG-30

## 1

十个不同的解题器在IMO-AG-30基准上的性能评估，在Table 1中展示，其中八个，包括AlphaGeometry，是基于搜索的方法。

此外，用提示词让GPT-4以自然语言生成完整的证明，带有多轮的反思和修改，作者也结合了GPT-4和DD+AR作为另一个基准来增强它的推理准确度。为了达到这个目标，作者使用非常详细的指令以及少样本(few-shot)例子加入到提示词中，来帮助GPT-4成功地与DD+AR交互，只要能以正确的语法提供辅助构造。涉及GPT-4的基准的提示词细节包括在Supplementary Information中。

## 2

AlphaGeometry达到了最佳的结果，总共解决25道问题。先前最好的方法(吴法)解决了十题，然而最强的基准(DD+AR+human-designed heuristics)解决了18道题，因为运用了这篇工作中所开发的代数推理引擎，以及由Chou等设计的人类启发式搜索方法。

为了匹配AlphaGeometry的计算测试时间，这个最强的基准利用了250个并行的核心(workers)运行了1.5小时，每一个都尝试不同的、由人类设计的并行启发式辅助构造的集合，直到搜索成功或超时。

其他如吴法的基准，或者全角法(full-angle method)并没有被并行计算资源所影响，因为他们实现的是固定的、逐步的算法，直到终止。

## 3

当度量了由基本符号推理引擎(DD)所带来的效果提升，作者发现融合代数推理，能够多解决7道问题而来到14道(DD+AR)，然而语言模型的辅助构造惊人地增加了另外11道解决地问题，结果来到了总共25道。

>As reported in Extended Data Fig. 6, we find that, using only 20% of the training data, AlphaGeometry still achieves state-of-the-art results with 21 problems solved.

如Extended Data Fig. 6中所报告的，作者发现，仅仅使用20%的训练数据，AlphaGeometry仍然可以达到最好的结果，解题21道。

>Similarly, using less than 2% of the search budget (beam size of 8 versus 512) during test time, AlphaGeometry can still solve 21 problems.

同样地，在测试时间中仅仅使用 少于 2%的搜索成本(集束搜索8 vs 512)，AlphaGeometry仍然可以解决21道题。

在一个更大、更多样的、包含231道集合问题的测试集，其中覆盖了课本习题、地区奥赛、以及著名定理，作者发现Table 1中的基准仍然保持了同样的性能等级(rankings)。其中，AlphaGeometry解决了几乎所有的问题(98.7%)，而吴法解决了75%，同时DD+AR+human-designed heuristics解决了92.2%，如Extended Data Fig. 6b所示。

## 4

>Notably, AlphaGeometry solved both geometry problems of the same year in 2000 and 2015, a threshold widely considered difficult to the average human contestant at the IMO.

值得注意的是，AlphaGeometry解决了2000和2015同年中的两题，这被广泛认为是普通人类竞赛者在IMO中的分水岭。

更进一步的，AlphaGeometry的回溯过程找到了一个，在翻译过的(translated 形式化的)IMO 2004 P1中未使用过的前提(premise)，如Fig. 5中所示，因此模型靠自己发现了一个，已翻译的IMO定理。

作者在Supplementary Information中包含了AG对于所有IMO-AG-30中题目的解法，并且他们人工分析了一些比较有意思的(notable)AG解法以及失败题目，在Extended Data Figs. 2-5。

>Overall, we find that AlphaGeometry operates with a much lower-level toolkit for proving than humans do, limiting the coverage of the synthetic data, test-time performance and proof readability.

总的来说，作者发现AlphaGeometry是用相比人类更加”低级的工具包“来解题，受限于合成数据的覆盖范围、测试时间内的性能、以及可读性。

## Human expert evaluation of AlphaGeometry outputs

因为AG能够输出高度可解释的(interpretable)证明，作者使用一个简单的模板（template 脚本)，来自动地把AG的解答翻译成自然语言。为了获得专家对于AG解决了所有2000和2015问题上的评估，由于AG潜在地可能已经超越了IMO金牌的分水岭，作者将AG的解答提交给了美国IMO竞赛教练，教练在数学奥赛上进行评分非常有经验，而且还撰写了(authored)奥赛几何板块的训练书籍。

AG的解答被认为应该拿到满分，因此能够通过近些年的金牌门槛。作者也注意到IMO测试也同样评估人来在其他三个数学领域的表现，除了几何之外，还有在以人为本的约束下(under human-centric constraints)，例如不允许使用计算器、或者4.5小时的时间限制。

那么作者也研究了AG在4.5小时或1.5小时时间限制下的表现，在Extended Data Fig. 1中由详细的结果报告。

## Learning to predict the symbolic engine’s output improves the language model’s auxiliary construction

原则上来说，在证明搜索的过程中，辅助构造的策略必须依赖于伴随工作的、具体推理引擎的细节。作者发现，一个没有预训练的语言模型只能解21道题。这说明，在那些由符号引擎DD+AR所生成的纯推理的证明上，进行预训练可以改善辅助构造的成功率。

从另一个角度来说，一个没有预训练过的语言模型，也会降低它的性能但没有那么严重(severely)，也能够解决23道题，而完整版的AlphaGeometry可以解决25道。

## Hard problems are reflected in AlphaGeometry proof length

图6度量了已解决问题的难度，用的是人类IMO竞赛选手的公共得分(public scores)，在度量的同时，也把这些题以AG相关的证明长度来作图。结果表现了，三道人类得分最低的题，AG也需要极其(exceptionally)长的证明序列，以及语言模型的构造，来帮助AG找到最终的题解。

然而，对于更容易的题目(平均人类得分>3.5)，作者观察到了在人类平均得分以及AG的证明序列长度之间没有关联性(correlation)。(p=-0.06)

# Conclusion

在证明欧氏平面几何定理上，AlphaGeometry是第一个超越了人类IMO竞赛选手的平均水准的计算机程序(computer program)，也胜过了强大的计算机代数和搜索的基准线。

>Notably, we demonstrated through AlphaGeometry a neuro-symbolic approach for theorem proving by means of large-scale exploration from scratch, sidestepping the need for human-annotated proof examples and human-curated problem statements.

值得注意的是，作者证明了，通过AlphaGeometry，一个用于定理证明的神经-符号学派方法，通过大规模地从零开始大规模地探索，可以规避人类标注证明例子，以及人工构造问题声明的需要。

>Our method to generate and train language models on purely synthetic data provides a general guiding framework for mathematical domains that are facing the same data-scarcity problem.

作者提出的，仅仅在合成数据上，生成和训练语言模型的方法，提供了一个更为通用的指导性框架(general guiding framework)，可以用于那些同样面临类似的数据稀少(data-scarcity)问题的领域。

# Extended data table ...
