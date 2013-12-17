---
layout: post
title: Ubuntu12.04下Vim配置之不折腾版
description: ""
category: Vim
tags: [vim]
---
{% include JB/setup %}

##写在前面

用vim大概也有六个月了吧，用原生的大概1个月，后面各种折腾，这是第2次折腾以后的最终版本了，写篇博客记录下。

##Vim基本用法

推荐：耗子的[简明Vim练级攻略](http://coolshell.cn/articles/5426.html) 以及《学习Vi和Vim编辑器》

##Vim的安装

由于插件[YouCompleteMe](https://github.com/Valloric/YouCompleteMe)需要系统安装的Vim版本在7.3.584之上。而UBuntu12.04下通过`sudo apt-get install vim`自动安装的Vim版本不满足要求，所以首先需要卸载原来的vim版本。安装最新的vim7.4版本，具体操作点[这里](https://junewater.github.com/2013/12/17/vim-setup.html)

当然，如果你不需要YCM插件支持。可以使用系统默认安装的Vim版本.

PS：安装好Vim7.4之后，在INSERT模式下，退格键(backspace)不能删除换行，也就是说不能从第二行一直按退格键到第一行，网上查找资料解决方法如下：

		1. vim缺省是和vi兼容的，设置成不兼容，在`.vimrc`中添加`set nocompatible`  
		2. vim的backspace有几种工作方式，默认是和vi兼容的，同样需要修改，在`.vimrc`中添加`set backspace=indent,eol,start`  
		(a) indent：如果使用了set indent等自动缩进，想用退格键将缩进字段删掉，必须设置这个选项，否则vim不响应退格  
		(b) eol：如果在INSERT模式下，在行头想通过退格键合并两行，需要设置这个选项  
		(c) start：要想删除在此次插入前的输入，需要设置这个选项  

##Vim配置步骤

###用Vundle管理插件
 
如果不使用vundle的话（当然啦，还有其他的插件管理工具，比如pathogen），进行插件的安装，配置和管理相对会比较麻烦，曾经没使用vundle的时候我经常遇到无法安装插件以及
卸载插件非常繁琐的问题。但使用vundle之后，你只要在文件中添加一行你的插件名再安装就OK了.这里简单说一下Vundle的使用。

* Vundle Github 地址:https://github.com/gmarik/vundle

##Vundle安装

###创建~/.vim和文件.vimrc

进入你的home目录创建.vim文件夹和.vimrc文件

###获取vundle

		$ git clone https://github.com/gmarik/vundle.git ~/.vim/bundle/vundle
		
###编辑如下内容到.vimrc文件

		" "开头的为注释,仅为说明
		set nocompatible               " be iMproved
		filetype off                   " required!
		
		set rtp+=~/.vim/bundle/vundle/
		call vundle#rc()
		
		" let Vundle manage Vundle
		" required! 
		Bundle 'gmarik/vundle'
		
		" My Bundles here:
		" 需要安装的插件都写在这下面

		" original repos on github
		" 插件来自github,写在下方，只要作者名/项目名就可以了
		" Bundle 'tpope/vim-fugitive'
		" Bundle 'Lokaltog/vim-easymotion'
		" Bundle 'rstacruz/sparkup', {'rtp': 'vim/'}
		" Bundle 'tpope/vim-rails.git'

		" vim-scripts repos
		" 插件来自vim-scripts，直接写插件名就可以了
		" Bundle 'L9'
		" Bundle 'FuzzyFinder'

		" non github repos
		" 非github上的插件。如下：
		" Bundle 'git://git.wincent.com/command-t.git'

		" git repos on your local machine (ie. when working on your own plugin)
		" Bundle 'file:///Users/gmarik/path/to/plugin'
		
		filetype plugin indent on     " required!

		" Brief help
		" Vundle的一些指令说明	
		" :BundleList          - list configured bundles
		" :BundleInstall(!)    - install(update) bundles
		" :BundleSearch(!) foo - search(or refresh cache first) for foo
		" :BundleClean(!)      - confirm(or auto-approve) removal of unused bundles
		"
		" see :h vundle for more details or wiki for FAQ
		" NOTE: comments after Bundle command are not allowed..

###安装你的插件

* 将要安装的插件加入.vimrc文件
* 保存.vimrc文件退出当前的vim
* 重新打开Vim,输入命令`:BundleInstall`,然后vim就会自动安装你的插件了

###如何删除插件

* 编辑.vimrc文件删除你要移除的插件行（注释或删除）
* 保存.vimrc文件并退出Vim
* 重新打开Vim,输入命令`:BundleClean`

##插件及插件配置说明：

1. 插件配置中，许多都设置了按键映射，不喜欢的可以不用配置按键映射
2. 本文推荐的部分脚本仅适用于C/C++开发，如用其他编程语言开发的，可再找其他合适的脚本
3. 有些脚本需要先安装ctags才能支持，ctags和cscope的安装配置网上一大堆，这里就不说了，推荐一个个人认为比较好的吧！[手把手教你把Vim改装成一个IDE变成环境](http://blog.csdn.net/wooin/article/details/1858917)
4. 没有操作说明的即为安装好插件即可以使用,没有配置说明的表示不需要配置也可以使用
4. 由于整理的匆忙，所列插件并没有按序排列

###a.vim
* 功能：快速切换.cpp(.c)文件和.h文件
* 操作：输入命令:A或:AV切换.c与同名.h文件
* 安装：Bundle 'a.vim'

###Mark
* 功能：高亮多个关键字
* 操作：mm高亮/取消高亮当前光标下的关键字，ma取消所有高亮关键字
* 安装：Bundle 'Mark'

###Tagbar
* 功能: 标签导航
* 操作：一般模式下输入tg打开或关闭Tagbar
* 安装：Bundle 'Tagbar'
		
### The-NERD-tree
* 功能：开启目录树导航
* 操作：一般模式下输入tr打开或关闭The-NERD-Tree
* 安装：Bundle 'The-NERD-tree'

###The-NERD-Commenter
* 功能：块注释
* 操作：一般模式下输入cc注释或取消注释当前行，或输入9cc注释当前行开始接下来的9行
* 安装：Bundle 'The-NERD-Commenter'

### YouCompleteMe
* 功能：自动补全
* 操作：自动补全时按向下方向键或CTRL+N键选择要补全的内容
* 说明：最后一条映射key_list_select是为了解决与UltiSnip插件的Tab键冲突问题，默认list_select键为Tab，如果不需要安装UltiSnip插件，可注释该条配置，使用默认Tab键进行list选择
* 安装：Bundle 'Valloric/YouCompleteMe'，下载号插件以后，还需要进行编译，操作比较复杂，具体查看[这里](http://junewater.github.io/vim/2013/12/11/youcompleteme.html)

###c.vim
* 功能：高亮C/C++类与函数名
* 说明：这个是我从网上找的高亮脚本，稍微修改了下，使高亮颜色与desert配色方案搭配，不需要的可以无视哈，或下载插件后自己修改颜色
* 安装：Bundle 'JuneWater/c.vim'

###lua.vim
* 功能：提供lua脚本编辑增强功能
* 安装：Bundle 'lua.vim'

###Tabular
* 功能：对齐文本
* 操作：在vim命令行模式下输入 :Tab /要根据其对齐的字符，如:Tab /"
* 安装：Bundle 'Tabular'

###mru.vim
* 功能：列出最近打开文件列表
* 操作：在vim命令行模式下输入:MRU
* 安装：Bundle 'vim-scripts/mru.vim'

###UltiSnips
* 功能：快速插入自定义号的代码片段(强烈推荐)
* 操作：详细操作安装过程请看[官方文档以及视频](https://github.com/SirVer/ultisnips)
* 说明：如果安装YouCompleteMe而没有将YCM默认Tab键配置过，可能会导致Ultisnips的Tab键冲突，无法使用
* 安装：Bundle 'UltiSnips'

###xml
* 功能：书写xml文件时自动补全标签
* 说明：这里我没有安装，因为默认只会对.xml,html等xml文件自动补全，而对.c或.cpp文件等不支持，而我需要在.c和.cpp中书写xml消息，这个插件对我来说没什么用。如果有人知道怎么使得这个插件支持其他类型的文件的，麻烦留言告知下，感激不尽!
* 安装：Bundle 'xml.vim'

###taglist
* 列出源文件中的tag并跳转
* 操作：一般模式下输入tl打开或关闭taglist窗口
* 说明：taglist依赖于ctags，所以要先安装ctags，否则taglist装了也没法用
* 安装：Bundle 'taglist.vim'

###syntastic
* 功能：静态语法以及风格检查
* 安装：Bundle 'scrooloose/syntastic'

###vim-misc
* 功能：lua.vim需要
* 安装：Bundle 'xolox/vim-misc'

###delemitMate
* 功能：输入引号，括号时，自动补全
* 操作：输入(时，自动补全为()并且光标停在括号中，其他类似
* 安装：Bundle 'Raimondi/delimitMate'

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
