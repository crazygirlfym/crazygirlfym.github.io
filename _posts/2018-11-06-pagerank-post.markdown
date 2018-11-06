---
layout: post
title: 关于为什么pagerank能收敛
date: 2018-11-06 15:32:24.000000000 +09:00
tags: machine learning 
---

## pagerank的主要思想

 - 如果一个网页被很多其他网页链接到的话说明这个网页比较重要，也就是PageRank值会相对较高
 - 如果一个PageRank值很高的网页链接到一个其他的网页，那么被链接到的网页的PageRank值会相应地因此而提高
 
-------------------

## 算法原理

![这里写图片描述](https://img-blog.csdn.net/20180506223511896?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3VzdGJmeW0=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

PageRank算法是互联网中的众多网页看做是一个有向图，如上图所示。算法过程是把网页预先给网页一个PR值（一般取$\frac{1}{N}$, N 为网页总数）。然后通过以下公式不断更新PR值，直到收敛：

$$PR(p_i) = \alpha \sum _{p_j \in M_{p_i}} \frac{PR(p_j)}{L(p_j)}  + \frac{1-\alpha}{N}$$

其中$M_{p_i}$ 是所有对$p_i$网页有出链的网页集合， $L(p_j)$是$p_j$的出链数目， $alpha=0.85$， 从以上公式来看，若$M_{p_i}$的PR值越大，$p_i$越大， 同时$M_{p_i}$ 中的网页， 出链数目越少，影响力越大。

为了解决有些网页不被链接的情况，引入了『teleporting』, 所谓『teleporting』就是我们认为在任何一个页面浏览的用户都有可能以一个极小的概率瞬间转移道另外一个随机页面。当然，这个页面之前可能不存在超链接，因此不可能直接转移过去，只是为了算法需要。即$\frac{1-\alpha}{ N}$


---------


##为什么能收敛？

然后需要回到我们的标题，为什么能收敛的问题。需要转换为Markov过程。

**如何转换为Markov过程**

取$e$为所有分量都为1的列向量，接着定义矩阵：
$A = \alpha S + \frac{1- \alpha }{N} e e^T$, 则PR的计算过程可以转化为

$P_{n+1} = A P_n$ , 于是该过程就转为了一个Markov过程了。


下面要证明pagerank收敛，即证明其满足Markov过程收敛的条件：

 - A为随机矩阵(**A矩阵所有元素都大于等于0，并且每一列的元素和都为1**)
 - A为不可约的(**当图是强连通时，A为不可约，我们之前定义各个网页都是可相互转跳的**)
 - A为非周期的
 
以上条件均满足，所以pagerank是收敛的，且与初始值无关。

