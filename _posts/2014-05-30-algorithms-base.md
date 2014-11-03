---
layout: post
title: 算法导论笔记(1)--分治策略和递归式求解
description: ""
category: "alogorithms"
tags: [master method,alogorithms]
---
{% include JB/setup %}

##分治法(Divide and Conquer)
许多算法在结构上都是递归的，为了解决一个问题，算法一次或多次的递归调用其自身以解决紧密相关的若干个子问题。这些算法典型地遵循分治法的思想：将原问题分解为几个规模较小但类似于原问题的子问题，递归的求解这些子问题，然后再合并这些子问题的解来建立原问题的解。

分治模式在每次递归时都有三个步骤：

1. 分解原问题为若干子问题，这些子问题是原问题的规模较小的实例
2. 解决这些子问题，递归地求解各子问题。然后，若子问题的规模足够小，则直接求解（即初始情况）
3. 合并这些子问题的解成原问题的解

##分治法分析
我们假设T(n)是规模为n的一个问题的运行时间。假设把原问题分解成a个子问题，每个子问题的规模是原问题的1/b。为了求解一个规模为n/b的子问题，需要T(n/b)的时间，所以需要aT(n/b)的时间来求解a个子问题。如果分解问题成子问题需要时间D(n)，合并子问题的解成原问题的解需要时间C(n)，那么得到递归式：

    T(n) = Θ(1) 若n <= c (c为常数，即在n小于某一值是，T(n)的运行时间为Θ(1))
    T(n) = aT(n/b)+D(n)+C(n) 其他

##递归式求解
在上面的分析中，我们知道，可以采用分值策略来获得利用解决一个问题的运行时间的递归式.

求解递归式，即可得到算法的"Θ"或"O"渐进界。主要有三种方法：

1. 代入法：我们猜测一个界，然后用数学归纳法证明这个界是正确的
2. 递归树法：将递归式转换成一棵树，其结点表示不同层次的递归调用产生的代价。然后采用边界和技术来求解递归式
3. 主方法：可求解形如下面公式的递归式的界：T(n) = aT(n/b)+f(n)，其中a>=1,b>1,f(n)是一个给定的函数。这种形式的递归式很常见，它刻画了这样一个分治算法：生成a个子问题，，每个子问题的规模是原问题规模的1/b，分解和合并步骤总共花费时间为f(n).

###代入法(The substitution method)

1. 猜测解的形式
2. 用数学归纳法求出解中的常数，并证明解是正确的。

`例：T(n) = 2T(⌊n/2⌋)+n`

我们猜测其解为T(n) = O(nlgn),代入法要求证明，恰当选择常数c>0,可有T(n)<=cnlgn.首先假定此上界对所有整数m<n都成立，特别是对于m= ⌊n/2⌋，有T(⌊n/2⌋)<=c⌊n/2⌋lg(⌊n/2⌋).将其带入递归式，得到

![](/images/algo1-0.png)

其中，只要c>=1，最后一步都会成立。

###递归树(The recursion-tree method)

在递归树中，每个节点表示一个单一问题的代价，子问题对应某次递归函数调用。我们将树中每层中的代价求和，得到每层代价，然后将所有层的代价求和，得到所有层次的递归调用的总代价。PS:递归树最适合用来生成好的猜测，然后即可用带入发来检测猜测是否正确.

例：
T(n) = 3T(⌊n/4⌋)+ Θ(n²)

![](/images/algo1-1.png)

上图显示了如何从递归式T(n) = 3T(⌊n/4⌋)+ Θ(n²)构造出递归树。为了方便起见，我们假定n是4的幂，这样所有子问题的规模均为整数。（a)显示了T(n)，他在图(b)中扩展为一棵等价的递归树。根结点中的cn^2项表示递归调用顶层的代价，根的三棵子树表示规模为n/4的子问题所产生的代价。(c)显示了进一步构造递归树的过程，将(b)中代价为T(n/4)的结点展开。我们继续扩展树中每个结点，根据递归式确定的关系将其分解为几个组成部分(孩子结点).

因为子问题的规模每一步减少为上一步的1/4，所以最终必然会达到边界条件。不难分析的出，递归树有log₄ⁿ+1层，深度为0,1,2...,log₄ⁿ。

接下来确定每一层的代价。每层的节点数都是上一层的3倍，因此深度为i的节点数为3ⁱ。因为每一层问题规模都是上一层的1/4，所以对i=0,1,2...,log₄ⁿ-1，深度为i每个每个节点的代价为c(n/4ⁱ)²。做一下乘法可得，对i=0,1,2,...,log₄ⁿ-1,深度为i的所有结点的总代价为3ⁱc(n/4ⁱ)²=(3/16)ⁱcn²。树的最底层深度为log₄ⁿ，有3^(log₄ⁿ)=n^(log₄³)个结点，每个结点的代价为T(1)，总代价为n^(log₄³)T(1)，即Θ(n^(log₄ⁿ)),这里假定T(1)是常量。

现在我们求所有层次的代价之和，确定整棵树的代价：

![](/images/algo1-2.png)

上图最后一步推导用等比数列求和公式即可，回退等比数列求和这一步，我们得到

![](/images/algo1-3.png)

这样，对原始的递归式T(n) = 3T(⌊n/4⌋)+ Θ(n²),我们推导出了一个猜测T(n)=O(n²).最后用代入法验证猜测是否正确。

###主方法(master method)

主方法为如下形式的递归式提供了一种“菜谱”式的求解方法：

`T(n) = aT(n/b)+f(n)`

其中a>=1和b>1是常数,f(n)是渐近正函数。

主方法依赖于下主定理：

注定理：令a>=1和b>1是常数,f(n)是一个函数，T(n)是定义在非负整数上的递归式：
    
`T(n) = aT(n/b)+f(n)`

其中我们将n/b解释为⌊n/b⌋或⌈n/b⌉。那么T(n)有如下渐近界：

![](/images/algo1-4.png)

主方法在常用算法中的应用:

二分查找法：

* 递归式：T(n) = T(n/2)+Θ(1)
* 运行时间：Θ(log(n))
* 备注：case 2

二叉树遍历：

* 递归式：T(n) = 2T(n/2)+Θ(1)
* 运行时间：Θ(n)
* 备注：case 1

归并排序：

* 递归式：T(n) = 2T(n/2)+Θ(n)
* 运行时间：Θ(nlog(n))
* 备注：case 2

这里我们仅关注递归式和运行时间，以及它们对应主方法的中情形。算法将在后续章节中介绍

本文主要记录分治策略和求解递归式的三种方法，其中主方法最简单，应用也最多。在适合使用主方法的地方应该尽可能的使用主方法来求解递归式。代入法能够处理主方法求解不了的递归式，需要猜测一个合理的上界。而递归树则最合适用来生成这个好的猜测。

last update:2014-05-31 13:54