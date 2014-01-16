---
layout: post
title: Git使用经验:Submodules,Subtree
description: ""
category: "tools"
tags: [git, submodule, subtree]
---
{% include JB/setup %}

##git submodules

1.何时使用Submodules

比如说你有一个Git repo（repository）叫做project,然后你的project 需要用到另一个git repo 叫做 lib.git的submodule就能管理你的子repo,也就是在project内clone lib,然后你能管理,维护,就像操作普通git repo一样.

2.submodules缺点:

* 难以理解
* 难以使用
* 命令复杂

3.git submodules实践

git submodules的使用比较复杂,懒得写博文了,把去年学习git submodules时看的博文推荐给大家吧:

Git Submodules使用完整教程:http://www.kafeitu.me/git/2012/03/27/git-submodule.html

##git subtree

1.为什么要使用git subtree

* 管理和更新流程比较方便
* git v1.5.2以后建议使用git subtree(git v.1.7.11才合并进来)
* 仓库clone 下来不需要init和update
* 不会产生像.gitmodule类似的文件
* git submodule 删除起来比较费劲
* 另外还有一些团队协作时的尴尬问题

2.git subtree 常用命令

    'git subtree' add --prefix=&lt;prefix&gt; &lt;commit&gt;
    'git subtree' pull --prefix=&lt;prefix&gt; &lt;repository&gt; &lt;refspec...&gt;
    'git subtree' push --prefix=&lt;prefix&gt; &lt;repository&gt; &lt;refspec...&gt;
    'git subtree' merge --prefix=&lt;prefix&gt; &lt;commit&gt;
    'git subtree' split --prefix=&lt;prefix&gt; [OPTIONS] [&lt;commit&gt;]

完整的使用说明,可以参考其[官方文档](https://github.com/git/git/blob/master/contrib/subtree/git-subtree.txt)

3.git subtree使用实例

初始化

    $ git init subtree-test
    $ cd subtree-test
    $ touch README
    $ git add .
    $ git commit -m "add first file"

向subtree-test中添加子仓库:

git subtree add --preifx=子目录名称 仓库地址 分支 --squash

--squash 代表将子repo的所有commit都挤成一个commit
    
    $ git subtree add --prefix=vim git@github.com:Junevimer/vim.cfg.git master --squash
    
远程子仓库被修改时,我们需要更新子仓库:

git subtree pull --prefix=子目录名称 仓库地址 分支 --squash

    $ git subtree pull --prefix=vim git@github.com:Junevimer/vim.cfg.git master --squash

4.用remote name 减少命令输入量

有没有发现到需要输入的命令都很长？

现在将testing 的 pull/push URL 加入remote：

    $ git remote add test git@github.com:Junevimer/vim.cfg.git

加入subtree的命令就成了：

    $ git subtree add --prefix=vim test master --squash 

subtree pull 变成：

    $ git subtree pull --prefix=vim test master --squash

5.在subtree-test下更新vim，再push上

在subtree-test下更新完vim以后,想要push到远程子仓库,使用命令:

    $ git subtree push --prefix=vim test master 

6.--squash参数

关于squash参数,可以查看[Git subtree 要不要使用 –squash 参数](http://www.fwolf.com/blog/category/blog)
