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
- Theorem：一个先验的几何知识，应用于推理过程中，来获得新的几何元素、属性或者关系

一个n步的推理序列：$\tau = \{ p_1, p_2, \dots, p_n \}$ 其中 $p_t$ 是推理序列中的第 $t$ 步，可以表达为：$p_t = I_{t-1} \rightarrow^{k_t} I_t$ ，其中 $k \in \mathcal{K B}$ 是几何定理集合 $\mathcal{KB}$ 中的一条定理，而 $I$ 是几何描述信息。

基于上述的定义，GeoDRL的工作流程如下所示：

1. 解析器实现一个函数 $Parse(\cdot)$ ，从问题图像D和文本T中，提取出初始的几何描述信息 $I_0$ 以及目标 $g$ ，公式表述为 $I_0, g = Parse(D,T)$
2. 推理器实现一个函数 $Reason(\cdot)$ 来生成一个推理序列 $\tau$ ，给定初始问题描述 $I_0$ 、目标 $g$ 以及结合定理集合 $\mathcal{KB}$ ，用公式表述为 $\tau = Reason(I_0, g, \mathcal{KB})$

一个仍然存在的问题是，从多种候选的序列 $C = \{\tau_1, \tau_2, \dots \}$ 中选择最佳的解答 $\tau^*$ 。作者倾向于选择最短的解答，并且如果有同样长度的多个解答的话，作者会选择那个时间更少的。用公式表达即为：$\tau^* = argmin_{\tau \in C} (|\tau|, Time(\tau))$ 。

## Reinforcement Learning Modeling

3.1中作者描述了几何问题求解，推理过程被建模成解决马尔可夫决策过程(Markov Decision Process)。对于几何推理的MDP来说，它包含所有潜在的几何描述状态以及它们之间的转移概率。

因此，作者采用了Q-Learning，一种DRL算法来基于状态的Q-Value，选择动作。在这种框架中，几何描述信息是状态空间 $\mathcal S = \{ s_i | i = 1,2,\dots \}$ ，几何定理几何是动作空间 $\mathcal A = \{ a_i | i = 1,2,\dots \}$ 。DRL agent是一个深度神经网络，对于在状态s中选择动作a产生值 $Q(s, a)$ 。

每一个时间间隔，环境都通过Q来进行一次动作，并更新到新的状态s'，而且会提供一个奖励r。算法的目标是让agent学习一种策略 $\pi : \mathcal S \rightarrow \mathcal A$ ，以最大化累计的奖励。

# Approach

## Parser

解析器的目标是从问题的图像和文本中，提取出几何元素和属性，并且在几何元素之间建立关系，把他们解析成描述(Interpretation)。对于几何信息的结构化建模，作者提出了几何逻辑图谱(Geometry Logic Graph, GLG)，作为作者框架中几何解释信息的表达。

GLG是一个异构的属性图谱(heterogenous attributed graph)，$G = (V,E,\Sigma, L)$，其中V是代表几何元素类型的顶点；E是边的集合，$e = (u,v)$ 表示顶点u到顶点v的边，也就是这两个几何元素间存在几何关系；$\Sigma$ 是属性的域；L是将属性分配给顶点的函数。

$l(v)$ 是v的属性。例如，如果v表示线段AB，那么l(v)就代表AB的长度。GLG是一个有向图，其中在不同几何元素之间的每条边 $e=(u,v)$ 都存在它自己的逆边 $e^{-1} = (v,u)$ 。更进一步的，为了在一张图中表达题目信息 $I$ 以及目标 $g$ ，作者将 $g$ 看作目标元素(targeted primitives)的一个特殊属性加入到GLG中。Figure 3 展示了一个GLG的例子。

此处作者讨论了GLG对比之前工作中，符号逻辑表达式(symbolic logical expressions)的优势。首先，GLG结构化成图，并且将几何解释信息融入进它的顶点和边中，这就避免了冗余的符号语法(symbolic grammar)。

其次，在符号逻辑表示中，一个几何元素是通过它的标记(notation)而定义的，造成的结果是神经网络学习到的特征会专注于它的标记。这是违反直觉的(counter-intuitive)，因为把一个点的标记从A改成X不会很大程度地影响问题定义。在GLG中，一个元素地特征仅仅通过它的类型、属性以及它与其他元素的关系定义，而不是它本身的标记。

特别地，作者将图像和文本分别解析了。对于图像，作者使用了PGDPNet作为图像解析器。PGDPNet是一个用于从几何图像中、提取元素和谓词的、端到端的模型，在平面几何图像解析上达到了SOTA的表现。PGDPNet把图像作为输入，生成点、线、圆的实例，以及代表元素间关系的逻辑表达式(logical expressions)。

对于文本，作者借用了Inter-GPS中的文本解析器。语义解析器把文本解析成符号逻辑表达式，通常包含目标。作者把上述两个分支中得到的逻辑表达式结合起来，并将它们结构化成一个GLG，包含一个初始的解释信息 $I_0$ 以及目标 $g$ 。

## Reasoner

给定一个初始的解释信息 $I_0$ ，一个求解目标 $g$ ，以及一个几何定理集合 $\mathcal{KB}$ ，推理器的目标是生成一个推理序列 $\tau$ ，其中 $I_t$ 满足目标 $g$ 。作者将这个过程建模成马尔可夫决策过程，并采用了DRL来解决MDP。

作者实现了一个环境，也就是一个符号几何系统(symbolic geometric system)。这个系统解析几何的表示信息和目标，并采取行动，更新状态信息并给出奖励。作者设计了一个深度强化学习agent来实现DQN算法，利用GNN来近似其中的q-value函数。DRL agent通过Q-value来选择当前状态的行动，并且跟环境交互(interact)来获得奖励以及下一个状态。

### Environment

上述提到的环境被描绘成一个符号几何系统。系统解析(resolve) [[#Parser]] 中提到的GLG，并存储几何描述信息以及目标。它应用一个几何定理，更新描述信息，而且通过agent各处的动作指导来生成一个奖励。在一个更新过程 $s \rightarrow s'$ 中，作者的奖励函数设置如下所示：

$$
Reward =
\left\{\begin{array}
 Solve(s',g) - \alpha e^{- \frac{t}{\sigma}} & s \ne s'\\
 -1.0 & s = s'
\end{array}\right.
$$

$$
Solve(s,g) =
\left\{\begin{array}{l}
 1.0  & s \vdash g\\
 0.0  & otherwise
\end{array}\right.
$$

其中t代表了过程中已经花费的时间。该系统仅当下一个状态 $s'$ 满足目标 $g$ 时才给出一个正奖励1.0，也就是问题解决的时候。作者加入了一个时间惩罚因子 $- \alpha e^{- \frac{t}{\sigma}}$ 来激励agent选择更有效率的动作。如果状态没有通过应用定理来改变，奖励被设置成-1.0来避免这个动作。

总的来说，奖励函数鼓励agent选择更短、更快的路径，且带有更少的无效动作。

### Action Space

动作空间被定义成一个几何定理集合 $\mathcal{KB}$ ，其中每个定理 $k$ 都被定义成一个条件声明语句 $p \Rightarrow q$ ，带有一个前提p和一个结论q。在t时刻，当在题目演绎(interpretation) $I_{t-1}$ 上应用定理 $k$ 时，如果 $I_{t-1}$ 满足了 $k$ 的前提 $p$ ，那么 $I_{t-1}$ 就根据结论 $q$ 进行更新：

$$
I_{t-1} \vdash p \Rightarrow I_t \leftarrow I_{t-1} \wedge q
$$

如果 $I_{t-1}$ 没有满足前提 $p$ ，那么它就不会被更新。更进一步，甚至如果 $p$ 被满足了，$I_t$ 也有可能保持不变。这通常当一个定理接连地被重复使用时发生。作者会把这两种情况称为无效的定理，也就会导致奖励 $r = -1.0$ 。

特别地，作者在这个工作中定义了24条定理，覆盖了例如角、圆、三角、以及多边形等多个类。此外，作者也把辅助线(auxiliary-line)定义为一个类，来解决一些困难的几何问题。

### GNN Architecture

作者使用了一个GNN来学习GLG的图表示信息，以评估状态的Q值。

>This is accomplished through the implementation of a Heterogeneous Attributed Graph Transformer built upon a transformer-based architecture.

这是通过实现一个异构的属性图Transformer来实现的，这建立在一个基于transformer的架构上。

给定一个GLG：$G = (V,E,\Sigma,L)$ ，模型的输入是一连串的顶点，并带有一个特殊的token：$[GRAPH]$ 作为起始。$[GRAPH]$ 聚合了顶点的特征，来捕捉全局的图表示信息。每一个顶点都包含一个顶点类型 $v_i$ 以及顶点的属性 $l(v_i)$ ，这些都被投影进顶点的嵌入(embedding) $h_i$ 中：

$$
h_i = E_{type} (v_i) + E_{attr} (l(v_i))
$$

顶点类型是几何元素例如点、线、圆的类型。顶i的那的属性通常被解释成一个数或者带有变量的数学表达式。实际上，记录下每个属性的具体值是没有多大意义的(make little sense)。例如，AB=3.2cm, BC=3.5cm。在这个例子中，我们只需要知道AB和BC的长度是两个不相等的数值即可。因此，作者使用了几个替换词来替换掉不经常出现的属性值。

模型会将GLG的结构信息编码，方式是改进transformer中的自注意力机制。边是通过增加偏置项矩阵 $B$ 来融入自注意力的，用公式描述如下所示：

$$
Attention(Q,K,V) = softmax(\frac {QK^T} {\sqrt d} + B) V
$$

$$
B_{i,j} =
\left \{\begin{array}{l}
 E_{edge} (e_{i,j}) & (v_i, v_j) \in E \\
 0 & i=j \\
 -\infty & otherwise
\end{array} \right.
$$

当顶点 $v_i$ 到 $v_j$ 存在一条边 $e_{i,j}$ 时，边 $e_{i,j}$ 的嵌入就被加入到attention分数的 $(i,j)$ 位置，作为一个偏置项。否则，作者就通过把分数设为 $-\infty$ 来封闭attention通道。全局图 token：$[GRAPH]$ 会注意到每一个顶点，来聚合所有的特征作为全局的图特征。

综上，作者实现了一个单跳(one-hop)的图注意力网络。最终，图特征向量被送入一个FFN，其输出的维度是动作空间的大小 $|\mathcal A|$ ，以生成Q值。

### Agent Operation

DRL agent通过与环境互动来运行，就比如符号系统。<算法图>

首先，系统会将从 [[#Parser]] 中获得的一个带有GLG的推演信息初始化为初始状态 $s_0$ 。然后它会进入一个 `while` 循环，其中每一步 $t$ ，GNN都会把 $s_{t-1}$ 的GLG作为输入，并对每个动作 $a_i$ 计算 $Q(s_{t-1} ,a_i)$ 。

DRL agent采用 $\epsilon-greedy$ 的探索策略来选择动作 $a_t$ 。符号系统执行动作 $a_t$ ，更新状态 $s_t$ ，并返回一个奖励 $r_t$ 。与此同时，系统会检查是否 $s_t \vdash g$ ，如果满足条件，就会发出一个标记 $Solved$ ，并跳出循环。

数据采样 $\{ (s_{t-1}, a_t, s_t, r_t) | t=1,2,...\}$ 是累计的，并且存储在一个经验记忆器当中(experience memory)。在它包含了一定量的样本之后，我们会每次从中随机采样一个批量，来训练Q网络，用贝尔曼方程(Bellman-Equation)：

$$
Q(s,a) = max_{a'} (\gamma Q(s',a') + r(s,a))
$$

其中训练目标是最小化 $Q(s,a)$ 的 $SmoothL1 \ loss$ 并且最大化 $max_{a'} (\gamma Q(s',a') + r(s,a))$ 。在推理阶段(inference stage)，DRL agent选择一个动作 $a$ ，它能够最大化现在状态 $s$ 的 $Q(s,a)$ 。符号系统执行这个动作并更新状态，直到问题解决。最后，我们收集每一步的 $p_t$ 一并得到了推理序列 $\tau$ 。

考虑到解答的可解释性，$\tau$ 需要被进一步处理来生成人类可读的解答。同时在推理中也有可能有冗余的更新部分。例如，在Figure 1的步骤2中有几对相似的三角形，但是只有 $\bigtriangleup ACD \sim \bigtriangleup CBD$ 能够导向目标。为了解决这个问题，作者记录了 $I_n \cap g$ 并且回溯导致此问题的更新步骤。终于，我们可以得到最后的解答。

# Experiments

在这一部分，作者在公开几何问题数据集上进行了公开实验，并比较了GeoDRL与先前方法的性能。进一步地，作者通过消融研究来分析不同部分的贡献，而且还讨论了它们模型在典型案例下的优越性和限制。

## Training Method

为了通过避免大量的初步探索，来加速训练的过程，作者采用了模仿学习(Imitation Learning)的方法来使得作者的agent学习一个初始化策略。具体来说，作者运用一篇文献中提到的搜索方法，在一些训练数据样本(500道题)上，搜索推理步骤，并在这些数据上训练一个teacher模型。

作者然后用teacher模型来与环境交互，并生成例子给到算法中的Experience Memory。然后，一个随机初始化的student模型，在这些例子上预训练2000步，来从teacher模型的经验中学习一个初始化策略。此后，作者继续在训练集上训练student模型作为一个DRL agent。

## Inference

在推理阶段，作者采用了集束搜索算法来选择最终的解答 $\tau^*$ 。候选序列 $\tau$ 的得分由如下的公式进行计算：

$$
p(s_t | s_{t-1}, a_t) = \frac {e^{Q(s_{t-1}, a_t)}} {\Sigma_{a' \in A} e^{Q(s_{t-1}, a')}}
$$

$$
score(\tau) = \Sigma_t log(p(s_t | s_{t-1}, a_t))
$$

其中，$p(s_t | s_{t-1}, a_t)$ 是状态 $s_{t-1}$ 到状态 $s_t$ 的转移概率，采用的动作是 $a_t$ ，具体值通过在Q-value上运用softmax函数计算得到。通过k束搜索，作者最终获得k个候选序列 $\{ \tau_1, \tau_2, \dots, \tau_k \}$ 带有最高k的得分，并选择最佳的 $\tau^*$ 作为结果。在这个研究中，作者用默认的5束来部署GeoDRL。

## Evaluation
