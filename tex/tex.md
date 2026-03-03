一般用tex做排版写公式或画图



## 安装

安装miktex



texwork



## 在线编辑预览

overleaf：

[Your projects - Overleaf, Online LaTeX Editor](https://www.overleaf.com/project)



## 语法

```tex
\documentclass[11pt]{ctexart} 
\usepackage{algorithm} 
\usepackage{algpseudocode}
\usepackage{amsmath}
\renewcommand{\algorithmicrequire}{ \textbf{Input:}}     %Use Input in the format of Algorithm
\renewcommand{\algorithmicensure}{ \textbf{Output:}}    %UseOutput in the format of Algorithm

\begin{document} 
\begin{algorithm}[t]
\caption{基于动态阈值的双层判定算法}
\begin{algorithmic}[1]
\Require 查询向量 $q$，模板库 $\mathcal{L} = \{(v_j, label_j, model_j)\}$，类别阈值映射表 $\mathcal{T} = \{C_i: \tau_i\}$，召回数量 $K$
\Ensure 识别结果 $Result$ (类型，型号) 或 未知

\State $Result \gets \text{Unknown}$

\For{每个模板 $v_j \in \mathcal{L}$}
    \State 计算相似度 $s_j \gets \cos(q, v_j)$
\EndFor

\State 将 $\mathcal{L}$ 按 $s_j$ 降序排列，选取前 $K$ 个构成候选集 $\mathcal{C}_{top}$

\For{每个候选实例 $(v_j, label_j, model_j) \in \mathcal{C}_{top}$}
    \State 读取该实例所属类别 $label_j$ 的动态阈值 $\tau_{label} \gets \mathcal{T}[label_j]$
    \If{$s_j \geq \tau_{label}$}
        \State $Result \gets (label_j, model_j)$
        \State \textbf{break} \Comment{命中即停止，输出最高相似度的合法结果}
    \EndIf
\EndFor

\State \Return $Result$
\end{algorithmic}
\label{alg:dynamic_threshold}
\end{algorithm}
\end{document} 
```

上述tex代码是描绘一段算法





LaTeX 的文档结构非常灵活，我来详细解释：

### 关于 `\documentclass`

**不是必须的**，但有几种情况：

### 1. 完整文档（需要 `\documentclass`）
```latex
\documentclass{article}  % 必须，定义文档类型
\usepackage{algorithm, algorithmic}  % 算法包

\begin{document}  % 必须
\begin{algorithm}
\caption{My Algorithm}
\begin{algorithmic}
\STATE $x \gets 0$
\end{algorithmic}
\end{algorithm}
\end{document}
```

### 2. **不需要完整文档的情况**

#### 情况 A：片段/子文件（使用 `standalone` 或 `subfiles`）
```latex
% 单独编译一个算法图，不需要完整的 article 结构
\documentclass[border=1pt]{standalone}
\usepackage{algorithm, algorithmic}

\begin{document}
\begin{algorithm}
\caption{My Algorithm}
\begin{algorithmic}
\STATE $x \gets 0$
\end{algorithmic}
\end{algorithm}
\end{document}
```

#### 情况 B：被主文件导入的片段（**不需要 `\documentclass` 和 `\begin{document}`**）
```latex
% 这是一个 .tex 片段，被 \input 或 \include 导入
% 注意：没有 \documentclass，也没有 \begin{document}！

\begin{algorithm}
\caption{My Algorithm}
\begin{algorithmic}
\STATE $x \gets 0$
\end{algorithmic}
\end{algorithm}
```

#### 情况 C：使用 `\input` 导入多个算法
```latex
% main.tex
\documentclass{article}
\usepackage{algorithm, algorithmic}

\begin{document}
\input{algorithm1}  % 导入片段，里面只有 \begin{algorithm}...\end{algorithm}
\input{algorithm2}
\end{document}
```

---

### 关键总结

| 场景                     | 需要 `\documentclass` | 需要 `\begin{document}` | 说明                 |
| ------------------------ | --------------------- | ----------------------- | -------------------- |
| 独立编译的完整文档       | ✅ 必须                | ✅ 必须                  | 正常情况             |
| `standalone` 类文档      | ✅ 必须                | ✅ 必须                  | 但可裁剪到仅显示内容 |
| 被导入的片段（`\input`） | ❌ 不需要              | ❌ 不需要                | 直接写环境即可       |
| 使用 `subfiles` 宏包     | 看情况                | 看情况                  | 可独立编译也可被导入 |

---

### 直接写 `\begin{algorithm}` 的场景

如果你只想写一个算法文件，然后被主文档导入：

**algorithm_snippet.tex:**
```latex
% 这是一个纯片段文件，没有文档类，没有 document 环境
\begin{algorithm}[H]
\centering
\caption{快速排序}
\begin{algorithmic}[1]
\REQUIRE 数组 $A[1..n]$
\ENSURE 排序后的数组 $A$
\IF{$n \leq 1$}
    \RETURN $A$
\ENDIF
\STATE 选择基准元素 $pivot$
\STATE $left \gets$ 小于 $pivot$ 的元素
\STATE $right \gets$ 大于 $pivot$ 的元素
\RETURN $\text{quicksort}(left) + [pivot] + \text{quicksort}(right)$
\end{algorithmic}
\end{algorithm}
```

然后在主文档中：
```latex
\documentclass{article}
\usepackage{algorithm, algorithmic}
\begin{document}
这里是正文内容...

\input{algorithm_snippet}  % 直接插入算法

继续正文...
\end{document}
```

---

### 常见误区澄清

❌ **错误理解**：所有 `.tex` 文件都必须以 `\documentclass` 开头  
✅ **正确理解**：只有**入口文件**（被编译的文件）需要 `\documentclass`，被 `\input` 或 `\include` 导入的片段不需要

所以你的需求完全可以实现——如果算法是作为一个模块被导入到更大的文档中，直接写 `\begin{algorithm}` 是完全正确的做法！



### 文档类型

1.book
自带\chapter
选项：\documentclass[twoside]{book} \documentclass[oneside]{book} twoside会自动设置章节在奇数页， 空页会放this page is intentionally left blank
2.article
3.slides
4.beamer：PPT
5.letters
6.minimal：可用于debug的轻量类
7.CTeX文档类：[用LaTeX写中文（包括支持中文显示和中文数字）-CSDN博客](https://blog.csdn.net/PolarisRisingWar/article/details/139029295)





## 其他

### 导出为pdf

```
# 编译为 PDF
pdflatex document.tex
```



### 导出为svg

```shell
# 方法一
# tex->pdf->svg
pdflatex document.tex

# 转换为 SVG
pdf2svg document.pdf document.svg
-----------------------------
#方法二 （可能缺失字体）
# tex->dvi->svg
# 先编译为 DVI
latex document.tex

# 转换为 SVG
dvisvgm document.dvi
----------------------------
# 或者直接处理
dvisvgm --pdf document.pdf

```

针对字体缺失问题

**解决方案**

```
# 方法 A：将字体转为路径（最兼容，推荐）
latex algorithm.tex
dvisvgm --no-fonts --exact-bbox algorithm.dvi

# 方法 B：使用 WOFF 字体格式（保留文本可选中）
dvisvgm --font-format=woff --exact-bbox algorithm.dvi

# 方法 C：从 PDF 转换（对中文支持更好）
pdflatex algorithm.tex
dvisvgm --pdf --no-fonts --exact-bbox algorithm.pdf
```

- `--no-fonts`：将文字转为矢量路径，避免字体缺失
- `--exact-bbox`：精确计算边界框，防止内容被裁剪
- `--font-format=woff`：使用网页字体格式（如果希望保留文字属性）



### 导出为png

```bash
#dvipng  （）
latex document.tex
dvipng -D 300 -T tight document.dvi -o output.png

----------------------
# pdflatex + ImageMagick/convert   pdf转png
pdflatex document.tex
convert -density 300 document.pdf -quality 90 output.png
----------------------
# pdftoppm pdf转png
pdflatex document.tex
pdftoppm -png -r 300 -singlefile document.pdf output
# 生成 output.png
```

