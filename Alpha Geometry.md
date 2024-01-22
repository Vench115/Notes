[Solving olympiad geometry without human demonstrations | Nature](https://www.nature.com/articles/s41586-023-06747-5)

# Abstract

证明奥林匹克等级的数学定理，不可否认地代表了在人类等级自动推理方面的显著里程碑，由于它们的难度在大学前，世界上最有天赋的学生中享誉盛名。

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