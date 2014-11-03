---
layout: post
title: 算法导论笔记(5)--基本数据结构，二叉搜索树
description: ""
category: "alogorithms"
tags: [hash table，alogorithms]
---
{% include JB/setup %}

一棵二叉搜索树是一颗二叉树来组织的，如下图所示。这样一棵树可以使用一个链表数据结构来表示，其中每个结点就是一个对象。除了key和卫星数据之外，每个结点还包含属性 left、right和p，它们分别指向结点的左孩子、右孩子和双亲。如果某个孩子结点和父结点不存在，则相应属性为NIL。根结点是数中唯一父指针为NIL的结点。

![](/images/algo5-1.png)

> 二叉搜索树中的关键字总是以满足二叉搜索树性质的方式来存储：
    
> 设x是二叉搜索树中的一个结点。如果y是x的左子树中的一个结点，那么y.key ≦ x.key。如果y是x右子树中的一个结点，那么y.key ≧ x.key

二叉搜索树性质允许我们通过一个简单的递归算法来按序输出二叉搜索树中的所有关键字，这种算法称为中序遍历 (inorder tree walk)算法，这样命名的原因是输出的子树根的关键字位于其左子树和右子树的关键字之间。

类似的，先序遍历（preorder tree walk）中输出的根的关键字在其左右子树的关键字值之前，而后序遍历(postorder tree walk)输出的根的关键字在其左右子树的关键字值之后。）调用下面的过程INORDER-TREE-WALK(T.root)，就可以输出一颗搜索树T中的所有元素。

![](/images/algo5-2.png)

上图中的两颗二叉搜索树，中序遍历输出的关键字顺序均为2，5，5，6，7，8。

> 定理：如果x是一颗有n个结点子树的根，那么调用INORDER_TREE_WALK(x)需要Θ(n)时间

###查询二叉搜索树

我们经常需要查找一个存储在二叉搜索树中的关键字。除了search操作之外，二叉搜索树还能支持诸如MINIMUM、MAXIMUM、SUCCESSOR、PREDECESSOR的查询操作。

####查找

输入一个指向树根的指针和一个关键字k，如果这个结点存在，TREE_SEARCH返回一个指向关键字为k的结点的指针，否则返回NIL。

![](/images/algo5-3.png)

>分析：
    这个过程从树根开始查找，并沿着这棵树中的一条简单路径向下进行。对于遇到的每个结点x，比较关键字k与x.key。如果两个关键字相等，查找就终止。如果看小于x.key，查找在x的左子树中继续，因为二叉搜索树性质蕴涵了k不可能存储在右子树中。对称地，如果k大于x.key，查找在右子树中继续。从树根开始递归期间遇到地结点就形成了一条向下的简单路径,所以TREE-SEARCH的运行时间为O(h)，其中h是这棵树的高度。

![](/images/algo5-4.png)

> 示例：为了找到关键字为13的结点，从树根开始沿着15--6--7--13路径进行查找。

也可以采用while循环来展开递归，用一种迭代方式重写这个过程。

![](/images/algo5-5.png)

####最大关键字元素和最小关键字元素

通过从树根开始沿着left孩子指针直到遇到一个NIL,我们总能在一颗二叉搜索数中找到一个最小的元素，下面的过程返回了一个指向在以给定结点x为根的子树中的最小元素的指针。

![](/images/algo5-6.png)

TREEMAXIMUM的伪代码是对称的，如下：

![](/images/algo5-7.png)

这两个过程在一棵高度为h的树上均能在O(h)时间内执行完，因为与TREE-SEARCH一样，它们所遇到的结点均形成了一条从树根向下的简单路径

####后继和前驱

如果一棵树的所有关键字互不相同，则一个结点x的后继是大于x.key的最小关键字的结点。如果后继存在，下面的过程将返回一颗二叉搜索数中的结点x的后继；如果x是这棵数中的最大关键字，则返回NIL。

![](/images/algo5-8.png)

> 分析：
    1.如果结点x的右子树非空，那么x的后继恰是x右子树中的最左结点，通过第2行中的TREE-MINIMUM（x.right）调用可以找到。
    2.如果结点x的右子树为空且右一个后继y，那么y就是x的有左孩子的最底层祖先，即y的左子树中包含x结点。此时y的左子树中x为最大结点，而y的右子树(若有右子树)的结点均大于y，y大于x，所以y为x的后继，

---

> 定理：在一棵高度为h的二叉搜索树上，动态集合上的操作SEARCH、MINIMUM、MAXIMUM、SUCCESSOR和PREDECSSOR可以在O(h)时间内完成

###插入和删除

插入和删除操作会引起二叉搜索树表示的动态集合的变化，一定要修改数据结构来反映这个变化，但修改要保持二叉搜索树性质的成立。

####插入

要将一个新值v插入到一棵二叉搜索树T中，需要调用过程TREE-INSERT。其中z.key = v，z.left = NIL，z.right = NIL。这个过程要修改T和z的某些属性。

![](/images/algo5-9.png)

下图显示了TREE-INSERT是如何工作的

![](/images/algo5-10.png)

> 分析：
    TREE-SEARCH从树根开始，指针x记录了一条向下的简单路径，并查找要替换的输入项z的NIL。该过程保持遍历指针(trailing pointer)y作为x的双亲。初始化后，3-7行的while循环使得这两个指针沿树向下移动，向左或向右移动取决于z.key和x.key的比较，直到x变为NIL。这个NIL占据的位置就是输入想z要放置的地方。我们需要遍历指针y，这是因为找到NIL时要直到z属于哪个结点。8-13行设置相应的指针，使得z插入其中。
    过程TREE-INSERT在一棵高度为h的树上的运行时间为O(h)。
    
####删除

从一棵二叉搜索树T中删除一个结点z的整个策略分为三种情况

> - z没有孩子
- z只有一个孩子
- z右两个孩子

对于没有孩子和只有一个孩子的情况，比较简单，有一个孩子的可以使用孩子代替z，一个孩子都没有的则用NIL。有两个孩子的情况下，我们必须找到z在右子树中的后继来代替z。

为了在二叉搜索树内移动子树，定义一个子过程TRANSPLANT,它是用另外一棵子树替换一棵子树成为其双亲的孩子结点。当TRANSPLANT用一棵以v为根的子树替换一棵以u为根的子树时，结点u的双亲就变为结点v的双亲，并且最后v成为u 的双亲的相应孩子。

![](/images/algo5-11.png)

利用现成的TRANSPLANT过程，下面时从二叉搜索树T中删除结点z的删除过程：

![](/images/algo5-12.png)

关于二叉搜索树，在给定的一列的元素构造二叉树，即使这列元素时有序的，那么将构造成一根“棍子”，为了避免这种最坏情况的发生，可以采用随机构造二叉树的方法。