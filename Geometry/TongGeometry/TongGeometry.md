[Proposing and Solving Olympiad Geometry with Guided Tree Search](https://arxiv.org/abs/2412.10673)

# Abstract

数学奥林匹克是享誉盛名的竞赛，其中问题的提出和求解都是十分荣耀的事情。构建能够提出并求解奥赛问题的人工智能，在自动定理发现和证明中，仍然代表着一个尚未解决的挑战，尤其是在几何问题中，因为它包含了数字和空间的元素。

作者提出了TongGeometry，一个欧式几何系统，支持基于树搜索引导的问题提出和求解。该高效的几何系统，搭建了迄今为止，最广泛的几何定理库(repository)：和现有的SOTA，在相同的计算资源(budget)下，TongGeometry发现了67亿条几何定理，且需要辅助构造，包括41亿条展现出了几何对称性。

在它们之中，10个定理被提交到地方数学奥林匹克竞赛中，其中TG提出的3个，在实际竞赛中被选中，在中国和美国的国家队资格考试，或者顶级的国内奥林匹克竞赛中获得一席之地。

由微调过的大语言模型指导，TG求解了IMO-AG-30中所有的IMO几何问题，首次超越了金牌选手。它也同样超越了现有的SOTA，在覆盖面更广的奥赛级别的问题中。

系统的全部能力能够被用于一个消费级的机器上，使得模型更加易于使用，而且促进了其使用的普及化(democratization)。由此类推，不像其他现有的系统仅仅能像学生一样求解问题，TG更像一个几何竞赛教练，发现，展示，并求解问题。

## Significance

该研究提出了TongGeometry，一个用于欧式几何问题提出和求解的系统，搭建了数字与空间推理的桥梁。TG超越了SOTA方法，通过独立自主地发现67亿条需要辅助构造的定理，包括41亿条展现出了几何的对称性。它提出的问题出现在有声望的数学奥赛中，以及在求解IMO几何问题中，超越人类竞赛者，展现出了它的现实影响。

该成就代表了迈向自动定理发现和问题求解的一大步，类似于从一个“学生”系统向一个“教练”系统的转变，该系统能够生成并求解高水平的问题。更重要的是，它的效率以及在消费级计算机上的易用性，普及了高级几何问题求解，使得更广阔的教学和研究应用变得可能。

# Introduction

## Background

定理证明，是一个从初始状态导航(navigate)到目标状态的过程(journey)，运用的方法是：通过连接潜在缺失的中间条件，该过程可以展现出超凡的问题求解能力。对任何一个人来说，赢得IMO的奖项是一个非常特别的荣誉，而且是一个对于他/她的问题求解能力的最好证明。

自从IMO开始举办以来，其已经成为了一个最好的平台，用于评估(identify)世界上最强的数学天才的能力。IMO也不经意间成为了AI领域研究者的目标，他们致力于开发出定理证明模型，能够在世界上最有威望的数学竞赛中，匹敌或者超越人类竞赛专家。

## Proposing and Solving Problems

提出奥赛级别的问题，在数学社区中也会受到高度的尊重，然而极少有计算方法能够实现自动的问题提出和严密的提案(供题)验证(proposal verification)。奥赛问题通常建立在多年的中间定理的发现，而且有非常严格的标准。最具代表性的问题看起来简单得令人难以置信，而且用基本的数学知识就能理解，但是却需要非凡的创造力才能完全解决。此外，题目优雅的特性，比如各种各样形式的对称性，通常是非常关键的。

提出或求解奥赛级别的问题，对于计算方法是相当大的挑战，因为海量的分支点，以及非常有限的历史题目池，能够用于启发式搜索的发展。尤其是几何问题，在其他奥赛主题中脱颖而出，因其同时包含了数字和空间的推理，从而不同于其他主题基于文本，能够更加轻松地被大语言模型所适应。此外，在欧式几何中有争议的公理化，要么会使得完备性(completeness)失效，要么则会使得应用于其他领域中的，交互式证明助手的人类可读性失效。尽管具体的专为几何领域设计的语言已经被创建，它们的设计局限导致了对于奥赛级别问题的不充分探索，仍然存在研究的空间。

## TongGeometry

在作者的工作中，他们提出了TongGeometry，一个用于合成欧式平面几何的基于树的系统，能够通过反向追踪(backward tracing)和利用前向推理的定理证明来推动人类可读的问题提出(problem proposing)。利用该可扩展而且高效的系统，以及196个奥赛问题作为指导性的统计数据，作者收集了41亿个带有证明的、对称的几何问题，它们被包含在67亿个需要辅助构造来进行完整证明的问题中。以此类推，相比于现存的只能用于求解问题的“学生”系统，TongGeometry充当的是“教练”的角色，它能够用一个有限的树，来特征化描述几何空间，并且使得引导问题提出和证明变得可能。

作者也构建了，对于奥赛问题的难度和适用性的评分指标，并且在其中选择了一些作为提案(proposals)。其中一个已经被2024年全国高中数学联赛(北京)所选择，该联赛是中国国家队的资格选拔竞赛。另外两个，则入围了2024美国Ersatz数学奥林匹克，一个美国顶级的国内数学奥林匹克竞赛。

生成的数据包含了大量的辅助构造来求解几何问题。填补这些辅助构造对于成功的几何定理证明是非常关键的；这些外生项使得一个证明系统能够弥补初始状态和目标之间的差异。因此，作者利用这些数据来指导TG进行树搜索，当它被要求求解一个问题时。

## Result

特别的，作者微调了两个LLM：一个专门用于建议可能的搜索方向，而另一个则用于评估每一个方向的步骤数。当在IMO-AG-30基准测试中，收集的过去23年的IMO问题上进行测试时，神经-符号系统在一台有32个CPU核心和单GPU卡的消费级机器上，在38分钟内求解了每一个问题，成为了首个超越平均IMO金牌选手的系统。

# Assessing TongGeometry

## Evaluating generated problems for olympiads

利用196个现存的奥赛问题作为指导数据，作者使用10368个并行CPU核心，进行了大规模的并行问题搜索。在30天的搜索中，TG在几何问题定义的空间内，穿过了(traverse)143,379,886条(1亿4千万)独立的路径(总共有170,883,417条，1亿7千万条路径)，推理了1,851,166,755(18亿5千万)个独立的状态。

在每一个独立的路径上，TG发现其中平均0.7613的构型(configuration)是需要辅助构造的，也就是109,157,477(1亿)个构型(即上下文和辅助构造对)。在它们之中，70,703,508个是独立的。在过滤过后，作者最终得到了6,688,310,403个问题(66亿个，上下文，目标，辅助构造的三元组)，其中4,096,680,574(40亿)个是对称的。见Figure 1(B)中，有与AG搜索效率的对比。

既然有了数十亿的问题可用，有选择性地为奥林匹克竞赛进行供题，检索合适的供题的挑战仍然没有得到解决，因为缺乏自动评估机制。受到GeoGen方法论的启发，作者开发了一组用于问题选择的评估标准。

在2024年高中联赛，以及2024美国Ersatz数学奥赛的供题截止日期之前，作者招募了一位2023 IMO金牌得主，和中国国家队的学生成员来人工检查一组供题，而且是在题目搜索的初始阶段。4道供题被提交到了2024高中数学联赛(北京)，其中1道则被选为了最终竞赛题目中唯一一道几何问题。此外，6道供题被提交给2024美国Ersatz数学奥赛，其中2道入围。值得注意的是，AG只求解了作者10道供题中的3道。

## Search discovers known lemmas and base configurations

在所有的供题中，作者发现了著名的引理。Figure 1(D)展示了一个数学社区中为人熟知的旋转对称引理，其中三条绿线在九点圆圆心中是一致的(concurrent)。

为了使得问题探索变得可能，作者实现了一个“回放缓存”，允许树搜索可以从缓存过的，有希望求解问题的中间状态重新开始。这些状态包括基本的、数学社区中熟知的构型(configurations)，这些通常会在构建更加复杂的问题情况时，充当脚手架(scaffolds)的角色。

Figure 1(F)解释了一个对称的例子，该例的核心是伪内切圆和内心/外心引理。这个构型已经被一个美国队的教练，盛赞为“最丰富的(richest)构型”

## Performance on benchmarks

作者用TG在两个基准测试上进行了定量分析，AG中构建的IMO-AG-30数据集以及在开发TG时最新铸造的(coined)数据集MO-TG-225。IMO-AG-30数据集包含了30道问题，源于将过去23年中的IMO题目，翻译成领域内专用的AG语言。

作为对比，MO-TG-225数据集囊括了225道数学奥赛问题，选自作者用于计算搜索统计的196道问题池。MO-TG-225已经被翻译成了TG的领域专用的语言，而且这些问题完全没有出现在TG的训练数据集中。

在实验中，作者对比了TG与AG，GPT-4以及它的o1变体。考虑到TG和AG运用了不同的领域专用的语言，作者将每个问题的原始描述，翻译成了每个模型所各自需要的语言。对于评估GPT-4和o1，作者使用了自然语言的格式，如同每道问题在竞赛中呈现的那样。所有模型对每个问题都使用标准化的90分钟时间限制进行评估。

Table 1(left)展现了每个模型和人类平均水平在IMO-AG-30上的表现。当最新的推理增强型语言模型o1在一系列任务上达到令人钦佩的结果时，作者也发现语言模型仍然很难处理严密的、数学中的几何推理，通常它们会生成含有错误逻辑和幻想中间结果的证明。AG的DD+AR路径显著地改进了吴法(仅求解10题)，然而它的符号推理引擎却很冗余，且未达到最优(suboptimal)。

通过重新设计和优化推理数据库方法，TG的推理后端，仅仅使用DD就成功求解了18道问题——超越了平均的IMO参赛选手。通过使用学习到的价值启发式方法，TG成为了首个超越IMO金牌选手的方法，成功地证明了基准测试中的全部30道问题。一个更近的调查揭示了：值模型在求解基准测试里，两道最具挑战性的问题(IMO 2000 P6, IMO 2008 P8)的过程中，起到了非常关键的作用。

相比于AG，TG在一台消费级计算机（32个CPU核心，一个单卡4090 GPU）上，在最多38分钟内，达到了这些效果。而AG则需要246个CPU核心，4张A100 GPU才能将求解时间降低到90分钟以内——这是一个资源密集型的设置，不适用于大多数用户。

Table 1(right)展示了各种模型在MO-TG-225上的性能表现。不同于IMO数据集，MO-TG-225包括了不同来源的题目，使得严密的人类评估基准变得不可用。在评估GPT-4和o1时，作者观察到，当给出提示词，让模型证明一个已知定理时，比如欧拉线定理，LLM频繁地假设定理已经存在，并且直接不加所需证明地应用定理。

作者将这些反馈视作正确，并解释了表中报告的一些解答。与在IMO-AG-30数据集上的结果一致，TG的DD后端展现出改进了的问题求解能力，相比于AG的DD+AR，总体上达到了接近AG的性能等级。作者注意到AG的成功，很大程度上来源于它的后端引擎，它总体求解中的72.5%，是通过DD+AR实现的。

作为对比，TG不仅解决了更多比例的问题(81.3% vs 45.3%)，而且利用了它的神经模型来有效地实现辅助构造，而只通过DD却只求解了55.2%。

对于值启发算法的消融实验说明了，在一个资源受限(resource-constrained)的环境中，比如32个核心和1个显卡，值模型能够压榨出最后一bit的性能，相比只使用策略模型，可以改进到3.4%(MO-TG-225)以及7.1%(IMO-AG-30)。

## Expert Evaluation on TongGeometry results

Figure 2展示了IMO 2024 P4，是最近的IMO竞赛中的几何问题，在TG训练的时间点，是一个相对新的，且没有很多已有文本记录(解答)的问题。针对该问题，TG生成了两个等效的解答。作为回顾，作者观察到这两种解答引入了与官方解答中相似的辅助点。作者邀请了2024 IMO金牌选手来评估了TG的解答，并且两个都被认为是正确的。

# Conclusion

在这篇工作中，作者提出了TongGeometry，一个神经-符号系统，设计目的是为了发现、展示、并证明IMO级别的几何问题，通过引导式树搜索。作者实现了用于评估TG提出问题的评估指标(rubrics)。值得注意的是，其中之一已经被2024年全国高中数学联赛(北京)选上，一个中国国家队的资格竞赛；其中之二，则入围了2024美国Ersatz数学奥林匹克，一个美国国内顶级的数学竞赛。

TG运用了Actor-Critic风格的推理，其中一个策略模型学习完成辅助构造，另一个值网络则用于评估，直到问题求解前的剩余步骤的数量。该问题求解的风格首次成就了超越金牌选手的性能，而且是在IMO级别的问题上，超过先前的SOTA，同时展现了神经模型对于辅助构造的更加有效的使用。

由此类推，TG工作起来比起仅仅求解问题的学生，更像是一个教练，它为未来进一步推进几何和数学领域的研究开辟道路是一个非常好的贡献。
