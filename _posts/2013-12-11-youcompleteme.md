---
layout: post
title:  Vim下自动补齐插件YouComplete安装与配置
description: ""
category: "Vim"
tags: [vim, YCM]
---
{% include JB/setup %}

(2014年11月3日更新)

使用Vim编写程序少不了使用自动补全插件，在Linux下有没有类似VS中的Visual Assist X这么方便快捷的补全插件呢？以前用的是omnicppcomplete,但效果还是不是很好,并且在项目比较大时,会出现卡顿的现象.在使用了YCM实现自动补全之后,完全抛弃了之前的插件.

###YCM整合实现了多种插件:

* clang_complete
* AutoComplPop
* Supertab
* neocomplcache
* Syntastic(类似功能,仅仅针对c/c++/obj-c代码)

###YCM使用效果图

![](/images/2013/12/ycm.gif)

###YCM使用感受

* 和IDE一样,自动补全,
* 根据include的文件进行补全
* 不用再蹩脚的生成tags
* 补全非常精准,而且速度很快,不会有延迟(以前在大项目上,acp用起来实在是很卡)
* 支持类似tags的跳转,跳到定义处以及使用处
* 出错提示很智能,不用输入:w进行强制检测

###安装

说完了那么多好处,就要说到安装了.不同于以往其他vim插件,YCM是一款编译型的插件.在下载完后,需要手动编译后才能使用.对应其他的插件来说,仅仅就是把.vim的文件丢到相应文件夹下就可以.而这也加大了使用YCM的难度.

* 最新版的Vim(7.3.584+),编译时添加+python标志(已经安装的可以通过vim --version查看,查看[从源码安装Vim7.4](/2013/12/17/vim-setup.html))
* cmake(mac可以通过[homebrew](http://brew.sh/)安装,brew install cmake,ubuntu可以通过sudo apt-get install cmake)
* 安装[vundle](https://github.com/gmarik/Vundle.vim#about)插件,用于安装管理vim的插件,不会安装和使用vundle的可以查看[Ubuntu12.04下Vim配置之不折腾版](/2013/12/12/vim-config.html)

###MAC OS X下快速安装

在.vimrc中添加下列代码

        Bundle 'Valloric/YouCompleteMe'

保存退出后打开vim,在正常模式下输入

        :BundleInstall

等待vundle将YouCompleteMe安装完成

而后进行编译安装:

        cd ~/.vim/bundle/YouCompleteMe
        git submodule update --init --recursive
        ./install.sh --clang-completer

如果不需要c-family的补全,可以去掉--clang-completer.如果需要c#的补全,请加上--omnisharp-completer.

正常来说,YCM会去下载clang的包,如果已经有,也可以用系统--system-libclang.

就这样,安装结束.打开vim,如果没有提示YCM未编译,则说明安装已经成功了.

Ps:安装的脚本并不是什么时候都好用,可能会出现情况,具体可以参考github上的完整安装说明.

###Ubuntu 12.04下安装

参考

- [Vim自动补全插件----YouCompleteMe安装与配置](http://www.cnblogs.com/zhongcq/p/3630047.html)
- [YouCompleteMe](http://valloric.github.io/YouCompleteMe/)

####1、编译安装llvm-clang

下载最新的LLVM、clang 及辅助库源码可用:

	cd ~/llvm-clang
	svn co http://llvm.org/svn/llvm-project/llvm/trunk llvm
	cd llvm/tools
	svn co http://llvm.org/svn/llvm-project/cfe/trunk clang
	cd ../..
	cd llvm/tools/clang/tools
	svn co http://llvm.org/svn/llvm-project/clang-tools-extra/trunk extra
	cd ../../../..
	cd llvm/projects
	svn co http://llvm.org/svn/llvm-project/compiler-rt/trunk compiler-rt
	cd ..

返回~/llvm-clang目录，并新建一个目录llvm-build专门用于编译llvm-clang，使得不污染源码。

	mkdir llvm-build
	cd llvm-build/
	../llvm/configure --enable-optimized --enable-targets=host-only --prefix=/usr/clang
		
建立编译环境

此种配置后，其中--prefix指定llvm-clang安装目录

输入 	
	
	make -j4 //	开始编译

	sudo make install //进行安装

如要卸载则在该目录下输入

	sudo make uninstall

安装好后，输入 

	clang -v //查看版本信息：

####2、安装clang标准库

clang 的标准库————libc++(接口层)和 libc++abi(实现层)需要安装头文件和动态链接库(*.so)。

安装libc++

	cd ~/llvm-clang
	svn co http://llvm.org/svn/llvm-project/libcxx/trunk libcxx
	cd libcxx/lib
	./buildit
	
头文件已经生成到 ~/llvm-clang/libcxx/include/,要让 clang 找到必须复制到 /usr/include/c++/v1/

	sudo cp -r ~/llvm-clang/libcxx/include/ /usr/include/c++/v1/

*.so 文件已生成 ~/llvm-clang/libcxx/lib/libc++.so.1.0,要让 clang 访问必须复 制到 /usr/lib/,并创建软链接

    ln -s ~/llvm-clang/libcxx/lib/libc++.so.1.0 ~/llvm-clang/libcxx/lib/libc++.so.1
    ln -s ~/llvm-clang/libcxx/lib/libc++.so.1.0 ~/llvm-clang/libcxx/lib/libc++.so
    sudo cp ~/llvm-clang/libcxx/lib/libc++.so* /usr/lib/
    
类似,源码安装 libc++abi 的头文件和动态链接库:

    cd  ~/llvm-clang/
    svn co http://llvm.org/svn/llvm-project/libcxxabi/trunk libcxxabi
    cd libcxxabi/lib
    ./buildit
  
头文件已经生成到 ~/llvm-clang/libcxxabi/include/,要让 clang 找到必须复制到 /usr/include/c++/v1/

	cp -r ~/llvm-clang/libcxxabi/include/ /usr/include/c++/v1/

*.so 文件已生成 ~/llvm-clang/libcxx/lib/libc++abi.so.1.0,要让 clang 访问必 须复制到 /usr/lib/,并创建软链接

	ln -s ~/llvm-clang/libcxxabi/lib/libc++abi.so.1.0 ~/llvm-clang/libcxxabi/lib/libc++abi.so.1
	ln -s ~/llvm-clang/libcxxabi/lib/libc++abi.so.1.0 ~/llvm-clang/libcxxabi/lib/libc++abi.so
	sudo cp ~/llvm-clang/libcxxabi/lib/libc++abi.so* /usr/lib/

####3、安装YouCompleteMe

下载源码。使用vundle搞定，在vimrc文件中加入 

> Bundle 'Valloric/YouCompleteMe'

执行命令：

    $ cd ~
    $ mkdir ~/ycm_build
    $ cd ~/ycm_build
    $ cmake -G "Unix Makefiles" -DPATH_TO_LLVM_ROOT=~/ycm_temp/llvm_root_dir . ~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp
　
注意：这里的DPATH\_TO\_LLVM\_ROOT要替换成你自己clang安装的目录，例如我的是 /usr/clang（即：上面步骤的编译所指定的路径）

执行 

	make ycm_core

这样将在~/.vim/bundle/YouCompleteMe/python/目录下自动生成两个文件(libclang.so和ycm_core.so)

这还是不够的，还必须执行命令：

	make ycm_support_libs
	
这条命令才会生成第三个文件ycm\_client\_support.so。因为，YouCompleteMe是C/S架构的，所以存在服务器和服务端的说法。
	
##配置

不同于很多vim插件,YCM首先需要编译,另外还需要有配置.在vim启动后,YCM会找寻当前路径以及上层路径的.ycm\_extra\_conf.py.在~/.vim/bundle/YouCompleteMe/cpp/ycm/.ycm\_extra\_conf.py中提供了默认的模板.也可以参考我的(就在模板上改改而已).不过这个解决了标准库提示找不到的问题.

- [C](https://github.com/Junevimer/dotfiles/blob/master/ycm_c_conf.py) 
- [C++](https://github.com/Junevimer/dotfiles/blob/master/ycm_cpp_conf.py) 

一般来说,在~目录下放一个默认的模板,而后再根据不同的项目在当前目录下再拷贝个.ycm\_extra\_conf.py.

##集成Syntastic

YCM很早就支持集成[Syntastic](https://github.com/scrooloose/syntastic)了.刚开始用YCM的时候，更看重其代码补全功能，Syntastic没放在心上，结果发现越用越离不开了。当编写C++代码的时候，每次光标悬停2秒钟以上的时候，YCM都会在后台扫描你当前的文件，你刚刚输入的代码有什么编译错误，马上就能显示出来，及时的改掉，不再积累到最后编译的时候。当然这是现代IDE的标配功能，vim中也有插件可以实现，但是有了YCM后，再用vundle安装Syntastic，甚至不用任何配置就实现了这些功能，实在是太方便了

![](/images/2013/12/syntastic.png)

##YouCompleteMe提供的其他功能

YCM除了提供了基本的补全功能,自动提示错误的功能外,还提供了类似tags的功能:

* 跳转到定义GoToDefinition
* 跳转到声明GoToDeclaration
* 以及两者的合体GoToDefinitionElseDeclaration

可以在.vimrc中配置相应的快捷键.

        nnoremap <leader>gl :YcmCompleter GoToDeclaration<CR>
        nnoremap <leader>gf :YcmCompleter GoToDefinition<CR>
        nnoremap <leader>gg :YcmCompleter GoToDefinitionElseDeclaration<CR>

另外,YCM也提供了丰富的配置选项,同样在.vimrc中配置.同时,YCM可以打开location-list来显示警告和错误的信息:YcmDiags.个人关于ycm的配置如下:

    """"""""""""""""""""""""""""""""""""""""""""""""""""""""""""
    " => YouCompleteMe  代码自动补全
    """"""""""""""""""""""""""""""""""""""""""""""""""""""""""""
    Bundle 'Valloric/YouCompleteMe'
    " youcompleteme  默认tab  s-tab 和自动补全冲突
    " let g:ycm_key_list_select_completion=['<c-n>']
    " let g:ycm_key_list_select_completion = ['<Down>']
    " let g:ycm_key_list_previous_completion=['<c-p>']
    " let g:ycm_key_list_previous_completion = ['<Up>']
    let g:ycm_confirm_extra_conf=0      " 关闭加载.ycm_extra_conf.py提示
    let g:ycm_complete_in_comments = 1  "在注释输入中也能补全
    let g:ycm_complete_in_strings = 1   "在字符串输入中也能补全
    let g:ycm_collect_identifiers_from_tags_files=1                 " 开启 YCM 基于标签引擎
    let g:ycm_collect_identifiers_from_comments_and_strings = 1   "注释和字符串中的文字也会被收入补全
    let g:ycm_seed_identifiers_with_syntax=1   "语言关键字补全, 不过python关键字都很短，所以，需要的自己打开
    let g:ycm_collect_identifiers_from_tags_files = 1
    let g:ycm_min_num_of_chars_for_completion=2                     " 从第2个键入字符就开始罗列匹配项
    " 引入，可以补全系统，以及python的第三方包 针对新老版本YCM做了兼容
    " old version
    if !empty(glob("~/.vim/bundle/YouCompleteMe/cpp/ycm/.ycm_extra_conf.py"))
        let g:ycm_global_ycm_extra_conf = "~/.vim/bundle/YouCompleteMe/cpp/ycm/.ycm_extra_conf.py"
    endif
    " new version
    if !empty(glob("~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/ycm/.ycm_extra_conf.py"))
        let g:ycm_global_ycm_extra_conf = "~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/ycm/.ycm_extra_conf.py"
    endif
    
    "mapping
    nmap <leader>gd :YcmDiags<CR>
    nnoremap <leader>gl :YcmCompleter GoToDeclaration<CR>           " 跳转到申明处
    nnoremap <leader>gf :YcmCompleter GoToDefinition<CR>            " 跳转到定义处
    nnoremap <leader>gg :YcmCompleter GoToDefinitionElseDeclaration<CR>
    
    " 直接触发自动补全
    let g:ycm_key_invoke_completion = '<C-Space>'
    " 黑名单,不启用
    let g:ycm_filetype_blacklist = {
          \ 'tagbar' : 1,
          \ 'gitcommit' : 1,
          \}
        
YCM提供的跳跃功能采用了vim的jumplist,往前跳和往后跳的快捷键为Ctrl+O以及Ctrl+I.

PS:C语言标准库函数不提供自动显示补全列表,需要`<C-Space>`(由vimrc配置文件配置)触发自动补全

##总结

YouCompleteMe是我用过的最爽的一个自动补全的插件了.之前使用acp时,遇到大文件基本上就卡死了,以至于都不怎么敢使用.由于YCM使用的时C/S结构,部分使用vim脚本编写,部分认为原生代码,使得跑起来速度飞快.
