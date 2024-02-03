[Solving olympiad geometry without human demonstrations | Nature](https://www.nature.com/articles/s41586-023-06747-5)

# Abstract

证明奥林匹克等级的数学定理，不可否认地代表了在人类等级自动推理方面的显著里程碑，由于它们的难度在大学前，世界上最有天赋的学生中享誉盛名。最近的机器学习的技术路径，然而并不适用于大多数的数学领域，因为这种方法需要将人类的证明步骤转化为机器可验证的格式，而这个过程代价非常高。这个问题在几何领域甚至更加严重，因为几何问题特殊的转换要求所带来的挑战。该问题所带来的结果就是，训练数据的严重缺失。

> We propose AlphaGeometry, a theorem prover for Euclidean plane geometry that sidesteps the need for human demonstrations by synthesizing millions of theorems and proofs across different levels of complexity.

所以，DeepMind团队推出了AlphaGeometry，一个用于欧式平面几何的定理证明器(prover)，它能够规避提供人类证明演示的需求。方法简单来说，是合成成百万上千万条，来自于不同难度层级的定理和证明。

AlphaGeometry是一个神经-符号(neuro-symbolic)系统，使用一个神经语言模型，在作者提出的大规模合成数据上，从零开始训练。并在具有挑战性的题目中，那些无穷无尽的分支节点上引导一个符号推理引擎，进行解题。

在一个最近的30道奥赛级别的测试题目集中，AlphaGeometry解决了25道，超过了之前最好的，只能解决10道的方法，并且AG能够接近IMO金牌选手的平均表现水平。

值得注意的是，AlphaGeometry能够生成人类可读的证明，解决了IMO 2000和IMO 2015中的所有几何问题，并且能够接受人类专家的评估检验。同时，它也在2004年的IMO定理中，发现了一个更为普遍的版本。

# Main

回避(sidestepping)翻译人类提供的证明例子。

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
