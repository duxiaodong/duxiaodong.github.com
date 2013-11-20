---
layout: post
title: Ubuntu12.04下Vim配置之不折腾版
description: ""
category: Vim
tags: [vim, ubuntu, Linux]
---
{% include JB/setup %}

##写在前面

用vim大概也有六个月了吧，用原生的大概一个月，后面各种折腾，这是第3次折腾以后的最终版本了，写篇博客记录下。

##Vim基本用法

推荐：耗子的[简明Vim练级攻略](http://coolshell.cn/articles/5426.html) 以及《学习Vi和Vim编辑器》

##Vim的安装

由于插件[YouCompleteMe](https://github.com/Valloric/YouCompleteMe)需要系统安装的Vim版本在7.3.584之上。而UBuntu12.04下通过`sudo apt-get install vim`自动安装的Vim版本不满足要求，所以首先需要卸载原来的vim版本。安装最新的vim7.4版本，具体操作点[这里](https://junevimer.github.com/2013/12/17/vim-setup.html)

当然，如果你不需要YCM插件支持。可以使用系统默认安装的Vim版本.

PS：安装好Vim7.4之后，在INSERT模式下，退格键(backspace)不能删除换行，也就是说不能从第二行一直按退格键到第一行，网上查找资料解决方法如下：

		1. vim缺省是和vi兼容的，设置成不兼容，在`.vimrc`中添加`set nocompatible`  
		2. vim的backspace有几种工作方式，默认是和vi兼容的，同样需要修改，在`.vimrc`中添加`set backspace=indent,eol,start`  
		(a) indent：如果使用了set indent等自动缩进，想用退格键将缩进字段删掉，必须设置这个选项，否则vim不响应退格  
		(b) eol：如果在INSERT模式下，在行头想通过退格键合并两行，需要设置这个选项  
		(c) start：要想删除在此次插入前的输入，需要设置这个选项  

##Vim配置步骤

1.用Vundle管理插件

相比sublime text2等现代编辑器，Vim缺乏默认的插件管理器，所有插件的文件都散布在~/.vim下的几个文件夹中，配置vim的过程, 就是在网上不停的搜插件，拷贝到~/.vim下，发现更新，要重新下载重新拷贝，想要删除某个不需要插件，更是要小心翼翼的不要删错。配置出顺手的Vim, 需要极大的耐心和运气。

当然vim社区的活力是不容怀疑的，没有枪没有炮大神们自己造。以前使用过tpope的[pathogen](https://github.com/tpope/vim-pathogen)，使用git submodule和pathogen, 管理插件已经大大简化。而今天介绍的vundle, 更是把git操作整合进去，用户需要做的只是去Github上找到自己想要的插件的名字，安装，更新和卸载都可有vundle来完成了。虽然去发现一个好的插件仍然是一个上下求索的过程，但是用户已经可以从安装配置的繁琐过程解脱了。

Vundle的具体介绍查看[vim.org](http://www.vim.org/scripts/script.php?script_id=3458),或者[github repo](https://github.com/gmarik/Vundle.vim)

2.Vundle安装和配置

下载vundle

         $ git clone https://github.com/gmarik/vundle.git ~/.vim/bundle/vundle

在.vimrc 中添加bundle的配置

		set nocompatible               " be iMproved
		filetype off                   " required!
		
		set rtp+=~/.vim/bundle/vundle/
		call vundle#rc()
		
		" let Vundle manage Vundle,required! 
		Bundle 'gmarik/vundle'
		
		" My Bundles here:
        Bundle 'a.vim'
        Bundle 'Mark'
        Bundle 'The-NERD-tree'
        Bundle 'The-NERD-Commenter'
        Bundle 'Junevimer/c.vim'
        Bundle 'UltiSnips'
        Bundle 'Tagbar'
        Bundle 'Tabular'
        Bundle 'Valloric/YouCompleteMe'
        Bundle 'scrooloose/syntastic'
        Bundle 'plasticboy/vim-markdown'
        Bundle 'kien/ctrlp.vim'
        Bundle 'ack.vim'
        Bundle 'Lokaltog/vim-powerline'
        
		filetype plugin indent on     " required!

bundle分为三类：

* 在Github vim-scripts 用户下的repos,只需要写出repos名称,如a.vim
* 在Github其他用户下的repos, 需要写出“用户名/repos名”,如Junevimer/c.vim
* 不在Github上的插件，需要写出git全路径

###安装插件

打开一个vim, 运行:`BundleInstall`

或者在命令行运行:`vim +BundleInstall +qall`

安装完成后插件就能用了

其它常用命令：

* 更新插件:`BundleUpdate`
* 清除不再使用的插件:`BundleClean`
* 列出所有插件:`BundleList`
* 查找插件:`BundleSearch`

##插件说明：

1. 本文推荐的部分脚本仅适用于C/C++开发，如用其他编程语言开发的，可再找其他合适的脚本
2. 一般插件都通过vundle来安装,特殊插件会说明
3. 插件配置就不放上来了,有兴趣的可以查看[我的vim配置](https://github.com/Junevimer/vim.cfg/blob/master/vimrc)

###a.vim
* 功能：快速切换.cpp(.c)文件和.h文件
* 操作：输入命令:A或:AV切换.c与同名.h文件
* 安装：Bundle 'a.vim'

###Mark
* 功能：高亮多个关键字
* 操作：<leader>m 高亮/取消高亮当前光标下的关键字，<leader>a 取消所有高亮关键字
* 安装：Bundle 'Mark'

### The-NERD-tree
* 功能：开启目录树导航
* 操作：命令行模式下输入:NERDTreeToggle打开或关闭目录导航
* 安装：Bundle 'The-NERD-tree'

###The-NERD-Commenter
* 功能：块注释
* 操作：一般模式下输入<leader>c<space>注释或取消注释当前行，或输入n<leader>c<space>注释当前行开始接下来的n行
* 安装：Bundle 'The-NERD-Commenter'

###c.vim
* 功能：高亮C/C++类与函数名
* 说明：这个是我从网上找的高亮脚本，稍微修改了下，使高亮颜色与desert配色方案搭配，不需要的可以无视哈，或下载插件后自己修改颜色
* 安装：Bundle 'junevimer/c.vim'

###UltiSnips
* 功能：快速插入自定义号的代码片段(强烈推荐)
* 操作：详细操作安装过程请看[官方文档以及视频](https://github.com/SirVer/ultisnips)
* 说明：如果安装YouCompleteMe而没有将YCM默认Tab键配置过，可能会导致Ultisnips的Tab键冲突，无法使用
* 安装：Bundle 'UltiSnips'

###Tagbar
* 功能: 标签导航
* 操作：命令行模式下输入:TagbarToggle打开或关闭Tagbar
* 安装：Bundle 'Tagbar'

###Tabular
* 功能：对齐文本
* 操作：在vim命令行模式下输入 :Tab /要根据其对齐的字符，如:Tab /"
* 安装：Bundle 'Tabular'

### YouCompleteMe
* 功能：自动补全
* 操作：自动补全时按向下方向键或CTRL+N选择要补全的内容
* 安装：Bundle 'Valloric/YouCompleteMe'，下载完插件以后，还需要进行编译，操作比较复杂，具体查看[这里](http://junevimer.github.io/vim/2013/12/11/youcompleteme.html)

###syntastic
* 功能：静态语法以及风格检查
* 安装：Bundle 'scrooloose/syntastic'

###vim-markdown
* 功能：提供markdown文件语法支持
* 安装：Bundle 'plasticboy/vim-markdown'

###ctrlp.vim
* 功能：文件搜索
* 操作：普通模式下按CTRL+P搜索文件，命令行模式下:CTRL+P搜索之前的命令
* 安装：Bundle 'kien/ctrlp.vim'

###ack.vim
* 功能：搜索当前文件夹下文件中的关键字，自动忽略.svn和.git中的内容
* 操作：一般模式下按F2自动搜索当前光标下关键字，命令行模式下输入:Ack 关键字，当然有多种模式可以选择，具体查看help
* 安装：Bundle 'ack.vim'

###vim-powerline
* 功能：加强美化的vim状态栏
* 安装：Bundle 'Lokaltog/vim-powerline'
* 说明：如果powerline状态栏显示乱码,则git clone https://github.com/eugeneching/consolas-powerline-vim 到~/.fonts目录下,目录不存在就创建一个
