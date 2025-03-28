> Proposing and Solving Olympiad Geometry with guided tree search[[TongGeometry]]

# Title

- 题目提到了Proposing“提出”也就是出题，与Solving“求解”，用于求解几何问题，与AG切入求解几何问题的角度是一致的。
- 同时，题目中也说到了Guided Tree Search，虽然跟我们相同，但是具体怎么做，后续文章里也没有具体说。这里我用粉色标记的，就是感觉应该说的，但是没说的点。

# Abstract

- “最广泛的几何定理库”，我认为这个跟AG一样，模糊了那些所谓定理、题目、构造这些概念。或者说，他们对于“定理”的理解是不同的。
- 以及“发现了67亿条定理”，“41亿条展现出了几何上的对称性”，只是数字看着很唬人，它长什么样我们可以到最后来看。
- 而且这里提到的展现出对称性，和AG的对于合成数据的结果是不太一样的。还记得AG1的时候，他们说AG自动合成的定理，并不受限于人类的“美学偏见”。等会我介绍AG2的时候，他们有一道求解结论也在强调这个观点，可能也是他们一直以来的风格。

# Introduction

## Proposing and Solving Problems

- 我本来以为这里会详细介绍TG如何实现“出题”与“解题”，结果完全没有，感觉像是故意隐去了全部的细节。
- 容易引起争议的公理化，强调了几何领域专用语言的重要性。

## TG

- 通过引导树搜索探索几何空间，以及利用反向追踪和前向推理证明，来生成几何问题（出题）。但是我并没有看到作者对于可扩展性的解释。
- 然后是一些非常恐怖的数字，也就只能看一看，并没有说到生成的方法和具体的例子。
- 关于这道题目，作者在附录中给出了，是2024全国高中联赛北京赛区预赛二试的第二题，唯一一道几何题。
- 虽然作者多次提到了题目包含辅助构造，但是没有给出具体例子，形式和在求解中的体现都不清晰。

## Result

- 提到了外生项的实例，这个跟AG的提法是一模一样的。
- 作者微调了两个LLM，一个专门用于建议可能的搜索方向，而另一个则用于评估每一个方向的步骤数。（根据文献，一个是DeepSeek-Coder，另一个是Compute-optimal LLM）
- 然后提到了神经-符号系统，但是也没具体说是如何设计的。尤其是这个符号系统，非常让人好奇到底是什么。当然，从好的方面来讲，这再次证明符号系统的不可或缺，同时我们也需要注意到：在求解高难度问题上，神经系统的重要性。

# Assessing TG

## Evaluating generated ...

- 使用了196个现存的奥赛问题作为指导数据，然后进行大量的生成。跟之前一样，也不知道具体是什么。
- 这里也是AG2文章中提到的，用来“攻击”TG的点，就是TG用了196个奥赛问题作为指导性的统计数据，而AG2则是完全基于对前提的随机采样。
- 生成题目的话，TG提到了一篇GeoGen，非常抽象的是，这是一篇毕业论文，我还没看。

## Search discovers ...

- 提到了回放缓存，但是没有说具体设计。只简单介绍了能够让TG从缓存的中间状态继续树搜索，感觉跟AG2的SKEST有异曲同工之处。

## Performance on benchmarks

- 提到了他们自己构建MO-TG-225，从作者的196道奥赛题目池中挑选出来的。
- 他们也采用了AG提到的超长时间的求解计算。
- 后面就是一些数据啊，一些评价，没什么有营养的信息。

## Expert evaluation on ...

- 对于2024 P4，他们也进行了测试，生成了两个等价的答案，专家认为是对的。
- 具体说到他们给出的Figure 2的话，没有什么信息，他们的关键步骤看上去标注的也非常简单。

# Conclusion

- 最后提了一嘴Actor-Critic，什么策略网络学习完成辅助线；值网络用来估计距离题目结束，还有多少步骤，具体也不知道什么玩意儿。
- 然后第四次提到了，TG相比做题的学生来说，更像是教练的角色。
- 非常奇怪，整篇文章写得非常虚，一些基本的细节也没有，连框架的细节都没有。简直可以说是一篇用GPT写的文章，即便是效果如此炸裂的情况下，居然没有揭露一定的细节，让人非常疑惑。
