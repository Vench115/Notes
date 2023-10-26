# 网站

[MarkDown语法 超详细教程 - 经验分享 - Obsidian 中文论坛](https://forum-zh.obsidian.md/t/topic/435)

[Markdown超级教程 Obsidian版 - 秃秃的小屋 - Obsidian Publish](https://publish.obsidian.md/csj-obsidian/0+-+Obsidian/Markdown/Markdown%E8%B6%85%E7%BA%A7%E6%95%99%E7%A8%8B+Obsidian%E7%89%88)

# 一级标题
>引用格式，挺好
>
>> Good
>
> 引用套引用

# 网页

网页如何显示呢？

# Todo List
- [ ] Todo list
- [ ] 待办事项
- [ ] 可以打勾

# 表格

|这里是表头1|这里是表头2|这里是表头3| 
|:--------|:---------:|---------:|  
|单元格数据1|单元格数据2|单元格数据3|
|单元格数据4|单元格数据5|单元格数据6|

不同的`:-:`组合对应不同的对齐方式

# 代码块

```python
print("Have a Great Day!")
```

# 文内注释

鲁迅原名叫什么[^1]


[^1]: 这是一个注解，鲁迅原名周樟寿，后改名周树人

# Callouts 着重强调
>[!note]
>callout 
>a note

# 公式

- 格式：
    - **$** + 行内公式 + **$**
    - 主要使用LaTex

- 示例：
    - **`$x^2 + 2x + 5 + \sqrt x = 0$`**
        **`$\ce{CO2 + C -> 2 CO}$`**

- 效果：
	- $x^2 + 2x + 5 \sqrt x = 0$
	- $\ce{CO2 + C -> 2 CO}$‘

- **公式块：**
	- \$\$ + 公式块公式 + \$\$
	-  示例：

```markdown
$$
$\ce{Zn^2+  <=>[+ 2OH-][+ 2H+]  $\underset{\text{amphoteres Hydroxid}}{\ce{Zn(OH)2 v}}$  <=>[+ 2OH-][+ 2H+]  $\underset{\text{Hydroxozikat}}{\ce{[Zn(OH)4]^2-}}$}$
$$

<!-- 麦克斯韦方程组 -->
$$
\begin{array}{lll}
\nabla\times E &=& -\;\frac{\partial{B}}{\partial{t}}   
\ \nabla\times H &=& \frac{\partial{D}}{\partial{t}}+J   
\ \nabla\cdot D &=& \rho
\ \nabla\cdot B &=& 0
\ \end{array}
$$
```

- 实际效果：

$$
$\ce{Zn^2+  <=>[+ 2OH-][+ 2H+]  $\underset{\text{amphoteres Hydroxid}}{\ce{Zn(OH)2 v}}$  <=>[+ 2OH-][+ 2H+]  $\underset{\text{Hydroxozikat}}{\ce{[Zn(OH)4]^2-}}$}$
$$

<!-- 麦克斯韦方程组 -->
$$
\begin{array}{lll}
\nabla\times E &=& -\;\frac{\partial{B}}{\partial{t}}   
\ \nabla\times H &=& \frac{\partial{D}}{\partial{t}}+J   
\ \nabla\cdot D &=& \rho
\ \nabla\cdot B &=& 0
\ \end{array}
$$


# 其他插入格式

- 本地图片直接复制，会在本地仓库中创建一个副本；
- 或者使用\[\[\]\]来直接指出，需要注意的是，这种操作需要文件在同一工作区（文件目录）下。