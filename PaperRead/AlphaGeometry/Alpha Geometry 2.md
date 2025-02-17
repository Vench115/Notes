[[2502.03544] Gold-medalist Performance in Solving Olympiad Geometry with AlphaGeometry2](https://arxiv.org/abs/2502.03544)

[AlphaGeometry2: Deepmind AI outperforms math Olympians at geometry tasks](https://the-decoder.com/alphageometry2-deepmind-ai-outperforms-math-olympians-at-geometry-tasks/)

# Abstract

作者团队推出了Alpha Geometry 2，一个由[[Alpha Geometry]]提出的Alpha Geometry的极大改进版本，目前，该版本已经再求解奥林匹克级别的几何问题中，超越了平均金牌选手的水平。为了达到该目标，坐着首先扩展了原本的AG语言，来求解(tackle)更难的问题，包括目标的运动，以及包括角度、比例、和距离的线性方程组问题。这一步，以及其他内容的增加，极大地改进了AG语言再IMO 2000-2024几何问题上的覆盖率，从66%提升到了88%。

AG2的搜索过程也有了很大的改进，通过使用Gemini的架构，来达到更好的语言建模(language modeling)，以及一个全新的知识共享(knowledge-sharing)机制，集合了多重搜索树。

除了进一步加强符号引擎和综合数据生成，作者也大力地加速了AG2对过去25年里所有集合问题的整体求解率，从现前的54%提升到了84%。

AG2也是在IMO 2024中达到银牌等级的系统的一部分。最后，同样重要的是，作者团队报告了整个过程，针对使用AG2作为一个完全自动化系统的一部分，来直接从自然语言输入中可靠地(reliably)求解几何问题。

# Introduction

IMO对于全世界的高中学生来说，都是一个享誉盛名的数学竞赛。IMO问题以它们的高难度而被人熟知，而且求解这些问题需要对于数学概念的深度理解，以及创造性地应用它们的能力。几何，四大IMO问题类别之一，是几类问题中形式最统一的一个，因此也最容易理解(approachable)。这类问题也非常适合基本的推理研究。

自动化求解几何问题，主要有两种技术路径。一个，是用代数方法“痛击”(bashing)问题，利用比如“吴法”；面积法；或者Grobner基方法。第二类，
