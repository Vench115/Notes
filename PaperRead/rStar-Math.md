[[2501.04519] rStar-Math: Small LLMs Can Master Math Reasoning with Self-Evolved Deep Thinking](https://arxiv.org/abs/2501.04519)

# Abstract

作者提出了rStar-Math来证明：小语言模型(Small Language Models)能够匹敌(rival)甚至超越(surpass) OpenAI o1的推理能力，且不需要从更高级的模型中蒸馏知识(distillation)。

rStar-Math通过蒙特卡洛树搜索(MCTS)训练(exercising)“深度思考”，来达到这种效果。其中，一个数学策略网络，也就是小语言模型(SLM)会进行长时间的(test-time)搜索，该过程由一个基于SLM的过程奖励模型(PPM)进行引导。

rStar-Math引入了三个创新，来解决训练这两个SLM时遇到的挑战：

1. 一种全新的，代码增强的，思维链(CoT)数据合成方法，该方法执行大量的MCTS尝试(模拟 rollouts)来生成逐步验证的推理轨迹(step-by-step verified reasoning trajectories)，用于训练策略网络SLM；
2. 一种全新的过程奖励模型训练方法，能够避免朴素的(naive)步骤级别的得分标注(annotation)，从而产生一种更加有效的，过程偏好模型(Process Preference Model)；
3. 一种自我进化的诀窍(self-evolution recipe)，其中策略网络SLM和PPM都从零开始构建，并且不断地迭代进化，以改善推理能力。

在747k道数学问题的，成百万上千万的合成解答上，通过四轮自我进化，rStar-Math增强了SLM的数学推理能力，到了SOTA的水准。在MATH的基准测试上，它改善了：Qwen2.5-Math-7B从58.8%到90.0%，Phi3-mini-3.8B从41.4%到86.4%，超越了o1-preview4.5%和0.9%。

在USA Math Olympiad(AIME)上，rStar-Math解决了平均53.3%的问题，排在最顶级高中数学学生中的top20%。代码和数据会在[github/MicroSoft/rStar](https://github.com/microsoft/rStar)中开源。

Table 1：rStar-Math赋予了（模型）前沿的(frontier)数学推理能力，通过在64条路径上进行深度思考。

# 1 Introduction

## 1.1 Background

最近的研究已经证明大语言模型(LLMs)能够解决数学问题。然而，让LLMs在一次推理中生成完整解答的传统方法，近似于System 1 thinking，这常常产生快速但是容易出错的结果。

作为回应(In response)，长时间的(test-time)计算尺度(scaling)暗示了一种朝向System 2 thinking的范式转移(paradigm shift)，这通过一个更慢的、更深的思考过程，模仿了(emulate)人类的推理。

在这个范式中，一个LLM会被作为一个策略模型来生成多个数学推理步骤，然后让另一个充当奖励模型的LLM来进行评估。那些被认为更有可能正确的步骤和解答被最终选中。这个过程不断迭代地重复，并最终得出最终结果。

## 1.2 Data

在长时间的计算范式中，关键是训练一个强大的策略模型，来生成潜在正确的(promising)解答步骤，以及一个奖励模型来准确地评估它们，而这两个模型都依赖于高质量的训练数据。不幸的是，现成的(off-the-shelf)高质量数学推理数据，众所周知是非常稀少的(scarce)，而且合成高质量的数学数据会面对根本的挑战。

对于策略模型，区分正确和错误的推理步骤是非常有挑战性的，这也导致去除低质量的数据非常复杂(complicating)。值得注意的是，在数据推理中，一个正确的最终答案，并不保证整个推理路径(trace)的正确性。不正确的中间步骤极大地(significantly)降低了数据质量。

而对于奖励模型，过程奖励建模(Process Reward modeling)展现出巨大的潜力，通过在中间过程中提供细粒度的(fine-grained)反馈。然而，准确的逐步反馈需要极多的人类标注工作，而且按比例增加这种工作(scale)是非常不现实的，在这一点上(in this regard)，训练数据就更加稀少了。而那些全自动的标注尝试，由于嘈杂的(noisy)奖励得分，仅展现出有限的收获。

由于上述的挑战，现存的用于训练模型的，基于知识蒸馏的数据合成方法，例如增大GPT4-distilled CoT数据，已经展现出边际效应递减(diminishing returns)，而且也不能够超越他们的指导模型(teacher model)的能力；与此同时，从今天起，训练可靠的过程奖励模型PRMs，用于数学推理，依然是一个尚未解决的问题(open question)。

## 1.3 Writers' work

在这篇工作中，作者提出了rStar-Math，一个能够自我进化的System 2风格的推理方法，能够达到SOTA的数学推理效果。在极具挑战性的数学竞赛基准测试上，(它能)挑战，甚至有时超越OpenAI o1模型，而且仅仅是一个用了70亿参数大小的模型。不像那些依赖于更高阶LLMs的用于数据合成的解决方法，rStar-Math充分利用(leverages)更小的模型(SLMs)，利用蒙特卡洛树搜索(MCTS)来建立一个可自我进化的流程，迭代地去生成更高质量的训练数据。为了实现自我进化，rStar-Math引入了三个关键创新：

### 1.3.1 代码增强的CoT数据合成方法

首先，一种全新的代码增强的CoT数据合成方法，进行巨量的(extensive)MCTS尝试，来生成逐步验证过的推理路径，且利用自标注的(self-annotated) MCTS Q-values。

特别地，数学问题求解在MCTS中被分解成多步的生成。在每一步上，充当策略模型的SLM，采样候选节点，每个节点都生成一个单步的(one-step)CoT，以及与之相关的Python代码。为了验证生成的质量，只有包含成功执行的Python代码片段的节点被保留，因此能够在中间步骤中减轻错误(mitigating errors)。

此外，巨量的MCTS尝试(模拟)会基于步骤的贡献，给每一个中间步骤分配一个Q值：贡献给更多导向正确答案轨迹的步骤被赋予更高的Q值，而且会被认为具有更高的质量。这就保证了由SLMs生成的推理路径，能够包含正确的、高质量的中间步骤。

### 1.3.2 过程偏好模型PPM

第二，是一种的全新方法，通过训练SLM来充当过程偏好模型PPM，以实现所需的过程奖励模型PRM，从而可靠地对每一个数学推理步骤预测一个奖励标签。PPM的核心在于：尽管通过巨量的MCTS模拟生成的Q值，仍然不足以精确量化每一步的得分，但是这些Q值能够可靠地区分正例(correct)步骤和负例(irrelavant/incorrect)步骤。

因此，该训练方法基于Q值对每个步骤都构建了偏好对(preference pairs)，并利用成对排序损失函数(ranking loss)来优化PPM对每一个推理步骤的得分预测，最终实现可靠的标注。这种方法避免了传统方法中，直接使用Q值作为奖励标签的设计，所固有的缺陷，也就是在逐步奖励分配中存在噪声，且精度不足。

### 1.3.3 自进化方法

最终，一个四轮次的自我进化诀窍(recipe)，能够从零开始，逐渐(progressively)构建出一个前沿的策略模型以及一个过程偏好模型PPM。作者从公共开源数据集上，构建的一个747k大小的数学文字问题开始。在每一轮中，作者使用最新的策略模型以及PPM来进行MCTS，不断生成(increasingly)高质量的训练数据，利用上述的两个模型，来为下一轮训练一个更强的策略模型和PPM。

每一轮达到的逐步细化效果是：

1. 一个更强的策略SLM；
2. 一个更可靠的PPM；
3. 通过PPM增强的MCTS来生成更好的推理路径；
4. 改善训练数据的覆盖范围，来解决更具挑战性的，甚至是竞赛级别的数学问题。

通过四个SLM的巨量实验，以及七个数学推理问题，展现了rStar-Math的有效性。出乎意料地，rStar-Math改善了全部四个SLM，在具有挑战性的数学测试基准上，媲美甚至超越OpenAI o1模型。

在MATH基准测试上，设置8条搜索路径下，rStar-Math将Qwen2.5-Math-7B从58.8%改进到89.4%、Qwen2.5-Math-1.5B从51.2%提升到87.8%。在64条搜索路径下，得分则会提升至90%和88.4%，超越了o1-preview模型4.5%和2.6%，而且媲美o1-mini的90%。在奥赛级别的AIME 2024中，rStar-Math解决了平均53.3%(8/15)的问题，超过了o1-preview 8.7%以及所有其他的开源LLM。

作者在SOTA的数据生成基准(baselines)上，进一步进行了综合实验，来验证逐步已验证的推理路径的优越性(superiority)，以及PPM与结果奖励模型、基于Q值PRM模型对比之下的有效性。

最终，作者从rStar-Math深度思考中提出了关键的发现，包括内在的自我反馈(self-reflection)能力，以及PPM对于定理应用的中间步骤的偏好。

# 2 Related Works

## Math Data Synthesis

LLM在数学推理上的研究进展，很大程度上依赖于构建(curating)高质量的CoT数据，尤其是在很多前沿方法都被GPT蒸馏的情况下，使用前沿的比如GPT-4的模型进行数据合成。主要工作包括NuminaMath和MetaMath。尽管有效，这仍然限制了模型的推理能力，上限只能达到它的指导模型。连指导模型都无法求解的困难问题，已经从训练集中剔除。甚至可求解的问题，也可能包含容易出错的中间步骤，而这些步骤却很难被察觉。

尽管拒绝采样(rejection sampling)方法能够改善数据质量，他们却不能保证正确的中间步骤。因此，扩大CoT数据的规模，只能获得递减的收益，而这些收益也趋近于饱和(with gains nearing saturation)——例如：OpenMathInstruct-2 尽管使用了增大八倍的数据集大小，却仅仅在MATH数据集上获得了3.9%的提升。

## Scaling Test-time Compute

增加长时间计算已经引入了新的Scaling Laws，使得LLMs能够通过生成多个样本，并利用奖励模型来选择最佳解答，以提升模型的整体性能。多样的长时间搜索方法已经被提出，包括随机采样以及基于树搜索的方法，比如MCTS。然而，针对增多长程计算的开源方法，在数学推理上，已经展现出了有限的收益，这常常是由于策略LLM或者奖励模型的限制。rStar-Math通过不断地进化策略LLM和奖励模型，来解决这个问题，最终达到类似于OpenAI o1的System 2 数学推理性能。

## Reward Models

奖励模型对于有效的System 2推理是至关重要的，但是却也极难获得。最近的工作，包括LLM-as-a-Judge用于验证；以及专门的奖励模型，比如Outcome Reward Model以及Process Reward Model (PRM)。仅PRMs能够提供非常好的、密集的、步骤级别的奖励信号用于推理，但是收集步骤级别的标注仍然是一个障碍。

Kang 2024, Wang 2024a等工作依赖于昂贵的人工标注数据集，比如PRM800k，而最近的方法则探索了通过蒙特卡洛采样或MCTS的自动化标注。然而，他们非常难以生成准确的奖励分数，而这会限制性能的收益。rStar-Math引入了一种全新的过程偏好奖励(PPM)，该模型完全消除了精确的、步骤级别的、奖励分数的标注需要。

# 3 Methodology

## 3.1 Design Choices

### MCTS for Effective System 2 Reasoning

作者的目的是训练一个数学策略SLM，以及一个过程奖励模型(PRM)，并将两者整合到蒙特卡洛树搜索 MCTS 中，以实现 System 2 的深度思考。MCTS被选中，是由于两个关键原因：

- 首先，它将复杂的数学问题分解成更简单的单步生成任务，降低了策略SLM的难度，相比于其他 System 2 方法比如 Best-of-N 或 self-consistency，这些方法都需要在一次推理的情况下，生成完整的解答。
- 其次，在 MCTS 中逐步的生成，可以自然地产生步骤级的完整解答，且通过一次推理就可以得到。标准的 MCTS 模拟过程(rollout)会基于每一步对最终正确答案的贡献度分配Q值，从而消除在过程奖励模型训练中，需要人工生成步骤级标注的需求。

理想情况下，增强的LLM如GPT-4可以被整合进MCTS中，以生成训练数据。然而，该方法会面临两个关键挑战：

- 首先，强如这些模型也难以稳定持续地求解复杂问题，比如奥赛级别的数学问题。因此，生成的训练数据将主要由更加简单的、可以解决的问题组成，从而限制了数据的多样性和质量。
- 其次，标注逐步的Q值需要海量的MCTS模拟；不够充分的树探索，会导致错误的(spurious)Q值分配，比如高估次优的步骤。考虑到每一次模拟都要包括多次的、单步的生成，而且这些模型的计算成本非常高，如果增多模拟次数，也会极大地提升推理开销。

### Overview

为此，作者探索了使用两个7B的轻量级语言模型(SLM，一个策略SLM和一个过程奖励模型PRM)，来生成更高质量的训练数据。由于模型的规模较小，可以在现有的硬件，比如4x40GB A100 GPU上进行海量的MCTS模拟。然而，自生成数据对于轻量级语言模型提出了更大的挑战，因为它们的性能较弱。这些模型经常无法生成正确的答案，即使最终答案正确，中间步骤也常常存在错误，或者质量较低。此外，与GPT-4等高级模型相比，轻量级语言模型能够解决的复杂问题更少。

这部分介绍了作者的方法论，如Fig. 1所示。为了减缓中间步骤出错和低质量的情况，作者引入了一个代码增强的思维链综合方法(code-augmented CoT)，该方法进行海量的MCTS模拟，来生成逐步验证的推理轨迹(trajectories)，并用Q值进行标记。

为了进一步改进SLM在具有挑战性问题上的性能，作者引入了一个四轮的(four-round)自进化方法(recipe)。在每一轮中，策略SLM和奖励模型都会被更新成更强的版本，逐渐地解决更多更难的问题，并生成高质量的训练数据。最终，作者提出了一个全新的过程奖励模型训练方法，能够消除对精确的逐步奖励标注的需求，最终产生更加有效的过程偏好模型(PPM)。

## 3.2 Step-by-Step Verified Reasoning Trajectory

首先，作者介绍的是一种生成逐步验证的推理轨迹，并为每一步标注Q值的方法。给出一个问题 $x$ 以及一个策略模型 $M$，作者运行标准的MCTS来递增地构建一个搜索树，用于逐步的解答探索。

如Fig. 1(a)中所示，根节点表示问题 $x$，而与中间步骤 $s$ 相关的子节点是由模型 $M$ 所生成的。一条从根节点到叶子节点的路径，最终的节点 $s_d$ 会形成一条轨迹 $\mathbf t = x \oplus s_1 \oplus s_2 \oplus \dots \oplus s_d$，其中每一步 $s_i$ 都被分配了一个Q值，也就是 $Q(s_i)$ 。

从搜索树 $\mathcal T$ 中，作者提取出的求解轨迹为 $\mathbb T = \{ t^1, t^2, \dots, t^n\} (n \ge 1)$。作者的目标是从 $\mathcal T$ 中选择出高质量的轨迹，来构建出训练集。为了这个目标，作者引入了代码增强的思维链合成方法，来筛选掉低质量的生成数据，并进行海量的模拟以改善对Q值准确度的依赖。

### Code-augmented CoT Generation

先前的MCTS方法主要是生成自然语言的思维链CoTs。然而，LLMs常常存在幻觉问题(hallucination)，即生成错误或不相关的步骤，但也仍然可能会偶然得出正确答案。这些有缺陷的步骤难以检测和消除。为了解决这一问题，作者提出了一种全新的，代码执行增强的，思维链(CoT)方法。

如Fig. 2所示，策略模型生成一个单步的自然语言思维链，及其对应的Python代码，其中自然语言思维链以Python注释的形式嵌入。只有成功执行Python代码的生成结果，会被保留为有效的候选结果。

具体来说，从初始节点 $x$ 开始，作者进行多次MCTS迭代，过程为：选择，扩展，模拟，并反向传播(back-propagation)。在第 $i$ 步，作者收集最新的推理轨迹 $x \oplus s_1 \oplus s_2 \oplus \dots \oplus s_{i-1}$ 作为最新的状态。基于这个状态，作者会用提示词提示(见 Appendix A.3)策略模型，生成n个针对第 $i$ 步的候选状态。然后，使用Python代码执行来筛选出有效的节点。

如Fig. 2所示，每次生成之后，$s_{i,j}$ 都会和所有之前的步骤衔接到一起(concatenate)，形成：$s_1 \oplus s_2 \oplus \dots \oplus s_{i-1} \oplus s_{i,j}$。候选的，能够成功执行代码的结果，就被保留为有效的节点，并由PPM(过程偏好模型)进行打分，这个过程会分配一个Q值 $q(s_i)$。

然后，作者会用众所周知的树置信上界算法(Upper Confidence bounds for Trees, UCT)，来在n个候选结果中，选择出最佳的节点。这个选择的过程，在数学上是如下表达的：

$$
\text{UCT} (s) = Q(s) + c \sqrt{\frac {\ln{N_{parent} (s)}} {N(s)}}; \ \text{where} \ Q(s) = \frac {q(s)} {N(s)}
$$

其中，$N(s)$ 表示了对节点 $s$ 的访问(visits)数量，而 $N_{parent} (s)$ 则是对 $s$ 的父节点的访问计数。预测得到的奖励 $q(s)$ 是由PPM提供的，而且会通过反向传播进行更新。$c$ 是一个常数，用于平衡(对所有父子节点的)利用(exploitation)和探索(exploration)。

### Extensive Rollouts for Q-value Annotation

在公式Eq. 1中，准确的Q值 $Q(s)$ 的标注是非常关键的，主要用于引导MCTS，朝着正确的问题求解路径进行节点选择，以及在所有探索轨迹之内，分辨出高质量的步骤。为了改进Q值的可信度(reliability)，作者从国际象棋选手中获得了灵感，那些选手会基于游戏的结果，对每一步的奖励进行回顾性的(retrospectively)评估。尽管初始的估计可能是不准确的，但是重复不断地进行对局，会随着时间逐渐改善这些评估。

同样的，在每次模拟中，作者会更新每一步的Q值，主要基于该步骤对于达到最终正确结果的贡献的评估。在海量的MCTS模拟过后，持续地导向正确结果的步骤，会获得更高的Q值；偶尔成功的步骤则会得到中等的Q值；而持续错误的步骤会获得很低的Q值。

具体来说，作者引入了两种自标注(self-annotation)方法来获得这些步骤级的Q值。Fig. 1(c)展示了在一个四轮的自我进化过程中，更加细节的设置。

- Terminal-guided annotation. 终端引导的标注。

在最先开始的两轮中，当PPM不可用，或者不够准确时，作者会使用终端引导的标注。形式上，令 $q(s_i)^k$ 表示：在第 $k^{th}$ 轮模拟产生的反向转播之后，对于步骤 $s_i$ 的 $q$ 值。跟随AlphaGo和rStar的脚步，作者基于节点对于最终正确答案的贡献，对每一个中间节点打分，公式如下：

$$
q(s_i)^k = q(s_i)^{k-1} + q(s_d)^k
$$

其中，在第一次模拟中，初始的q值 $q(s_i)^0 = 0$。如果这一步频繁地导向正确的答案，它的q值就会提升；否则，它就会下降。终端节点会被打分为：对于正确答案的终端节点，$q(s_d) = 1$；否则，$q(s_d) = -1$，正如Fig. 1中所示。

- PRM-augmented annotation. 过程奖励增强的标注。

从第三轮开始，作者使用PPM来对每一个步骤评分，为的是更加有效的生成。相比于终端引导标注的那种，需要对一个有意义的q值进行多次的模拟，PPM则会直接预测一个非零的初始q值。过程偏好模型增强的MCTS也会帮助策略模型来生成更高质量的步骤，将解答朝着正确的路径引导。形式上，对于步骤 $s_i$，PPM会基于部分的轨迹，预测一个初始的 $q(s_i)^0$ 值：

$$
q(s_i)^0 = PPM(x \oplus s_1 \oplus s_2 \oplus \dots \oplus s_{i-1} \oplus s_i)
$$

这个q值会基于终端节点的 $q(s_d)$ 值，通过公式Eq. 2中提到的MCTS的反向传播来进行更新。对于终端节点 $s_d$，在训练数据生成期间，作者不会用PRM来进行打分。取而代之的，作者会基于实际标签(ground truth label)，来进行评分，并作为终端引导的奖励。

## 3.3 Process Preference Model

过程奖励模型，会提供细粒度的(granular)步骤级的奖励信号，它非常值得用于解决具有挑战性的数学问题。然而，获得高质量的步骤级别的训练数据，仍然是一个尚未解决的挑战。现存的方法都依赖于人工标注，或者MCTS生成的分数，来对每一个步骤分配一个分数。然后，这些分数会被用于充当训练目标，有如MSE loss或者pointwise loss的方法，用于最小化预测分数和标签分数之间的区别。因此，这些标注的步骤级的奖励分数的预测，直接决定了最终过程奖励模型的有效性。

不幸的是，准确的逐步评分仍然是一个尚未解决的挑战。尽管作者海量的MCTS模拟改进了Q值的可靠性，但是准确地评估细粒度的(fine-grained)步骤质量是一个主要的障碍。例如，在一系列正确的步骤之中，非常难以将其排序成：最好，次好，或平均好，并且还分配一个精确的分数。类似的，在错误的步骤之中，区分最差与中等较差的步骤也面临类似的难题。即使是专家人工标注也非常难以保持一致性，尤其是在大规模操作中，从而导致训练标签的固有噪声(inherent noise)。

作者提出了一种全新的训练方法，能够通过构建出步骤级的，正-负偏好对(positive-negative prefence pairs)，来训练一个过程偏好模型(Process Preference Model)。如Fig. 1(b)所示，不用Q值作为直接的奖励标签，取而代之的，是将它们用于从MCTS树中选择步骤，为的是偏好对的构建。

对于每一个步骤，作者会根据Q值最高，挑选出两个候选步骤作为正向步骤，以及Q值最低的挑选出两个作为负向步骤。其中极为重要的是：被挑选出来的正向步骤必须导向正确的最终答案，而负向步骤必须导向错误的答案。对于中间的步骤(除了最终的答案步骤)，正向和负向步骤对(pair)共享相同的前驱步骤(preceding steps)。

对于最终的答案步骤，因为相同的推理轨迹极少产生不同的最终答案，所以作者就放宽了这个约束。作者通过最高的平均Q值来选出两个正确的轨迹，作为正向例子，以及两个有最低的平均Q值的轨迹选为负向例子。跟\[Ouyang et al., 2022\]文献中做的一样，作者用包含成对排序损失的标准Bradley-Terry模型，来定义出他们的损失函数：

$$
\mathcal L_{ppm} (\theta) = - \frac 1 {2 \times 2} E_{(x, y_i^{pos}, y_i^{neg} \in \mathbb D)} \left [ \log( \sigma(r_{\theta} (x,y_i^{pos}) - r_{\theta} (x,y_i^{neg}) ) ) \right ]
$$

当 $i$ 不是最终步骤时：

$$
y_i^{pos} = s_1 \oplus \dots \oplus s_{i-1} \oplus s_i^{pos}; y_i^{neg} = s_1 \oplus \dots \oplus s_{i-1} \oplus s_i^{neg}
$$

此处，$r_{\theta} (x, y_i)$ 表示PPM的输出，其中 $x$ 是问题，$y$ 则是从第一步到第 $i^{th}$ 步的轨迹。

## 3.4 Self-Evolved Deep Thinking

### 3.4.1 Training with Step-by-Step Verified Reasoning Trajectory

#### Math Problem Collection

作者收集了一个747k数学文字问题的大型数据集，其中问题包含最终答案的真值标签，主要从NuminaMath以及MetaMath中收集。值得注意的是，只有从NuminaMath中获得的竞赛级别的问题（例如，奥赛和AIME/AMC）是被包含了的，因为作者观察到小学水平级别的(grade-school-level)题目并不能显著地改进LLM对于复杂数学的推理能力。

为了增加有限的竞赛级别的问题，作者沿用\[Li et al., 2024\]文章中的方法，并使用GPT-4来合成新的问题，基于7.5k MATH训练集和3.6k AMC-AIME训练分块中的种子问题。然而，对于有挑战性的种子问题，GPT-4经常生成不可解的问题，或者不正确的解答。为了筛选掉这些问题，作者提示GPT-4为每个问题生成10个解答，并只保留其中至少包含三个一致解的结果。

#### Reasoning Trajectories Collection

作者没有使用747k数据集中原始的解答，取而代之进行了海量的MCTS模拟(Section 3.2)来生成更高质量的，逐步验证的，推理轨迹。在每个自进化轮次中，作者对每个数学问题进行16次模拟，这就会生成16个推理轨迹。然后，问题会基于生成的估计的正确比例，根据难度进行分类：

- easy：所有的解答都是正确的
- medium：解答中有对有错
- hard：所有的解答都是错误的

对于困难的没有正确轨迹的问题，一次额外的有16次模拟的MCTS会被运行。在此之后，所有逐步的轨迹以及他们标注好的Q值，都会被收集并筛选，用来训练策略SLM以及过程偏好模型。

#### Supervised Fine-tuning the Policy SLM

通过大量的实验，作者发现：选择高质量的推理轨迹，是微调一个前沿数学LLM的关键。虽然如GPT-distillation和Best-of-N的模型，会包含低质量或错误的中间步骤，但是一个更加有效的方法能够保证轨迹中的每一步都是高质量的。为了达到这个目标，作者使用逐步的Q值来从MCTS模拟中挑选出最优的轨迹。具体来说，对每一道数学问题，作者会从那些引导到正确答案的轨迹中，选择top-2的，有最高平均Q值轨迹，作为SFT的训练数据。

#### Training PPM

PPM是从微调的策略模型中初始化的，而模型中的next-token预测头被替换为一个张量-值头(scalar-value head)，包含一个线性层以及一个 $\tanh$ 函数来将输出值限制在\[-1,1\]之内。

作者筛选掉了那些所有解答轨迹都完全正确，或完全不正确的问题。对于有混合结果的问题来说，作者基于Q值，对每一个步骤，都选择两个正向的和两个负向的例子，而这些例子则是用作训练数据的偏好对。

### 3.4.2 Recipe for Self-Evolution

由于SLMs的能力普遍更弱，作者进行了四轮深度思考，来逐渐地生成更高质量的数据，并且用更加具有挑战性的数学问题，来扩展训练数据集。

> Table 2：747k数学问题在每一轮中正确求解的百分比。只有那些有正确解答的问题会被囊括进训练集中。第一轮使用DeepSeek-Coder-Instruct作为策略LLM，而后续的轮次会使用作者微调过的7B策略SLM。

> Table 3：最终的策略SLM在每一轮的 Pass@1 准确度，展现出持续的改进，直到超越最初的模型(bootstrap model)。

每一轮都使用MCTS来生成逐步验证的推理轨迹，随后，这些轨迹被用于训练新的策略SLM和PPM。训练后的新模型将应用在下一轮，以生成更高质量的训练数据。Fig. 1(c)和Table 2详细列出了在每一轮中，用于数据生成的模型细节，包括训练后的策略模型和PPM，都有所标记。接下来，作者将概述每一轮的具体改进目标和实现细节。

#### Round 1: Bootstrapping an initial strong policy SLM-r1

为了使得SLMs能够自生成相对较好的(reasonably good)训练数据，作者进行了一个引导轮次(bootstrap)，来微调一个初始就很强的策略模型，标记为SLM-r1。如Table 2所示，作者用DeepSeek-Coder-V2-Instruct (236B)来运行MCTS，并收集SFT数据。

这一轮中，没有可用的奖励模型，作者用终端引导的标注来赋予Q值，并为了效率，限制MCTS只进行8次模拟。为了正确的解答，top-2的含有最高平均Q值的轨迹，被选为SFT数据。作者也训练了PPM-r1，但是受限的模拟产生了不可靠的Q值，影响了PPM-r1的有效性。

#### Round 2: Training a reliable PPM-r2

在这一轮中，随着模型更新到7B SLM-r1，作者进行了大量的MCTS模拟，为的是更加可靠的Q值标注，以及训练首个可靠的奖励模型，PPM-r2。具体来说，作者对每个问题，都进行16次MCTS模拟。

最终得到的逐步验证的推理轨迹，在质量和Q值预测上，都展现出了显著的提升。如Table 4所示，PPM-r2比初始轮中的模型，是更加显著有效的。不仅如此，策略SLM-r2也如预期一样，在持续地改进。(Table 3)

#### Round 3: PPM-augmented MCTS to significantly improve data quality

有了可靠的PPM-r2，作者在这一轮中，运行PPM增强的MCTS来生成数据，导向了更高质量的轨迹，能够在训练集中(Table 2)，覆盖更多数学和奥赛级别的问题。然后，这些生成的推理轨迹和自标注的Q值，都被用于训练新的策略SLM-r3和PPM-r3，这两个模型都展现出了显著的改进。

#### Round 4: Solving challenging math problems

在第三轮之后，虽然在小学和MATH问题上，都能达到很高的成功求解率，但是只有62.16%的奥赛级问题被包括进了训练集。这不仅仅是由于作者采用的SLMs的推理能力较弱，也因为很多奥赛级别的问题仍然不能被GPT-4或o1所解决。

为了改进覆盖率，作者采纳了一个直截了当的策略。对于那些16轮MCTS模拟之后还不能求解的问题，作者会进行一次额外的64次的模拟，而且如果需要的话，可以提升到128次模拟。作者也用不同的随机种子进行了多重MCTS树扩展。这促使奥赛级别的求解率暴增至80.58%。

在四轮的自进化之后，747k数学问题中的90.25%能够被成功地覆盖进训练集中，如Table 2所示。在还不能求解的问题之中，极大比例的问题包含综合问题(synthetic questions)。作者人工地审核了随机采样的20个问题，并发现19个都用错误的答案进行了不正确地标记。基于此，作者能得出结论：剩下的不能解决的问题质量很低，而因此终止了第四轮的自我进化。

> Table 4：PPM的质量随着轮次的增加，持续地改进。策略模型已经被固定为策略SLM-r1为了一个更加公平的对比。

# 4 Evaluation

## 4.1 Setup

### 4.1.1 Evaluation Datasets

作者在多样的数学基准测试上评估了rStar-Math。在广泛使用的GSM8K基础之上，作者从多个领域囊括进了更有挑战性的基准测试：

1. 竞赛和奥赛基准测试，例如MATH-500、AIME 2024、AMC 2023、以及奥赛指标。具体来说，AIME是专为挑战美国最聪明的高中数学学生而设计的，2024数据集中包含了30道来自于AIME $I$ 和 $II$ 测试的题目；
2. 从College Math中收集的大学级别的数学问题；
3. 以及领域外的数学评价指标：GaoKao En 2023 (中国高考)。

### 4.1.2 Base Models and Setup

rStar-Math是一个通用的方法，对于多种LLMs都是可用的。为了展现其有效性和普适性，作者使用不同大小的SLMs作为基准策略模型：

- Qwen2.5-Math-1.5B
- Phi3-mini-Instruct (3B)
- Qwen2-Math-7B
- Qwen2.5-Math-7B

在这些模型中，Phi3-mini-Instruct是一个通用的SLM，在数学推理上并没有专长。

由于有限的GPU资源，作者单独在Qwen2.5-Math-7B上，进行了四轮的自进化，产出了4个完成进化的策略SLMs(Table 3)以及4个PPMs(Table 4)。对于其他3个策略LLM，作者使用由Qwen2.5-Math-7B第四轮生成的，逐步验证的轨迹来微调它们。然后，从这一轮生成的最终的PPM，被用作3个策略SLMs的奖励模型。

### 4.1.3 Baselines

rStar-Math 是一种System 2方法。作者将其与代表System 1和System 2方法的三类很强的基准进行了对比：

1. 前沿LLM：包括GPT-4o、最新版的Claude、OpenAI的o1-preview和o1-mini。作者通过AMC 2023、奥赛测试、College Math、GaoKao和GSM8K等基准测试来衡量它们的准确性，其他基准的准确率数据来自公开的技术报告。
2. 开源的强大的推理模型：包括DeepSeek-Coder-V2-Instruct、Mathstral、NuminaMath-72B和LLaMA3.1，这些模型代表了当前，提升大语言模型数学推理能力的主流System 1方法。
3. 原始模型团队训练的基础模型的System 1和System 2性能，包括它们的Instruct版本（比如Qwen2.5-Math-7B-Instruct）和Best-of-N采样策略（比如Qwen2.5-Math-72B-Instruct + Qwen2.5-Math-RM-72B）。值得注意的是，用于三个Qwen基础模型的奖励模型，是72B参数规模的ORM(输出奖励模型)，显著大于作者采用的7B参数PPM。

### 4.1.4 Evaluation Metric

作者报告了所有基准模型的 Pass@1 准确度。对于System 2基准，作者使用了默认的评估设置，比如：o1-mini和o1-preview的默认思考时间。对于使用Best-of-N的Qwen模型，作者重新评估了MATH-500、AIME/AMC的准确度；其他基准评估的结构都来自于他们的技术报告。

为了公平对比，rStar-Math运行MCTS来生成与Qwen相同数量的解答。具体来说，对于AIME/AMC，作者对AIME/AMC生成了16条轨迹，对于其他基准评估则生成8条，并使用PPM来选择最佳的解答。作者也报告了使用64条轨迹，使用更多测试时间计算的性能结果，在表中标记为 $\text {rStar-Math}^{64}$。

## 4.2 Main Results

### 4.2.1 Results on diverse challenging math benchmarks

Table 5展示了rStar-Math的结果，对比的是SOTA的推理模型。作者高亮了三个关键的观察结果：

1. rStar-Math显著地改善了SLMs的数学推理能力，达到了能够跟OpenAI o1相抗衡甚至超越o1的性能，而模型的大小是明显更小的(1.5B-7B)。

例如，Qwen2.5-Math-7B，初始在MATH上的准确率为58.8%，用rStar-Math大幅度改善到了90.0%，超越了o1-preview和Claude 3.5 Sonnet，同时比肩o1-mini。在College Math基准测试上，rStar-Math超越o1-mini 2.7%。在AIME 2024上，rStar-Math得分53.3%，排名仅次于o1-mini，仅7B大小的模型解决了AIME $I$ 和 $II$ 中8/15道题，在最聪明的高中数学学生中排名前20%。

> Notably, 8 of the unsolved problems were geometry-based, requiring visual understanding, a capability rStar-Mathcurrently does not support.

需要注意的是，没有解决的问题中，8道是基于几何的，（求解这些题目）需要视觉上的理解，而这种能力是rStar-Math所暂时不支持的。

2. 尽管使用了更小的策略模型(1.5B-7B)和奖励模型(7B)，rStar-Math显著地超越了SOTA的System 2基准。

相比于Qwen Best-of-N基准，其使用(与rStar-Math)相同的基础模型(Qwen2-Math-7B, Qwen2.5-Math-1.5B/7B)，但是却用了一个大十倍的奖励模型(Qwen2.5-Math-RM-72B)，而rStar-Math则(在没有使用如此大模型的情况下)稳定地将所有基础模型的推理准确度，提升到了SOTA的水平。

甚至在对抗Best-of-N这种使用10倍更大的Qwen2.5-Math-72B-Instruct的策略模型时，rStar-Math在除了GSM8K的基准测试上都超越了它，而且是在使用相同数量采样的解答情况下。

3. 在众所周知的测试基准如：MATH, GSM8K和AIME以外，这些数据集容易发生过拟合的风险，rStar-Math在其他有挑战性的数学基准测试上，展现出了强大的泛化性能，包括Olympiad Bench, College Math, Chinese College Entrance Math Exam (Gaokao)，创造了新的SOTA分数标杆。

如Section 3.4中讨论过的，作者的训练集主要来源于公开数据集，对于这些测试基准，并没有任何专门的优化。

> Table 5：呈现了rStar-Math和其他前沿LLMs，在最具挑战性的数学基准测试上的结果。$\text{rStar-Math}^{64}$ 呈现的，则是在采样64条轨迹下的 Pass@1 准确率。

### 4.2.2 Scaling up test-time computation

rStar-Math使用MCTS来增强策略模型，在PPM的引导下，搜索解答。通过提高测试时间的计算，它会探索更多的轨迹，潜在地提升性能。

在Fig. 3中，作者展示了增加(scaling)长时间(test-time)计算的影响，通过对比官方Qwen Best-of-N的准确率，在四个有挑战性的数学基准测试上，使用不同数量的采样轨迹。只采样一条轨迹，与策略LLM的 Pass@1 准确率相关，显示出在System 1推理上的下降(fallback)。

作者高亮了两个关键的观察结果：

1. 在只有4条轨迹下，rStar-Math显著地超越了Best-of-N基准，超越了o1-preview并且接近o1-mini，展示出它的有效性。
2. 增加长时间计算，在所有的基准评估中都改善了推理准确度，尽管有一些不同的趋势。在Math, AIME, Olympiad Bench上，rStar-Math展现出饱和，或者在64条轨迹上展现出非常缓慢的改进。然而，在College Math上，性能仍然在持续稳定地改进。

## 4.3 Ablation Study and Analysis

作者通过消融实验验证了他们三个主要创新的有效性。针对System 2式推理，AIME和AMC基准测试的 Pass@ 1 准确率采用16条推理轨迹进行计算，而对其他基准测试则使用8条轨迹。

> Table 6：展现了通过rStar-Math自进化的深度思考，而持续改善的数学推理能力。从第2轮开始，由rStar-Math增强的7B基础模型超越了GPT-4o。

### 4.3.1 The effectiveness of self-evolution

在Table 5中的令人折服的结果，是通过四轮的rStar-Math自进化的深度思考得到的。Table 6展现了每一轮中的数学推理性能，呈现出在准确度上的持续改进。

在第1轮，主要的提升来自于对基础模型应用了SFT。第2轮，则通过在MCTS中应用了一个更强的PPM得到了显著的增强，而这解锁了System 2深度思考的全部潜力。值得注意的是，从第2轮开始，rStar-Math就超越了GPT-4o。第3第4轮则展现出更进一步的提升，由更强的System 2推理驱动，通过更优秀的策略SLMs和PPMs。

### 4.3.2 The effectiveness of step-by-step verified reasoning trajectory

rStar-Math生成了逐步验证的推理轨迹，能够消除错误的中间步骤，并进一步用具有挑战性的问题扩展数据集。为了评估它的有效性，作者使用从第4轮生成的数据，作为SFT训练数据，并将其与3个强大的基准进行对比：

1. GPT-distillation，包括了开源的CoT解答，利用GPT-4进行合成，例如MetaMath, NuminaMath-CoT；
2. 从自生成中随机采样，使用相同的策略模型（如SLM-r3），来随机地生成轨迹；
3. 拒绝采样(Rejection sampling)，其中32条轨迹是从策略模型中随机采样得到的，并通过作者训练的ORM(Appendix A.1中提到)进行高质量解答排序。

公平起见，作者对每一道数学问题都选择了两个正确的轨迹，在上述基准模型2和3中。所有的SFT实验，都使用相同的训练方法。

Table 7展现了：在不同数据集上进行微调的Qwen2.5-Math-7B的数学推理准确度。作者高亮了两个观察结果：

1. 通过作者提出的，逐步验证的轨迹进行微调，可以显著地超越所有其他基准模型。这主要由于作者的PPM增强的MCTS，用于代码增强的CoT合成，这个过程在数学解答生成中，提供了密度更大的验证。它证明了，相比于随机采样（缺乏验证）和拒绝采样（ORM只提供稀疏的验证）都要更加有效；
2. 即使是从作者的SLM生成的，随机采样的代码增强的CoT解答，也产出了能够比肩，甚至超越GPT-4合成的Numina-Math和MetaMath数据集的性能。这就表明：作者的策略SLMs，在自进化几轮过后，能够生成高质量的数学解答。这些结果展现了作者方法巨大的潜力，能够在不依赖于高级LLM蒸馏的情况下，自生成更高质量的推理数据。

> Table 7：作者的逐步验证的推理轨迹，作为SFT数据集的有效性的消融实验。作者报告了用不同数据集微调的Qwen2.5-Math-7B的SFT准确率。

### 4.3.3 The effectiveness of PPM

作者同时训练了一个强大的ORM和Q值基于评分的PRM(PQM)用于对比。为了保证公平地评估，作者使用了最高质量的训练数据：在第4轮中生成的逐步验证的轨迹，包含那些能够媲美用于PPM训练的，精挑细选的数学问题。

与PPM类似，作者使用步骤级的Q值，来针对每一个数学问题，选择正向和负向轨迹。ORM是用一个成对的排序损失进行训练的，而PQM则遵循文章\[Chen et al. 2024\]，来使用Q值作为奖励标签，并用MSE损失进行优化。细节的训练数据在Appendix A.1中提供。

> Table 8：奖励模型的消融实验。过程奖励模型(PQM和PPM)超越了ORM，PPM则继续突破数学推理能力的边界。

Table 8对比了ORM, PQM, PPM的性能，使用作者的最终轮次的策略模型进行System 2推理。ORM仅仅在问题求解的结尾提高奖励信号，因此，作者使用了Best-of-N方法，而PRM和PPM则使用的是MCTS驱动的搜索。

如Table 8所示，PQM和PPM都超越了ORM，通过提供更密集的步骤级奖励信号，在复杂的数学推理任务上导向了更高的准确度。然而，PQM却在更加有挑战性的评估指标上受阻，例如MATH和Olympiad Bench，由于Q值固有的不准确。作为对比，PPM构建了步骤级的偏好数据用于训练，使得作者的7B策略模型，在所有的测试基准上，达到了与o1-mini不相上下或者更好的性能。

# 5 Findings and Discussions

## 5.1 The emergence of intrinsic self-reflection capability

在OpenAI o1中的一个关键突破，是它内在的自我反思能力(self-reflection)。当模型犯错时，它会识别到错误，并能够通过一个正确的答案来自我纠正。然而，研究者持续地发现，在开源的LLM中，这种方法基本上是无效的。社区里也一直在积极地探索各种各样的方法，包括自我纠正\[Huang et al\]，自我反思\[Renze and Guven\]，来显式地训练，或者提示LLMs来开发出这种能力。

在作者的实验中，他们出人意料地观察到：他们的MCTS驱动的深度思考，在问题求解阶段展现出了自我反思。

> As shown in Fig. 4, the model initially formalizes an equation using SymPy in the first three steps, which would lead to an incorrect answer (left branch). Interestingly, in the fourth step (right branch), the policy model recognizes the low quality of its earlier steps and refrains from continuing along the initial problem-solving path.

如Fig. 4所示，模型一开始会在前三步中，使用SymPy形成一个方程，这就会导向一个错误的答案(左分支)。有趣的是，在第四步(右分支)，策略模型分辨出了它前期步骤的低质量情况，并避免继续沿着初始问题求解路径前进。取而代之的是，模型会回溯，并使用一个全新的、更简单的方法解决了问题，最终抵达了正确答案。一个额外的自我纠正的例子，在Appendix A.2中提供。

> Notably, no self-reflection training data or prompt was included, suggesting that advanced System 2 reasoning can foster intrinsic self-reflection.

值得注意的是，关于自我反思的训练数据，或者提示词都没有被提供给模型，这表明更高级的System 2推理能够培养出内在的自我反思。

> Figure 5：策略模型的Pass@1 准确度，以及他们在应用多种奖励模型情况下的System 2推理后的准确度，表明奖励模型主要决定了最终的性能表现。

## 5.2 PPM shapes the reasoning boundary in System 2 deep thinking.

策略模型与奖励模型对System 2深度推理机制均至关重要。作者的实验表明，一旦策略模型达到足够强的能力水平时（见 Appendix A.1），过程偏好模型(PPM)就会成为决定性能上限的核心要素。

Figure. 5系统展示了不同大小策略模型的基准准确率，以及奖励模型带来的性能增益。尽管训练策略、数据集构成和模型规模的差异，会导致Pass@1准确率存在波动，但实验证明：奖励模型在System 2推理中占据主导地位。

例如：虽然rStar-Math-7B的SFT准确率低于Qwen2.5-Math-72B-Instruct，但通过集成作者7B大小的PPM，rStar-Math最终的推理表现可以超越采用Qwen 72B ORM的72B大小的策略模型。

此外，值得注意的是，尽管三种不同大小的策略SLM的初始Pass@1准确率存在差异，但在应用PPM后，最终推理准确率呈现出收敛的态势。

## 5.3 PPM spots theorem-application steps

当求解高难度数学问题时，识别并应用相关的定理或者关键的结论，常常是成功求解问题的基石\[Xin et al.\]。在作者的实验中，他们发现：在rStar-Math的解题过程中，他们的PPM能够有效地识别策略模型深度思考过程中，涉及定理应用的关键中间步骤。这些步骤通过更高的奖励分数被预测，从而引导策略模型生成正确的解答。

Appendix A.2 提供了一些例子，呈现的是PPM成功分辨关键定理，例如：费马小定理、高次韦达定理、AM-GM均值不等式、毕达哥拉斯定理以及鞋带定理(Shoelace formula)等等。

## 5.4 Generalization discussions

rStar-Math向改进LLM推理提供了一种通用的方法论，可以将其应用于更加多样的领域。

首先，rStar-Math能够泛化到更具挑战性的数学任务上，比如：定理证明，尽管它最近的关注点是在文字问题(应用题)上，也是由于数据集的限制。但是，rStar-Math展现出了能够证明数学命题的潜力。

如Appendix A.2中所示，它成功地证明了一道奥赛级别的问题，其中包括费马小定理，通过它的深度推理过程，提供了一个逐步的正确的证明。值得注意的是，合成逐步验证的训练轨迹用于一般推理，需要一个机制来提供反馈，用于反映给定的轨迹是否在MCTS模拟的最终阶段，达到了预期的输出。

例如，在代码推理中，这种方法可能会包括设计海量的测试用例；在一般推理中，反馈可能会通过人类标注或者用另一个LLM进行交叉验证得到。

# 6 Conclusion

在这篇工作中，作者推出了rStar-Math，一个自进化的System 2深度思考方法，能够显著地增强小LLMs的数学推理能力，达到SOTA OpenAI-o1级别的性能表现。

作者的方法展现了SLMs能够自生成高质量的训练数据，用于前沿级别的数学推理。在四个不同大小的SLMs和有挑战性的数学基准测试上，进行的大量实验证明了rStar-Math的优越性，在超越现有数学推理LLM和Best-of-N基准的同时，达到了领先的水准。作者也揭示了关键的发现，包括自我反思的出现，以及PPM在识别关键中间步骤时的有效性，例如：定理-应用步骤。

最终，rStar-Math能够通过收集更具挑战性的数学问题，实现进一步的提升，作者将这部分内容留作未来的工作。
