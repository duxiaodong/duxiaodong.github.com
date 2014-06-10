---
layout: post
title: 算法导论笔记(3)--数据结构--堆、队列、链表
description: ""
category: "alogorithms"
tags: [data structures，alogorithms]
---
{% include JB/setup %}

##Index
* [二叉堆(binary heap)](#binary heap)
* [优先队列(priority queue)](#priority queue)
* [栈(stack)和队列(queue)](#stack and queue)
    * [栈(stack)](#stack)
    * [队列(queue)](#queue)
* [链表(linked list)](#linked list)

## <span id="binary heap">二叉堆(binary heap)</span>

二叉堆是一个数组，它可以被看成一个近似的完全二叉树。树上的每一个结点对应数组中的一个元素。除了最底层外，该树是充满的，而且是从左到右填充。表示堆的数组A包括两个属性：A.length通常给出数组元素的个数，A.heap-size表示有多少个堆元素存储在该数组中。也就是说，A[1..A.length]可能都存有数据，但只有A[1..A.heap-size]中存放的是堆的有效元素。这里0<=A.heap-size<=A.length。树的根节点是A[1]，这样给定一个节点的下标i，我们很容易计算得到它的父结点、左孩子和右孩子的下标：

- PARENT(i) return ⌊i/2⌋
- LEFT(i) return 2i
- RIGHT(i) return 2i+1

![](/images/algo2-7.png)

二叉堆可以分为两种形式：最大堆和最小堆。在最大堆中，除了根以外的所有结点i都要满足：A[PARENT(i)]>=A[i]。在最小堆中，除了根以外的所有结点i都要满足：A[PARENT(i)]<=A[i]。最大堆中最大元素存放在根结点中，最小堆中最小元素存放在根结点中。

###维护堆的性质

MAX-HEAPIFY是用于维护最大堆性质的重要过程。它的输入为一个数组A和一个下标i，在调用MAX-HEAPIFY的时候，我们假定假定根节点为LEFT(i)和RIGHT(i)的二叉树都是最大堆，但这时A[i]有可能小于其孩子，这样就违背了最大堆的性质。MAX-HEAPIFY通过让A[i]的值在最大堆中”逐级下降“，从而使得下标i为根结点的子树重新遵循最大堆的性质。

伪代码：

![](/images/algo2-8.png)

下图显示了MAX-HEAPIFY的执行过程：

![](/images/algo2-9.png)

因为每个孩子的子树的大小最多为2n/3(最坏情况发生在树的最底层恰好半满的时候)，我们可以用下面的这个递归是刻画MAX-HEAPIFY的运行时间：

`T(N)<=T(2n/3)+Θ(1)`

根据主定理，上述递归式的解为T(n)=O(lgn)

###建堆

我们可以用自底向上的方法利用过程MAX-HEAPIFY把一个大小为n=A.length的数组转换为最大堆。由于子数组A(⌊n/2⌋+1..n)中的元素都是树的叶结点，每个叶结点都可以看成只包含一个元素的堆(即认为叶结点满足堆的性质)。过程BUILD-MAX-HEAP对树中的其他结点都调用一次MAX-HEAPIFY。

伪代码：

![](/images/algo2-10.png)

下图给出了BUILD-MAX-HEAP过程的一个例子。

![](/images/algo2-11.png)

BUILD-MAX-HEAP的时间复杂度为O(n)

## <span id="priority queue">优先队列(priority queue)</span>

优先队列是一种用来维护由一组元素构成的集合S的数据结构，其中的没一个元素都有一个相关的值，被成为关键字(key)，一个最大优先队列支持以下操作：

- INSERT(S,x)：把元素x插入集合S中。这一操作等价与S=S∪{x}。
- MAXIMUM(S)：返回S中具有最大键值的元素
- EXTRACT-MAX(S)：去掉并返回S中的具有最大键字的元素。
- INCREASE-KEY（S,x,k)：将元素x的关键字值增加到k，这里设置k的值不小于x的原关键字值

优先队列可以用堆来实现，现在，我们来讨论如何实现最大有限队列的操作。过程HEAP-MAXIMUM可以在Θ(1)时间内实现MAXIMUM操作。

HEAP-MAXIMUM（A）
 return A[1]

过程HEAP-EXTRACT-MAX实现EXTRACT-MAX操作

![](/images/algo3-1.png)

HEAP-EXTRACT-MAX的时间复杂度为O(lgn)。因为除了时间复杂度为O(lgn)的MAX-HEAPIFY以外，其他的操作都是常数阶的。

HEAP-INCREASE-KEY能够实现INCREASE-KEY操作。在优先队列中，我们希望增加关键字的优先队列元素由对应的数组下标i来标识。这一操作需要首先将A[i]的关键字更新为新值。因为增大A[i]的关键字可能会违反最大堆的性质，所以上述操作会采用类似于INSERTION-SORT中插入循环的方式，在从当前节点到根节点的路径上，为新增的关键字寻找恰当的插入位置。在HEAP-INCREASE-KEY的操作过程中，当前元素会不断地与其父节点进行比较，如果当前元素的关键字较大，则当前元素与其父结点进行交换。这一过程会不断重复，直到当前元素的关键字小于其父结点时终止，因为此时已经重新符合了最大堆的性质。

![](/images/algo3-2.png)

下图显示了HEAP-INCREASE-KEY的一个操作过程。在包含n个元素的堆上，HEAP-INCREASE-KEY的时间复杂度是O(lgn)。这是因为算法在第3行做了关键字更新的节点到根结点的路径长度为O(lgn)。

![](/images/algo3-3.png)

MAX-HEAP-INSERT能够实现INSERT操作。它的输入是要被插入到最大堆A中的新元素的关键字。MAX-HEAP-INSERT首先通过增加一个关键字为-∞的叶节点来扩展最大堆。然后调用HEAP-INCREASE-INSERT为新节点设置对应的关键字，同时保持最大堆的性质。

![](/images/algo3-4.png)


在包含n个元素的堆上，MAX-HEAP-INSERT的运行时间为O(lgn)

总之，在一个包含n个元素的堆中，所有优先队列的操作都可以在O(lgn)时间内完成。

## <span id="stack and queue">栈(stack)和队列(queue)</span>

栈和队列都是动态集合，且在其上运行DELETE操作所移除的元素都是预先设定的。在栈中，被删除的是最新插入的元素：栈实现的是一种后进先出(Last in first out，LIFO)策略。类似地，在队列(queue)中，被删去的总是集合中存在时间最长的那个元素：队列实现的是一种(first in first out，FIFO)策略。

### <span id="stack">栈(stack)</span>

栈上的INSERT操作被成为压入(PUSH)，而无参数的DELETE操作称为弹出(POP)。下图显示了用一个数组S[1..n]来实现一个最多可容纳n个元素的栈。该数组有一个属性S.top，指向最新插入的元素。栈中包含的元素为S[1..S.top],其中S[1]是栈底元素，而S[S.top]是栈顶元素，

![](/images/algo3-5.png)

只有出现在浅灰色格子里的才是栈内元素。当S.top=0时，栈内不包含任何元素，即栈是空(empty)的。要测试一个栈是否为空可以用查询操作STACK-EMPTY。如果试图对一个空栈执行弹出操作，则称栈下溢(underflow),这通常是一个错误。如果S.top超过了n,则称为栈上溢(overflow)

栈的几种操作只需几行代码来实现

![](/images/algo3-6.png)

三种栈操作的执行时间都为O(1)

### <span id="queue">队列(queue)</span>

队列上的INSERET操作称为入队(ENQUEUE )，DELETE操作称为出队(DEQUEUE);和栈的POP操作一样，DEQUEUE操作也没有元素参数。

下图表明了利用数组Q[1..n]来实现一个最多容纳n-1个元素的队列的一种方案。该队列有一个属性Q.head指向队头元素，而属性Q.tail则指向下一个新元素将要插入的位置。当Q.head=Q.tail时，队列为空。初始时，有Q.head=Q.tail=1。

在下列伪代码中，假设n=Q.length

![](/images/algo3-7.png)

两种操作的执行时间都为O(1)

## <span id="linked list">链表(linked list)</span>

链表是一种这样的数据结构，其中的各对象按线性顺序排列。链表的顺序是有各个对象里的指针决定的。

如下图所示：双向链表L的每个元素都是一个对象，每个对象有一个关键字key和两个指针：next和prev。对象还可以包含其他的辅助数据(或称卫星数据)。设x为链表的一个元素，则x.next指向它在链表中的后继元素，x.prev指向它的前驱元素。如果x.prev=NIL，则元素x没有前驱，因此是链表的第一个元素如果x.next=NIL，则元素x没有后继，因此是链表的最后一个元素，即链表的尾(tail)

![](/images/algo3-8.png)

链表可以有多种形式，可以是单链接或双链接的，可以是已排序的或未排序的

- 单链接：省略每个元素中的prev指针。
- 已排序：链表的线性顺序与链表元素中关键字的线性顺序一致,因此，最小元素就是表头元素，最大元素则是表尾元素
- 循环链表：表头元素的prev指针指向表尾元素，表尾元素的next指针指向表头元素

###链表的搜索

过程LIST-SEARCH(L,k)采用简单的线性搜索方法，用于查找链表L中第一个关键字为k的元素，并返回指向该元素的指针。如果链表中没有关键子为k的对象，则返回NIL。对于上图，调用LIST-SEARCH(L,4)返回指向第三个元素的指针，而调用LIST-SEARCH(L,7)返回NIL

![](/images/algo3-9.png)

要搜索一个有n个对象的链表，过程LIST-SEARCH在最坏情况下的运行时间为Θ(n)，因为可能需要搜索整个链表

###链表的插入

给定一个已设置好关键字key的元素x，过程LIST-INSERT将x"连接入"链表的前端

![](/images/algo3-10.png)

在一个含n个元素的链表上执行LIST-INSERT的运行时间是O(1)

###链表的删除

过程LIST-DELETE将一个元素从链表L中移除。该过程要求给定一个指向x的指针，然后通过修改一些指针，将x"删除出"该链表。如果要删除具有给定关键字值的元素，则必须先调用LIST-SEARCH找到该元素

![](/images/algo3-11.png)

###哨兵

哨兵是一个哑对象，其作用是简化边界条件的处理。假设在链表L中设置一个对象L.nil,该对象代表NIL，但也具有和其他对象相同的各个属性。对于链表代码中出现的每一处对NIL的引用，都代之以哨兵L.nil的引用。这样的调整将一个常规的双向链表变为一个有哨兵的双向循环链表，哨兵L.nil位于表头和表尾之间。属性L.nil.next指向表头，L.nil.prev指向表尾。类似地，表尾的next属性和表头的prev属性同时指向L.nil。因为L.nil.next指向表头，我们就可以去掉属性L.head，并把对它的引用代替为L.nil.next的引用。

![](/images/algo3-12.png)

![](/images/algo3-13.png)

![](/images/algo3-14.png)

哨兵基本不能降低数据结构相关操作的渐进时间界，但可以降低常数因子。
