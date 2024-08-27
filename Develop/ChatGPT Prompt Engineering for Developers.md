# 网页资源

Deeplearning.ai：
- 貌似不需要梯子
- [ChatGPT Prompt for Developers](https://learn.deeplearning.ai/chatgpt-prompt-eng/lesson/1/introduction)
- [Pair Programming with a LLM](https://learn.deeplearning.ai/pair-programming-llm/lesson/1/introduction)

B站：
- [ChatGPT Prompt提示词课程 吴恩达](https://www.bilibili.com/video/BV1Tw411S7ni/?spm_id_from=333.788.recommend_more_video.1&vd_source=5c0fccc3e62acb7264f3cd05395b00c0)
- [给编程开发者的「ChatGPT」必会使用指南](https://www.bilibili.com/video/BV1iN411U7sy/?spm_id_from=333.880.my_history.page.click&vd_source=5c0fccc3e62acb7264f3cd05395b00c0)

ChatGPT Prompts Engineering：
- [ChatGPT 中文调教指南 (github.com)](https://github.com/PlexPt/awesome-chatgpt-prompts-zh)
- [ChatGPT prompt curation to use ChatGPT better. (github.com)](https://github.com/f/awesome-chatgpt-prompts)

其他：
- [ChatGPT与DALL·E 3之间的行业「黑话」被人发现了](https://www.51cto.com/article/770658.html)
- [Principled Instructions Are All You Need for Questioning LLaMA-1/2, GPT-3.5/4 (arxiv.org)](https://arxiv.org/pdf/2312.16171.pdf)

# 提示词工程

LLMs的强大不在于我们能够通过提示词来做很多“一次性”的任务，而是我们能够通过API的接口调用强大的LLM工具，来快速搭建我们自己的应用。

这个课程会更加关注**Instruction Tuned LLM**，因为其使用RLHF技术，生成更加安全可靠的文本。其次，它能够根据人类输入的指令，更好地生成有指向性的文本。

想象我们交给LLMs任务时，就好像在跟一个非常聪明的人说话，但是TA不知道我们任务的细节。所以，有时候LLMs没有按照我们预期的工作时，可能是因为我们的**提示词不够清晰**。

一些更加具体、更加清晰的提示词，甚至是一小段与内容相关的内容片段，让LLM提前了解，都可以让LLM更好地生成我们真正想要的内容。

# 两个原则

1. 提供清晰、明确、具体的指令 (Write Clear and Specific Instructions)
2. 给LLM一点时间去思考 (Give the model time to think)

# Principal 1

> Write Clear and Specific Instructions

你需要给模型一个清晰且具体的指令，它才能按照你的指令完成工作。

此处，clear $\ne$ short。
