# 视频资源

[OpenAI Codex 论文精读_Bilibili](https://www.bilibili.com/video/BV1iY41137Zi/?spm_id_from=333.880.my_history.page.click&vd_source=5c0fccc3e62acb7264f3cd05395b00c0)

# Codex能干什么？

- 通过注释补全函数
- 通过函数名直接补全函数
- 支持多种程序设计语言
- 通过前文的重复的内容，生成(猜)后续的内容
- 补充测试样例
- 同一功能可以提供多种语言的实现

# 技术描述

Open AI在2021年8月发表的博客，介绍了Codex，其中甚至提到了一个demo，是用自然语言来进行游戏开发。只需要描述你需要的游戏内容，它可以直接帮助生成代码。

它可以解决程序员的两大**痛点**：

1. 如何给函数和变量命名
2. 如何整理文件夹

# 对应论文

>[!Paper]
> **Evaluating Large Language Models Trained on Code**
> 在代码上评估大语言模型的性能

# 摘要

**Codex**：

- 一个基于GPT的语言模型
- 使用GitHub上公开的代码进行了微调
- 原文提到，只调查了Python的代码，与GitHub Copilot上的版本不同
- 如果可以允许重复采样(Sampling)一百遍，使用同样的模型，可以解决数据集中70.2%的问题
- 最后分析了一下其影响

通过摘要整体来看，本文相对简单，没有做非常大的改动，只是把GPT在一个新的数据集上训练了一下，并解决一个新的应用，然后效果比较好。

> 可以不用太关注文章在算法上的创新，可以关注文章集中解决了一个什么问题，这个问题是否重要，或者他们解决的是不是很好。

# 导言

团队在做GPT-3时，发现GPT-3可以解决一些简单的代码问题，但是如果用GPT-3来写比较复杂的代码，就比较困难了。

这是因为GPT-3在训练的时候并没有使用特别多的代码，在GPT-3论文里讲局限性的时候，也提到过它不能去做在训练数据里面没怎么出现过的任务。

所以他们就认为如果将GPT-3在公开的代码上重新训练一下的话，或许在代码能力上会有所提高，于是他们就把这个新“东西”叫作Codex。

总结来看，这个模型在本质上与GPT并没有区别，只是用于训练的数据变了，模型的权重也就应该发生变化，所以这一套权重（模型内的参数）叫Codex。

