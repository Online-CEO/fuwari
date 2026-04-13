---
title: Markdown Example
published: 2023-10-01
description: A simple example of a Markdown blog post.
tags: [Markdown, Blogging, Demo]
category: Examples
draft: false
---

# An h1 header

Paragraphs are separated by a blank line.

2nd paragraph. _Italic_, **bold**, and `monospace`. Itemized lists
look like:

- this one
- that one
- the other one

Note that --- not considering the asterisk --- the actual text
content starts at 4-columns in.

> Block quotes are
> written like so.
>
> They can span multiple paragraphs,
> if you like.

Use 3 dashes for an em-dash. Use 2 dashes for ranges (ex., "it's all
in chapters 12--14"). Three dots ... will be converted to an ellipsis.
Unicode is supported. ☺

## An h2 header

Here's a numbered list:

1. first item
2. second item
3. third item

Note again how the actual text starts at 4 columns in (4 characters
from the left side). Here's a code sample:

    # Let me re-iterate ...
    for i in 1 .. 10 { do-something(i) }

As you probably guessed, indented 4 spaces. By the way, instead of
indenting the block, you can use delimited blocks, if you like:

```
define foobar() {
    print "Welcome to flavor country!";
}
```

(which makes copying & pasting easier). You can optionally mark the
delimited block for Pandoc to syntax highlight it:

```python
import time
# Quick, count to ten!
for i in range(10):
    # (but not *too* quick)
    time.sleep(0.5)
    print i
```

### An h3 header

Now a nested list:

1. First, get these ingredients:

    - carrots
    - celery
    - lentils

2. Boil some water.

3. Dump everything in the pot and follow
    this algorithm:

        find wooden spoon
        uncover pot
        stir
        cover pot
        balance wooden spoon precariously on pot handle
        wait 10 minutes
        goto first step (or shut off burner when done)

    Do not bump wooden spoon or it will fall.

Notice again how text always lines up on 4-space indents (including
that last line which continues item 3 above).

Here's a link to [a website](http://foo.bar), to a [local
doc](local-doc.html), and to a [section heading in the current
doc](#an-h2-header). Here's a footnote [^1].

[^1]: Footnote text goes here.

Tables can look like this:

size material color

---

9 leather brown
10 hemp canvas natural
11 glass transparent

Table: Shoes, their sizes, and what they're made of

(The above is the caption for the table.) Pandoc also supports
multi-line tables:

---

keyword text

---

red Sunsets, apples, and
other red or reddish
things.

green Leaves, grass, frogs
and other things it's
not easy being.

---

A horizontal rule follows.

---

Here's a definition list:

apples
: Good for making applesauce.
oranges
: Citrus!
tomatoes
: There's no "e" in tomatoe.

Again, text is indented 4 spaces. (Put a blank line between each
term/definition pair to spread things out more.)

Here's a "line block":

| Line one
| Line too
| Line tree

and images can be specified like so:

[//]: # (![example image]&#40;./demo-banner.png "An exemplary image"&#41;)

Inline math equations go in like so: $\omega = d\phi / dt$. Display
math should get its own line and be put in in double-dollarsigns:

$$I = \int \rho R^{2} dV$$

$$
\begin{equation*}
\pi
=3.1415926535
 \;8979323846\;2643383279\;5028841971\;6939937510\;5820974944
 \;5923078164\;0628620899\;8628034825\;3421170679\;\ldots
\end{equation*}
$$

And note that you can backslash-escape any punctuation characters
which you wish to be displayed literally, ex.: \`foo\`, \*bar\*, etc.

---


# **一级标题**

段落之间用空行分隔。

第二个段落。*斜体*、**粗体**和`等宽字体`。项目符号列表的写法如下：

- 这一项
- 那一项
- 另一项

请注意——不考虑星号——实际文本内容从第 4 列开始（即左侧缩进 4 个字符）。

> 块引用是这样写的。
> 
> 
> 如果你愿意，它们可以跨越多个段落。
> 

使用 3 个短横线表示破折号（em-dash）。使用 2 个短横线表示范围（例如，"都在第 12--14 章"）。三个点 ... 会被转换为省略号。支持 Unicode 字符。☺

## **二级标题**

这是一个有序列表：

1. 第一项
2. 第二项
3. 第三项

再次注意，实际文本从第 4 列开始（即左侧缩进 4 个字符）。下面是一个代码示例：

```
# 让我再重复一遍……
for i in 1 .. 10 { do-something(i) }
```

正如你可能猜到的，需要缩进 4 个空格。顺便说一句，如果你愿意，也可以使用分隔块来代替缩进：

```
define foobar() {
    print "Welcome to flavor country!";
}
```

（这样更方便复制和粘贴）。你可以选择为分隔块标记语言类型，以便 Pandoc 对其进行语法高亮：

```
import time
# 快，数到十！
for i in range(10):
    # （但别*太*快）
    time.sleep(0.5)
    print i
```

### **三级标题**

现在是一个嵌套列表：

1. 首先，准备这些食材：
    - 胡萝卜
    - 芹菜
    - 扁豆
2. 烧开一些水。
3. 把所有东西倒进锅里，然后按照以下算法操作：不要碰到木勺，否则它会掉下来。
    
    ```
     找到木勺
     揭开锅盖
     搅拌
     盖上锅盖
     将木勺小心翼翼地架在锅柄上
     等待 10 分钟
     回到第一步（或完成后关闭炉灶）
    ```
    

再次注意，文本始终对齐到 4 空格缩进（包括上面延续第 3 项的最后一行）。

下面是一个链接示例：[一个网站](http://upsubs.com)、[本地文档](local-doc.html)，以及[当前文档中的章节标题](#an-h2-header)。这是一个脚注标记 [^1]。


表格可以这样写：

尺寸 材质 颜色

---

9 皮革 棕色
10 麻布 本色
11 玻璃 透明

表：鞋子、它们的尺寸以及制作材料

（以上是表格的标题。）Pandoc 也支持多行表格：

---

关键词 描述

---

red 日落、苹果，以及
其他红色或偏红的
事物。

green 树叶、草地、青蛙，
以及其他"做绿不易"
的事物。

---

下面是一条水平分隔线。

---

下面是一个定义列表：

apples（苹果）
: 适合制作苹果酱。
oranges（橙子）
: 柑橘类水果！
tomatoes（番茄）
: "tomatoe"里没有字母"e"。

再次提醒，文本需要缩进 4 个空格。（在每个术语/定义对之间添加空行，可以让排版更疏朗。）

下面是一个"行块"示例：

| 第一行
| 第二行
| 第三行

图片可以这样指定：

[//]: # (![example image]&#40;./demo-banner.png "An exemplary image"&#41;)

行内数学公式这样写：ω=dϕ/dt*ω*=*dϕ*/*dt*。独立显示的数学公式应独占一行，并用双美元符号包裹：

$$I = \int \rho R^{2} dV$$

$$
\begin{equation*}
\pi
=3.1415926535
 \;8979323846\;2643383279\;5028841971\;6939937510\;5820974944
 \;5923078164\;0628620899\;8628034825\;3421170679\;\ldots
\end{equation*}
$$

请注意，你可以使用反斜杠转义任何希望原样显示的标点字符，例如：`foo`、*bar* 等。