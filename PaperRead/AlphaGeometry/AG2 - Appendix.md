# A. Fine-tuning of math specialized language models on AG data

尽管在初步转向AG2的过程中，作者保留了AG1的训练设置（从零开始，使用AG领域专用的语言构成的定制分词器），很自然的问题是：是否微调一个已经掌握问题求解能力的模型，能够提升性能呢？这样的微调并不立即可行，由于在使用的分词器和训练语言中，存在一些不同。在本节中，作者会探索定制分词器和领域专用的语言，所扮演的角色，然后再讨论数学专用的Gemini模型在AG数据上的微调。

## Tokenizers

分词器在现代语言模型中非常重要，更加广泛地说，对任意一个基础模型来说都是。研究者们普遍认为，分词器可能是模型解决数学问题的主要瓶颈(Singh 2024)。作者在控制AG的设定下，仔细研究了这个假设。为此，作者训练了具有相同架构，但采用不同分词器的模型：

- 定制分词器，使用几千个tokens构成的词汇表
- 大语言模型分词器，使用3万个tokens

回想一下，作者的定制分词器是在单词级别(word-level)被构建的，也就是说，相比于副词级别(subword level)的token，每一个token都有完整的含义。在AG使用的语言中，有如下几类tokens：

1. 点名："a", "b", "c", ... "z", "a1", ..., "z1"。
2. 谓词名：`coll`, `cong`, `coll`, `cyclic`, `eqangle`, `eqratio`, `acompute`, `rcompute`, `aconst`, `rconst`, `distmeq`, `distseq`, `angeq`, `overlap`, `noverlap`, `sameclock`, `lessthan`。
3. 数字和分数：1, 2, 3, ..., -, /。
4. 谓词引用词元(token)：`(000)`, `(001)`, `(002)`, ..., `(999)`。
5. 预留的词元(token)：`{Analysis}`, `{Numerical}`, `{FromGoal}`, `{Proof}`, `x00`, `:`, `;`, `.`。

有点意外的是，作者发现AG在IMO 2000-2024几何问题上的性能，在使用不同分词器的情况下，性能是一致的，这说明现代LLM分词器可能已经足够灵活(flexible)来进行数学运算。

## Domain specific language

除了词元分析器之外，研究领域特定语言，在语言模型解决数学问题能力方面的作用也很有趣。我们会非常自然地认为：使用领域特定语言能够简化数学运算，并避免因使用不够严谨的语言，而可能出现的明显错误。为了对此展开调查，作者将所有AG2的数据从AG语言翻译成自然语言，并训练一个新模型。然后，作者将这个新模型的表现与基于原始AG数据训练的、规模相同的模型进行比较。同样令人有些惊讶的是，在2000-2024 IMO的几何问题上，作者得到了相同的结果，这为在自然语言中预训练的大型语言模型，针对数学数据进行微调开辟了一条道路。接下来，作者展示了一个将AlphaGeometry翻译成自然语言的示例。

- AG语言：`d e f g : coll a d g (000) coll f a b (001) coll d b c (002) coll e c a (003) cong d b d c (004) cong f a f b (005)`
- 自然语言：`Construct points d e f g such that a d g are collinear (000), f a b are collinear (001), d b c are collinear (002), e c a are collinear (003), |d b| = |d c| (004), |f a| = |f b| (005)`

> 无词元分析器的模型也是一个非常活跃的研究领域，例如文章(Deiseroth et al. 2024)

## Fine-tuning of language models pre-trained on math data

既然之前作者已经证明了：定制的分词器和领域专用的语言，并不在AG中扮演非常重要的角色，作者就采用了在多样的数学数据上预训练的语言模型。作者首先从一个33亿参数，在公开数学数据集上训练的Gemini模型开始，然后在AG数据上，以无监督方式微调它。

在作者的IMO-AG-50评估集上，经过微调的模型的表现，与较小的模型，以及从零开始训练的33亿参数规模的模型相当。另一方面，作者发现，尽管所有这些模型都是在相同的AG数据上进行训练的，但它们确实会产生略微不同的辅助点提议，并且通过Section 6中描述的知识共享机制相互帮助，从而形成了一个类似集成系统的结构（见Figure 4）。

> Figure 10 | 两个3B模型的学习率曲线：其中之一是从零开始训练，另一个则是在先在数学数据上预训练，再在AG数据上进行微调。在数学数据上进行预训练的模型，在一开始有更低的损失，但在2千亿tokens的训练之后，都收敛到了同样的点。

# Multi-modal

直到现在，作者将AG2视为一个将语言模型和符号引擎耦合在一起的系统。然而，既然作者地语言模型是基于Gemini 1.5，一个从设计上就是多模态的模型，那么通过多模态推理增强AG模型也是非常自然的。为此，作者训练了一个模型簇(family)，与问题文本一起，把相关的图形图片也作为输入。用于训练以及在训练时，图形是由Section 3中所描述的算法构建的。

尽管在训练时得到了有前景的结果，但是，当作者单独使用该模型时，并没有在下游IMO问题的求解率上观察到任何提升。然而，就像在微调预训练模型的情况中一样(见Section A)，作者发现多模态模型会产生略微不同的辅助点建议。通过知识共享机制(Section 6)结合其他模型，会提升整体的性能。

> We hypothesize that adding the image on its own might not help that much due to very complicated diagrams, which become very crowded for the IMO problems. The image tokenization process might also play a negative role, as it splits the diagram into independent sequential patches which leads to losing some spatial information.

作者假定：增加图像本身，并不会帮上太多忙，因为图形非常复杂，对于IMO问题来说，图形也会变得十分“拥挤”(crowded)。图像分词化(tokenization)的过程，也有可能会产生负面影响，因为它会将图像分割成相互独立的序列块，从而导致部分空间信息的丢失。

而且我们也需要记得：一些关于图形的细节已经通过文本提供了，如Section 7.2提到的那样，而且作者的符号引擎DDAR，确实可以访问到图形的拓扑结构信息(topological aspects)。例如，通过检查 `sameclock` 谓词。更进一步的，文章(Chae 2024) 展示出视觉-语言模型的原子视觉能力(atomic visual skills)非常差，这就表明增加视觉元素可能并不会助力几何问题求解的过程。

最后需要注意的是，几何问题求解的核心取决于代数推理(algebraic reasoing)，正如之前在AG1中提到的那样，而不是几何上的推理(geometric reasoning)。许多人类IMO竞赛者能够非常稳定地(可靠地 reliably)求解几何问题（包括如IMO 2011 P6那样非常困难的问题），通过使用计算的方法，例如复数、重心坐标(barycentric coordinates)、以及暴力三角方法(trigonometry bashing)，这意味着视觉信息和图形对于求解几何问题来说并不是非常关键。

# C. Featured AlphaGeometry2 solutions

## IMO 2024 P4

在所有已经解决的IMO问题中(Figure 8)，作者团队中的几何专家认为许多AG的解法展现出了超越人类的创造性。一个类似的例子是IMO 2024 P4问题(Figure 11)。

- IMO 2024 P4：令三角形ABC，有内心I，满足 $AB<AC<BC$ 。令X是线段BC上的点，不与C重合，满足穿过X的直线，平行于AC，并相切于内接圆。类似地，令Y是边BC上的点，不与B重合，满足穿过Y的直线，平行于AB，并相切于内接圆。边AI与三角形ABC的外接圆，再相较于点P。令K和L，分别是AC和AB的中点。证明：$\angle KIL + \angle YPX = 180 \degree$ 。

该问题聚焦于 $\angle KIL$ 与 $\angle XPY$ 之间的关系。前者是由一个中点和内心形成的角，中点和内心通常不太协调(go well)，而且不能通过主三角形ABC的角度来计算得到。通常，一个人类竞赛者会依赖于三角几何(trigonometry)、复数、或者其他计算的方法来找到解答。

对于AG来说，它的DDAR系统只依赖于简单的角度跟踪和比例跟踪(chasing)，因此，这就凸显出一些辅助点的构造是非常必要的。为了这个目的，AG构造了BI上的一点E，使得 $\angle AEB = 90 \degree$ ，这一步优雅地将这些看起来毫无关联地几何元素联系在一起，通过创建两对相似角，ABE和YBI，ALE和IPC。这些相似三角形对创造了新的相等角对，和相等的边长比例关系。也就是说，点 $E$ 让线段 $AB$ 的中点 $L$ 有了存在的意义。

为了完成证明，我们需要证明 $\angle AIK = \angle BYP$ 以及 $\angle AIL = \angle CPX$ 。为了这个目标，我们需要证明出三角形AKI相似于三角形BPY，以及三角形ALI相似于三角形CPX，而这是通过比边长比例追踪得到的，也就是从上述的相似三角形对中得到的。

完整的证明公布在：[AG2 solution to IMO 2024 P4](https://storage.googleapis.com/deepmind-media/DeepMind.com/Blog/imo-2024-solutions/P4/index.html)

这个解答在IMO 2024中，30秒内就得到了，而且被Joseph Myers认为应该得到满分7分，他是一个两届IMO金牌选手，以及 IMO 2024 选题委员会中的主席。

## IMO 2013 P3

除了IMO 2024 P4之外，AG仅用1个额外的辅助点就能解决许多具有挑战性的问题，其中一些问题涉及到相当不常规的构造方法。其中，一个这样的问题就是IMO 2013 P3。

- IMO 2013 P3：设三角形ABC中，顶点A所对的旁切圆与边BC相切于点 $A_1$​。类似地，利用顶点B和C所对的旁切圆分别定义点 $B_1$​（在CA上）和 $C_1$​（在AB上）。假设三角形 $A_1 B_1 C_1$ 的外心在三角形ABC的外接圆上。证明：三角形ABC是直角三角形。

在这道题中，AG简单地构造了弧 $A \hat B C$ 的中点D，包含B作为额外点，这种构造是非常规的，因为它并不对称。然而，该构造允许AG揭露一个事实：就是 $B_1, A_1, D, I_{\alpha}$ 是共圆的点，当且仅当 $AB \perp AC$ 时，这是一个关键的结论。为了证明这个结论，AG又利用了结论：$O_1$ 和 $D$ 构成了相似三角形对 $\triangle O_1 C_1 B_1 \sim \triangle O_1 B C$ 以及 $\triangle D A_1 B_1 \sim \triangle D B A$，然后利用这些结果来推进角度跟踪，也就得到了 $\angle D A_1 I_{\alpha} = \angle D B I_{\alpha}$ ，紧接着可以得到 $B_1, A_1, D, I_{\alpha}$ 是共圆的点。

## IMO 2014 P3

另一个例子是IMO 2014 P3，在IMO历史上都算最难的几何问题之一。

- IMO 2014 P3：凸四边形ABCD中，$\angle ABC = \angle CDA = 90 \degree$ 。点H是A到BD的垂足。点S和T分别在边AB和AD上，且H在 $\triangle SCT$ 内部，并且$\angle CHS − \angle CSB = 90 \degree, \angle THC − \angle DTC = 90 \degree$。证明：直线BD是 $\triangle TSH$ 的外接圆的切线。

让作者团队惊讶的是，AG成功证明了一个更加一般化的结论：$OH \perp BD$，也就意味着，结合原问题中 $H \in BD$ 这个条件，$\triangle HST$ 的外接圆于BD相切。

为此，AG构造了点 $E, F, G, I$，它们分别是S关于OH的对称点、H关于AT的对称点、H关于AS的对称点、以及H关于ST的对称点。由于给定条件$\angle CHS - \angle CSB = 90^{\circ}$，$\angle THC - \angle DTC = 90^{\circ}$，意味着 $\triangle CHS$、$\triangle CHT$ 的外心分别在 $AB$、$AD$ 上，所以构造点 $F$ 和 $G$ 会形成圆内接四边形 $CHSG$、$CHTF$，这将有助于进行角度的推导和计算。

更进一步的，点E和点I的构造创造了以S为圆心的循环四边形HGIE，以及点C, T现在分别成了 $\triangle FHI$ 和 $\triangle FGI$ 的外心。将这些结论结合起来，AG就得到了一个非常意想不到的角度跟踪证明，相比于那些常用的，比如使用比例追踪（有可能需要结合阿波罗尼斯圆的知识）、三角几何学、或反演的方法，这些都是大多数人类竞赛者会使用的。这就展示出AG有能力通过只是用一个简单的推理引擎，就能求解非常难的问题。

## IMOSL 2009 G7

最后一个例子是G7问题，来自于IMO 2009 Shortlist。

- IMOSL 2009 G7：设 $\triangle ABC$ 是一个内心为 $I$ 的三角形，设$X$、$Y$和$Z$分别是 $\triangle BIC$、$\triangle CIA$ 和 $\triangle AIB$ 的内心。设 $\triangle XYZ$ 是等边三角形。证明：$\triangle ABC$ 也是等边三角形。

据作者所知，该问题在此之前只有计算解法，例如：通过使用复数、三角函数计算法(trigonometric computations)、或者利用不等式论证的反证法来求解。由于AG无法使用这些计算和推理工具，也无法运用高深的欧几里得几何知识，作者原本以为AG无法解决这道问题。然而，AG通过构建关键的辅助构造，仅利用角度和比例推算，就得出了一个精妙的解法。

首先，AG表明：X和Z是关于BI相互对称的，根据对称性可知：I是 $\triangle XYZ$ 的外心。由此，我们可以证明：AB=AC，再根据对称性可知：$\triangle ABC$ 是一个等边三角形。然而，解决这个问题的主要难点在于，利用 $\triangle XYZ$ 是等边三角形这一条件，即 $XY = YZ$ 及其循环等价形式。为此，AG构造了一系列关键三角形的外心：

- $D$ 作为 $\triangle BXC$ 的外心；$E$ 作为 $\triangle AYZ$ 的外心；$X_1$ 作为 $\triangle BIX$ 的外心；$X_2$ 作为 $\triangle AIY$ 的外心；$X_3$ 作为 $\triangle CIX$ 的外心；$X_4$ 作为 $\triangle ABZ$ 的外心；$X_5$ 作为 $\triangle ACY$ 的外心；$X_6$ 作为 $\triangle AXZ$ 的外心（后续作者会展示 $A, C, X, Z$ 是共圆的）；$X_7$ 作为 $I$ 关于 $BZ$ 的对称点；$X_8$ 作为 $\triangle AXY$ 的外心（后续作者会展示$A, B, X, Y$ 是共圆的）；
- $X_9, X_{10}$ 是令 $\triangle IZX_{9}, \triangle IZX_{10}$ 成为等边三角形的点；
- $X_{11}$ 作为 $Z$ 关于 $BI$ 的对称点（该点可以用Section 4.1中描述的点替代方法证明，该点与点 $X$ 是等价的）。

起先，这些构造看起来非常的反直觉，因为大多数人类并不会构造这么多的点。考虑到点 $X, Y, Z$ 的特性，以及这些点整体所构成的特定构型，与之相关的几何性质并不多，这使得人类很难相处一个综合性的解决方案。然而，这些外心的构造有助于形成一对对全等或相似的三角形，从而使得AG能够利用 $\triangle XYZ$ 是等边三角形这一事实，来求解问题。

所有这些例子都表明：AG在构造辅助点方面非常高效，并且其能够在不使用高度复杂的欧几里得几何知识和方法的情况下，为难题提供相当优雅的解决方案。因此，它能得出人类通常想象不到的创新且高效的解法。
