[[2501.04519] rStar-Math: Small LLMs Can Master Math Reasoning with Self-Evolved Deep Thinking](https://arxiv.org/abs/2501.04519)

# Abstract

作者提出了rStar-Math来证明：小语言模型(Small Language Models)能够匹敌(rival)甚至超越(surpass) OpenAI o1的推理能力，且不需要从更高级的模型中蒸馏知识(distillation)。

rStar-Math通过蒙特卡洛树搜索(MCTS)训练(exercising)“深度思考”，来达到这种效果。其中，一个数学策略网络，也就是小语言模型(SLM)进行长时间的(test-time)搜索，该过程由一个基于SLM的过程奖励模型进行引导。

rStar-Math引入了三个创新，来解决训练这两个SLM时遇到的挑战：

1. 一种全新的，代码增强的，思维链(CoT)数据合成方法，该方法执行大量的MCTS尝试(rollouts)来生成逐步验证的推理轨迹(step-by-step verified reasoning trajectories)，用于训练策略网络SLM；
2. 一种全新的过程奖励模型训练方法，能够避免朴素的(naive)步骤级别的得分标注(annotation)，从而产生一种更加有效的，过程偏好模型(Process Preference Model)；
3. 一种自我进化的诀窍(self-evolution recipe)，用在策略网络SLM和PPM都从零开始构建，并迭代进化的过程中，以改善推理能力。

在747k道数学问题的，成百万上千万的合成解答上，通过四轮自我进化，rStar-Math增强了SLM的数学推理能力，到了SOTA的水准。在MATH的基准测试上，它改善了：Qwen2.5-Math-7B从58.8%到90.0%，Phi3-mini-3.8B从41.4%到86.4%，超越了o1-preview4.5%和0.9%。

在USA Math Olympiad(AIME)上，rStar-Math解决了平均53.3%的问题，排在最顶级高中数学学生中的top20%。代码和数据会在[github/MicroSoft/rStar](https://github.com/microsoft/rStar)中开源。

Table 1：rStar-Math赋予了（模型）前沿的(frontier)数学推理能力，通过在64条路径上进行深度思考。

# Introduction

## Background

最近的研究已经证明大语言模型(LLMs)能够解决数学问题。然而，让LLMs在一次推理中生成完整解答的传统方法，近似于System 1 thinking，这尝尝产生快速但是容易出错的结果。

作为回应(In response)，长时间的(test-time)计算尺度(scaling)暗示了一种朝向System 2 thinking的范式转移(paradigm shift)，这通过一个更慢的、更深的思考过程，模仿了(emulate)人类的推理。

在这个范式中，一个LLM作为一个策略模型来生成多个数学推理步骤，然后让另一个充当奖励模型的LLM来进行评估。那些被认为更有可能正确的步骤和解答被最终选择。这个过程迭代地重复，并最终得出最终结果。

## Data

在长时间的计算范式中，关键是训练一个强大的策略模型，来生成好的(promising)解答步骤，以及一个奖励模型来准确地评估它们，而这两个模型都依赖于高质量的训练数据。不幸的是，现成的(off-the-shelf)高质量数学推理数据，众所周知是非常稀少的(scarce)，而且合成高质量的数学数据会面对根本的挑战。

对于策略模型，区分正确和错误的推理步骤是非常有挑战性的，这也导致去除低质量的数据非常复杂(complicating)。值得注意的是，在数据推理中，一个正确的最终答案，并不保证整个推理路径(trace)的正确性。不正确的中间步骤极大地(significantly)降低了数据质量。

而对于奖励模型，过程奖励建模(Process Reward modeling)展现巨大的潜力，通过在中间过程中提供细粒度的(fine-grained)反馈。然而，准确的逐步反馈需要极多的人类标注工作，而且按比例增加这种工作(scale)是非常不现实的，在这一点上(in this regard)，训练数据就更加稀少了。而那些全自动的标注尝试，由于嘈杂的(noisy)奖励得分，仅展现出有限的收获。

由于上述的挑战，现存的用于训练模型的，基于知识蒸馏的数据合成方法，例如增大GPT4-distilled CoT数据，已经展现出边际效应递减(diminishing returns)，而且也不能够超越他们的指导模型(teacher model)的能力；与此同时，从今天起，训练可靠的过程奖励模型PRMs，用于数学推理，依然是一个尚未解决的问题(open question)。

## Writers' work

在这篇工作中，作者引入了rStar-Math，一个可自我进化的System 2风格的推理方法，能够达到SOTA的数学推理效果。在极具挑战性的数学竞赛基准测试上，（它能）挑战，甚至有时超越OpenAI o1模型，而且仅仅是一个用了70亿参数大小的模型。不像那些依赖于更高阶LLMs的用于数据合成的解决方法，rStar-Math充分利用(leverages)更小的模型(SLMs)，利用蒙特卡洛树搜索(MCTS)来建立一个可自我进化的过程，迭代地去生成更高质量的训练数据。为了实现自我进化，rStar-Math引入了三个关键创新。

### 代码增强的CoT数据合成方法

首先，一种全新的代码增强的CoT数据合成方法，进行巨量的(extensive)MCTS尝试，来生成逐步验证过的推理路径，且利用自我标注的(self-annotated) MCTS Q-values。

特别地，数学问题求解在MCTS中被分解成多步的生成。在每一步上，充当策略模型的SLM，采样候选节点，每个节点都生成一个单步的(one-step)CoT，以及与之相关的Python代码。为了验证生成的质量，只有包含成功执行的Python代码片段的节点被保留，因此能够在中间步骤中减轻错误(mitigating errors)。

此外，巨量的MCTS尝试会基于步骤的贡献，给每一个中间步骤分配一个Q值：贡献给更多导向正确答案轨迹的步骤被赋予更高的Q值，而且会被认为具有更高的质量。这就保证了由SLMs生成的推理路径，能够包含正确的、高质量的中间步骤。

### 过程偏好模型PPM

第二，用于训练一个充当过程偏好模型的SLM的一种全新方法。也就是说，一个过程奖励模型PPM来实现我们所需要的过程奖励模型PRM，该模型能够可靠地对每一个数学推理步骤预测一个奖励标签。PPM利用了一个事实，尽管Q值对每一个推理步骤打分来说还不够准确，尽管使用了巨量的MCTS尝试，Q值能够可靠地区分正例(correct)步骤和负例(irrelavant/incorrect)步骤。

因此训练模型对每一个基于Q值的步骤都构建了偏好对(preference pairs)，并利用一个成对的排序损失函数(ranking loss)来优化PPM对每一个推理步骤的得分预测，以获得可靠的标签。这种方法避免了传统方法中，直接使用Q值作为奖励标签的设计，这种方式在逐步的奖励分配中，固有的嘈杂(noisy)而且不准确。

### 自进化方法

最终，一个四轮的自我进化诀窍(recipe)，能够从零开始，逐渐(progressively)构建出一个前沿的策略模型以及一个过程偏好模型PPM。作者从公共开源数据集上，构建的一个747k的数学文字问题开始。在每一轮中，作者使用最新的策略模型以及PPM来进行MCTS，不断生成(increasingly)高质量的训练数据，利用上述的两个模型，来为下一轮训练一个更强的策略模型和PPM。

每一轮达到的逐步细化效果是：

1. 一个更强的策略SLM；
2. 一个更可靠的PPM；
3. 通过PPM增强的MCTS来生成更好的推理路径；
4. 改善训练数据的覆盖范围，来解决更具挑战性的，甚至是竞赛级别的数学问题。

通过四个SLM的巨量实验，以及七个数学推理问题，展现了rStar-Math的有效性。出乎意料地，rStar-Math改善了全部四个SLM，在具有挑战性的数学测试基准上，媲美甚至超越OpenAI o1模型。在MATH基准测试上，设置8条搜索路径下，rStar-Math改进了Qwen2.5-Math-7B从58.8%到89.4%、Qwen2.5-Math-1.5B从51.2%到87.8%。在64条搜索路径下，得分会提升至90%和88.4%，超越了o1-preview模型4.5%和2.6%，而且媲美了o1-mini的90%。

在奥赛级别的AIME 2024中，rStar-Math解决了平均53.3%(8/15)的问题，超过了o1-preview 8.7%以及所有其他的开源LLM。

作者在SOTA的数据生成基准(baselines)上，进一步进行了综合实验，来验证逐步已验证的推理路径的优越性(superiority)，以及PPM与结果奖励模型、基于Q值PRM模型对比之下的有效性。

最终，作者从rStar-Math深度思考中提出了关键的发现，包括内在的自我反馈(self-reflection)能力，以及PPM对于定理应用的中间步骤的偏好。

# Related Works

## Math Data Synthesis

LLM在数学推理上的研究进展，很大程度上依赖于构建(curating)高质量的CoT数据，尤其是在很多前沿方法都被GPT蒸馏的情况下，使用前沿的比如GPT-4的模型进行合成数据。主要工作包括NuminaMath和MetaMath。尽管有效，这仍然限制了模型推理能力，上限只能达到它的指导模型。连指导模型都无法求解的困难问题，则已经从训练集中剔除。甚至可求解的问题，也可能包含容易出错的中间步骤，而这些步骤却很难被察觉。

尽管拒绝采样(rejection sampling)方法能够改善数据质量，他们却不能保证正确的中间步骤。因此，扩大CoT数据的规模，只能获得递减的收益，而这些收益也趋近于饱和(with gains nearing saturation)——例如：OpenMathInstruct-2 尽管使用了增大八倍的数据集大小，却仅仅在MATH数据集上获得了3.9%的提升。

## Scaling Test-time Compute

增多长时间计算已经引入了新的Scaling Laws，使得LLMs能够通过生成多个样本，并利用奖励模型来选择最佳解答，以提升模型的整体性能。多样的长时间搜索方法已经被提出，包括随机采样以及基于树搜索的方法，比如MCTS。然而，针对增多长程计算的开源方法，在数学推理上，已经展现出了有限的收益，这常常是由于策略LLM或者奖励模型的限制。rStar-Math通过迭代地进化策略LLM和奖励模型，来解决这个问题，最终达到类似于OpenAI o1的System 2 数学推理性能。

## Reward Models

奖励模型对于有效的System 2推理是至关重要的，但是却也极难获得。最近的工作，包括LLM-as-a-Judge用于验证；以及专门的奖励模型，比如Outcome Reward Model以及Process Reward Model (PRM)。仅PRMs能够提供非常好的、密集的、步骤级别的奖励信号，用于推理，但是收集步骤级别的标注仍然是一个障碍。Kang 2024, Wang 2024a等工作依赖于昂贵的人工标注数据集，比如PRM800k，而最近的方法则探索了通过蒙特卡洛采样或MCTS的自动化标注。然而，他们非常难以生成准确的奖励分数，而这会限制性能的收益。rStar-Math引入了一种全新的过程偏好奖励(PPM)，该模型完全消除了精确的、步骤级别的、奖励分数的标注需要。

# Methodology

## Design Choices

MCTS用于有效的System 2 推理。作者的目的是训练一个数学策略SLM，以及一个过程奖励模型(PRM)，并将两者整合到蒙特卡洛树搜索MCTS中，以实现System 2 的深度思考。MCTS被选中，是因为了两个关键原因。

首先，它将复杂的数学问题分解成更简单的单步生成任务，降低了