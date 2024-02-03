[Solving olympiad geometry without human demonstrations | Nature](https://www.nature.com/articles/s41586-023-06747-5)

# Abstract

证明奥林匹克等级的数学定理，不可否认地代表了在人类等级自动推理方面的显著里程碑，由于它们的难度在大学前，世界上最有天赋的学生中享誉盛名。最近的机器学习的技术路径，然而并不适用于大多数的数学领域，因为这种方法需要将人类的证明步骤转化为机器可验证的格式，而这个过程代价非常高。这个问题在几何领域甚至更加严重，因为几何问题特殊的转换要求所带来的挑战。该问题所带来的结果就是，训练数据的严重缺失。

> We propose AlphaGeometry, a theorem prover for Euclidean plane geometry that sidesteps the need for human demonstrations by synthesizing millions of theorems and proofs across different levels of complexity.

所以，DeepMind团队推出了AlphaGeometry，一个用于欧式平面几何的定理证明器(prover)，它能够规避提供人类证明演示的需求。方法简单来说，是合成成百万上千万条，来自于不同难度层级的定理和证明。

AlphaGeometry是一个神经-符号(neuro-symbolic)系统，使用一个神经语言模型，在作者提出的大规模合成数据上，从零开始训练。并在具有挑战性的题目中，那些无穷无尽的分支节点上引导一个符号推理引擎，进行解题。

在一个最近的30道奥赛级别的测试题目集中，AlphaGeometry解决了25道，超过了之前最好的，只能解决10道的方法，并且AG能够接近IMO金牌选手的平均表现水平。

值得注意的是，AlphaGeometry能够生成人类可读的证明，解决了IMO 2000和IMO 2015中的所有几何问题，并且能够接受人类专家的评估检验。同时，它也在2004年的IMO定理中，发现了一个更为普遍的版本。

# Main

(能够)证明定理，意味着完全驾驭(mastery)了逻辑推理的能力，以及在朝着目标的无限大的动作空间中进行搜索的能力。同时，这也象征着强大的解决问题的能力。

自从20世界50年代，追求更好的定理证明的能力，已经成为了AI研究领域一个持续的焦点。数学奥林匹克竞赛是世界上享誉盛名的定理证明竞赛，类似地可以追溯到1959年，在问题求解能力的判断上，扮演着重要的(instrumental)角色。能够在奥赛级别，匹配顶级人类选手的表现，已经成为了AI研究领域一个重要的里程碑。


……几何相比于其他奥赛领域更为突出，因为它几乎没有已经用通用数学化语言（如Lean）形式化好的证明例子，这也是由于几何上，非常独特的形式化难度。

几何专用的语言，从另一个角度来说，是被非常狭义地定义的，而因此不能完全解释很多人类的证明，因为证明中使用了不局限于纯几何领域的工具，例如复数。

总的来说，这造成了数据上的瓶颈，导致几何领域在最近的使用人类演示的研究中落后(lag behind)。因此，近几年解决几何问题的方法，仍然主要依赖于符号的方法，以及人类设计的、硬编码的启发式搜索方法。


DeepMind团队提出了一种利用合成数据进行定理证明的方法，因而可以回避(sidestepping)翻译人类提供的证明例子。他们专注于欧式平面集合领域，并且排除了比如几何不等式以及组合几何的主题。

通过在一个随机定理前提下使用现有的符号引擎，团队提取了一亿条(100 million)合成的定理以及它们的证明，其中有许多超过200个证明步骤，四倍于奥赛定理的平均证明长度。

作者在综合证明生成上，更进一步地定义并且使用了依赖性差异(dependency difference)的概念，使得他们的方法能够生成接近一千万(10 million)综合证明步骤。这些步骤能够构造辅助点，超越纯符号推理的极限。

辅助构造(Auxiliary Construction)是外生项生成的几何实例，

生成了综合定理以及证明，他们并没有用到任何，现有的带有人类设计问题集的定理前提，并且统一地、随机地采样了符合条件的构造。

## Synthetic data generation rediscovers known theorems and beyond

> We performed deduplication as described in Methods, resulting in more than 100 millions unique theorems and proofs, and did not find any IMO-AG-30 theorems, showing that the space of possible geometry theorems is still much larger than our discovered set.

他们生成的定理并没有发现IMO-AG-30中的任何定理，所以他们认为可能的几何定理空间，比他们发现的集合还要大得多。

## Generating proofs beyond symbolic deduction

> We move this difference from P to the proof so that a generative model that learns to generate the proof can learn to construct them, as illustrated in Fig. 3c.

……一个用于生成定理的生成模型可以学着去构造它们。

> Previous methods to generate them are based on hand-crafted templates and domain-specific heuristics8–12, and are, therefore, limited by a subset of human experiences expressible in hard-coded rules. Any neural solver trained on our synthetic data, on the other hand, learns to perform auxiliary constructions from scratch without human demonstrations.

之前的方法“受限于”一个人类经验的子集，这些是可解释的硬编码的规则。

然而他们的方法是从零开始，不按照人类的指导，进行辅助构造。

> As reported in Extended Data Fig. 6, we find that, using only 20% of the training data, AlphaGeometry still achieves state-of-the-art results with 21 problems solved.

仅仅使用20%的训练数据，就已经可以解题21道。

> Similarly, using less than 2% of the search budget (beam size of 8 versus 512) during test time, AlphaGeometry can still solve 21 problems.

仅仅使用2%的搜索成本，仍然可以解决21%的问题。

> Notably, we demonstrated through AlphaGeometry a neuro-symbolic approach for theorem proving by means of large-scale exploration from scratch, sidestepping the need for human-annotated proof examples and human-curated problem statements.

一个用于定理证明的神经-符号学派方法，通过大规模地从零开始探索，规避人类标注证明例子，规避人工构造问题的声明结构。

> Our method to generate and train language models on purely synthetic data provides a general guiding framework for mathematical domains that are facing the same data-scarcity problem.

他们的用于在纯综合数据上，生成和训练语言模型的方法，对于数学领域中所面对的同样的数据缺失问题，提供了一种通用的指导框架。

# Construct
