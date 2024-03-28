[GeoDRL: A Self-Learning Framework for Geometry Problem Solving using Reinforcement Learning in Deductive Reasoning - ACL Anthology](https://aclanthology.org/2023.findings-acl.850/)

# Abstract

在自动几何问题求解领域(GPS)，同时保证准确性和正确性是一个非常大的挑战，而且标注好的数据十分稀缺，阻碍了模型从例子中学习数学推理的方法。因此，团队提出了GeoDRL，一种自学习的几何问题求解框架，其能够将逻辑图推理和深度强化学习整合起来，把几何推理优化成一个马尔可夫决策过程(Markov Decision Process)。

GeoDRL将图神经网络部署在一个几何逻辑图(Geometry Logic Graph)上，并利用一个符号引擎更新状态。把DRL融入进演绎推理中，能够使得GeoDRL进行无监督的自学习，同时保证准确性。

GeoDRL通过无监督学习，在Geometry3K数据集上展现了更好的准确度，比之前SOTA的方法提升了11.1%，并且同时增强了效率和可解释性。

# Problem Statement and Preliminaries

问题定义和前提

## Problem Definition

- Primitive：一个基本的、可以组成图形的几何元素，例如点、线、圆、多边形等等
- Attribute：几何元素的属性(property)，通常可以解释成一个数值，例如线段的长度、角度、圆的半径等
- Predicate：几何元素之间的关系，例如点在线上、线段1垂直于线段2等等
- Interpretation：一个几何问题中所有元素、属性和谓词的完整描述
- Theorem：一个先验的几何知识，应用于推理过程中，来获得新的集合元素、属性或者关系

一个n步的推理序列：$\tau = \{ p_1, p_2, \dots, p_n \}$ 其中 $p_t$ 是推理序列中的第 $t$ 步，可以表达为：$p_t = I_{t-1} \rightarrow^{k_t} I_t$ ，其中 $k \in \mathcal{K B}$ 是几何定理集合 $\mathcal{KB}$ 中的一条定理，而 $I$ 是几何描述信息。

基于上述的定义，GeoDRL的工作流程如下所示：

1. 解析器实现一个函数 $Parse(\cdot)$ ，从问题图像D和文本T中，提取出初始的几何描述信息 $I_0$ 以及目标 $g$ ，公式表述为 $I_0, g = Parse(D,T)$
2. 推理器实现一个函数 $Reason(\cdot)$ 来生成一个推理序列 $\tau$ ，给定初始问题描述 $I_0$ 、目标 $g$ 以及结合定理集合 $\mathcal{KB}$ ，用公式表述为 $\tau = Reason(I_0, g, \mathcal{KB})$

一个仍然存在的问题是，从多种候选的序列 $C = \{\tau_1, \tau_2, \dots \}$ 中选择最佳的解答 $\tau^*$ 。作者倾向于选择最短的解答，并且如果有同样长度的多个解答的话，作者会选择那个时间更少的。用公式表达即为：$\tau^* = argmin_{\tau \in C} (|\tau|, Time(\tau))$ 。

## Reinforcement Learning Modeling

3.1中作者描述了几何问题求解，推理过程被建模成解决马尔可夫决策过程(Markov Decision Process)。对于集合推理的MDP来说，它包含所有潜在的几何描述状态以及它们之间的转移概率。

因此，作者采用了Q-Learning，一种DRL算法来基于状态的Q-Value，选择动作。在这种框架中，几何描述信息是状态空间 $\mathcal S = \{ s_i | i = 1,2,\dots \}$ ，几何定理几何是动作空间 $\mathcal A = \{ a_i | i = 1,2,\dots \}$ 。DRL agent是一个深度神经网络，对于在状态s中选择动作a产生值 $Q(s, a)$ 。

每一个时间间隔，环境都通过Q来进行一次动作，并更新到新的状态s'，而且会提供一个奖励r。算法的目标是让agent学习一种策略 $\pi : \mathcal S \rightarrow \mathcal A$ ，以最大化累计的奖励。

# Approach

## Parser

解析器的目标是从问题的图像和文本中，提取出几何元素和属性，并且在几何元素之间建立关系，把他们解析成描述(Interpretation)。对于几何信息的结构化建模，作者提出了几何逻辑图谱(Geometry Logic Graph, GLG)，作为作者框架中几何解释信息的表达。

GLG是一个异构的属性图谱(heterogenous attributed graph)，$G = (V,E,\Sigma, L)$，其中V是代表几何元素类型的顶点；E是边的集合，$e = (u,v)$ 表示顶点u到顶点v的边，也就是这两个几何元素间存在几何关系；$\Sigma$ 是属性的域；L是将属性分配给顶点的函数。

$l(v)$ 是v的属性。例如，如果v表示线段AB，那么l(v)就代表AB的长度。GLG是一个有向图，其中在不同几何元素之间的每条边 $e=(u,v)$ 都存在它自己的逆边 $e^{-1} = (v,u)$ 。更进一步的，为了在一张图中表达题目信息 $I$ 以及目标 $g$ ，作者将 $g$ 看作目标元素的一个特殊属性加入到GLG中。

## Reasoner

