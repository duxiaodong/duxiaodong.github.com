---
layout: post
title:  Vim自动补全神器–YouCompleteMe
description: ""
category: "Vim"
tags: [vim, YCM]
---
{% include JB/setup %}

##Vim代码补全现状

在漫长的Vim发展历史中，代码补全一直是比较被忽视的环节，相比众多IDE，vim本身的代码提示功能包括其众多补全插件显得无比简陋, 这是因为vim的先天不足，它是文本编译器，不能理解程序语意。引用王垠的一段文字：

    “文本编辑器”这种东西一般都不真正的理解程序语言。很多 Emacs 和 vi 的用户以为用 etags 和 ctags 这样的工具就能让他们“跳转到定义”，然而这些 tags 工具其实只是对程序的“文本”做一些愚蠢的正则表达式匹配。它们根本没有对程序进行 parse，所以其实只是在进行一些“瞎猜”。简单的函数定义它们也许能猜对位置，但是对于有重名的定义，或者局部变量的时候，它们就力不从心了。

##YouCompleteMe的特别之处

###基于语义补全

总所周知,Vim是一款文本编辑器.也就是说,其最基础的工作就是编辑文本,而不管该文本的内容是什么.在Vim被程序员所使用后,其慢慢的被肩负了与IDE一样的工作,文本自动补全(ie.acp,omnicppcompleter),代码检查(Syntastic)等等工作.

针对文本自动补全这个功能来说,主要有两种实现方式.

1.基于文本

我们常用的omnicppcompleter,acp,vim自带的c-x, c-n的实现方式就是基于文本.更通俗的说法,其实就是一个字:

猜

其通过文本进行一些正则表达式的匹配,再根据生成的tags(利用ctags生成)来实现自动补全的效果.

2.基于语意

顾名思义,其是通过分析源文件,经过语法分析以后进行补全.由于对源文件进行分析,基于语义的补全可以做到很精确.但是这显然是vim所不可能支持的.而且经过这么多年发展,由于语法分析有很高的难度,也一直没有合适的工具出现.直到,由apple支持的clang/llvm横空出世.YouCompleteMe也正是在clang/llvm的基础上进行构建的.

###整合实现了多种插件

* clang_complete
* AutoComplPop
* Supertab
* neocomplcache
* [Syntastic](https://github.com/scrooloose/syntastic)(类似功能,仅仅针对c/c++/obj-c代码)

###支持语言

* c
* c++
* obj-c
* c#
* python

对于其他的语言,会调用vim设置的omnifunc来匹配,因此同样支持php,ruby等语言.

已知的有

* javascript —-[tern\_for\_vim](https://github.com/marijnh/tern_for_vim)
* ruby/java —-[eclim](http://eclim.org/)

使用效果图

![](/images/ycm.gif)

###使用感受

* 和IDE一样,自动补全,
* 根据include的文件进行补全
* 不用再蹩脚的生成tags
* 补全非常精准,而且速度很快,不会有延迟(以前在大项目上,acp用起来实在是很卡)
* 支持类似tags的跳转,跳到定义处以及使用处
* 出错提示很智能,并且用起来真的是如丝般柔滑,不用输入:w进行强制检测

##安装

说完了那么多好处,就要说到安装了.不同于以往其他vim插件,YCM是一款编译型的插件.在下载完后,需要手动编译后才能使用.对应其他的插件来说,仅仅就是把.vim的文件丢到相应文件夹下就可以.而这也加大了使用YCM的难度.

* 最新版的Vim(7.3.584+),编译时添加+python标志(已经安装的可以通过vim --version查看,查看[从源码安装Vim7.4](http://www.junevimer.com/2013/12/17/vim-setup.html))
* cmake(mac可以通过[homebrew](http://brew.sh/)安装,brew install cmake,ubuntu可以通过sudo apt-get install cmake)
* 安装[vundle](https://github.com/gmarik/Vundle.vim#about)插件,用于安装管理vim的插件

###mac下快速安装

在.vimrc中添加下列代码

        Bundle 'Valloric/YouCompleteMe'

保存退出后打开vim,在正常模式下输入

        :BundleInstall

等待vundle将YouCompleteMe安装完成

而后进行编译安装:

        cd ~/.vim/bundle/YouCompleteMe
        git submodule update --init --recursive
        ./install --clang-completer

如果不需要c-family的补全,可以去掉--clang-completer.如果需要c#的补全,请加上--omnisharp-completer.

正常来说,YCM会去下载clang的包,如果已经有,也可以用系统--system-libclang.

就这样,安装结束.打开vim,如果没有提示YCM未编译,则说明安装已经成功了.

###手动编译安装

安装的脚本并不是什么时候都好用,至少对我来说是这样的.安装完之后出现了问题,参考issue#809.

在用:BundleInstall安装完成或者使用

        git clone --recursive https://github.com/Valloric/YouCompleteMe.git

获取最新的仓库,而后使用git submodule update --init --recursive确认仓库的完整性后,开始安装流程.

* 下载最新的clang二进制文件 YCM要求clang版本 > 3.2,一般来说都是[下载最新的](http://llvm.org/releases/download.html#3.4).
* 安装python-dev.(ubuntu下使用sudo apt-get install python-dev,mac下默认提供,否则请安装[command line tools](http://marchtea.com/?p=104))
* 编译

        cd ~ 
        mkdir ycm_build 
        cd ycm_build cmake -G “Unix Makefiles” -DPATH_TO_LLVM_ROOT=~/ycm_temp/llvm_root_dir . ~/.vim/bundle/YouCompleteMe/cpp make ycm_support_libs

这里需要注意的是,~/ycm\_temp/llvm\_root\_dir中包含的是根据第一步下载的压缩包解压出来的内容(包括include, bin等等文件)

这样就完成了,开始感受YCM提供的完全不逊色于大型IDE所提供的自动补全功能吧.

##配置

不同于很多vim插件,YCM首先需要编译,另外还需要有配置.在vim启动后,YCM会找寻当前路径以及上层路径的.ycm\_extra\_conf.py.在~/.vim/bundle/YouCompleteMe/cpp/ycm/.ycm\_extra\_conf.py中提供了默认的模板.也可以参考我的(就在模板上改改而已).不过这个解决了标准库提示找不到的问题.

一般来说,我会在~目录下放一个默认的模板,而后再根据不同的项目在当前目录下再拷贝个.ycm\_extra\_conf.py.

    # This file is NOT licensed under the GPLv3, which is the license for the rest
    # of YouCompleteMe.
    #
    # Here's the license text for this file:
    #
    # This is free and unencumbered software released into the public domain.
    #
    # Anyone is free to copy, modify, publish, use, compile, sell, or
    # distribute this software, either in source code form or as a compiled
    # binary, for any purpose, commercial or non-commercial, and by any
    # means.
    #
    # In jurisdictions that recognize copyright laws, the author or authors
    # of this software dedicate any and all copyright interest in the
    # software to the public domain. We make this dedication for the benefit
    # of the public at large and to the detriment of our heirs and
    # successors. We intend this dedication to be an overt act of
    # relinquishment in perpetuity of all present and future rights to this
    # software under copyright law.
    #
    # THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
    # EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
    # MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
    # IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR
    # OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE,
    # ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
    # OTHER DEALINGS IN THE SOFTWARE.
    #
    # For more information, please refer to <http://unlicense.org/>
    
    import os
    import ycm_core
    
    # These are the compilation flags that will be used in case there's no
    # compilation database set (by default, one is not set).
    # CHANGE THIS LIST OF FLAGS. YES, THIS IS THE DROID YOU HAVE BEEN LOOKING FOR.
    flags = [
    '-Wall',
    '-Wextra',
    #'-Werror',
    #'-Wc++98-compat',
    '-Wno-long-long',
    '-Wno-variadic-macros',
    '-fexceptions',
    '-stdlib=libc++',
    # THIS IS IMPORTANT! Without a "-std=<something>" flag, clang won't know which
    # language to use when compiling headers. So it will guess. Badly. So C++
    # headers will be compiled as C headers. You don't want that so ALWAYS specify
    # a "-std=<something>".
    # For a C project, you would set this to something like 'c99' instead of
    # 'c++11'.
    '-std=c++11',
    # ...and the same thing goes for the magic -x option which specifies the
    # language that the files to be compiled are written in. This is mostly
    # relevant for c++ headers.
    # For a C project, you would set this to 'c' instead of 'c++'.
    '-x',
    'c++',
    '-I',
    '.',
    '-isystem',
    '/usr/include',
    '-isystem',
    '/usr/local/include',
    '-isystem',
    '/Library/Developer/CommandLineTools/usr/include',
    '-isystem',
    '/Library/Developer/CommandLineTools/usr/bin/../lib/c++/v1',
    ]
    
    
    # Set this to the absolute path to the folder (NOT the file!) containing the
    # compile_commands.json file to use that instead of 'flags'. See here for
    # more details: http://clang.llvm.org/docs/JSONCompilationDatabase.html
    #
    # Most projects will NOT need to set this to anything; you can just change the
    # 'flags' list of compilation flags. Notice that YCM itself uses that approach.
    compilation_database_folder = ''
    
    if os.path.exists( compilation_database_folder ):
      database = ycm_core.CompilationDatabase( compilation_database_folder )
    else:
      database = None
    
    SOURCE_EXTENSIONS = [ '.cpp', '.cxx', '.cc', '.c', '.m', '.mm' ]
    
    def DirectoryOfThisScript():
      return os.path.dirname( os.path.abspath( __file__ ) )
    
    
    def MakeRelativePathsInFlagsAbsolute( flags, working_directory ):
      if not working_directory:
        return list( flags )
      new_flags = []
      make_next_absolute = False
      path_flags = [ '-isystem', '-I', '-iquote', '--sysroot=' ]
      for flag in flags:
        new_flag = flag
    
        if make_next_absolute:
          make_next_absolute = False
          if not flag.startswith( '/' ):
            new_flag = os.path.join( working_directory, flag )
    
        for path_flag in path_flags:
          if flag == path_flag:
            make_next_absolute = True
            break
    
          if flag.startswith( path_flag ):
            path = flag[ len( path_flag ): ]
            new_flag = path_flag + os.path.join( working_directory, path )
            break
    
        if new_flag:
          new_flags.append( new_flag )
      return new_flags
    
    
    def IsHeaderFile( filename ):
      extension = os.path.splitext( filename )[ 1 ]
      return extension in [ '.h', '.hxx', '.hpp', '.hh' ]
    
    
    def GetCompilationInfoForFile( filename ):
      # The compilation_commands.json file generated by CMake does not have entries
      # for header files. So we do our best by asking the db for flags for a
      # corresponding source file, if any. If one exists, the flags for that file
      # should be good enough.
      if IsHeaderFile( filename ):
        basename = os.path.splitext( filename )[ 0 ]
        for extension in SOURCE_EXTENSIONS:
          replacement_file = basename + extension
          if os.path.exists( replacement_file ):
            compilation_info = database.GetCompilationInfoForFile(
              replacement_file )
            if compilation_info.compiler_flags_:
              return compilation_info
        return None
      return database.GetCompilationInfoForFile( filename )
    
    
    def FlagsForFile( filename, **kwargs ):
      if database:
        # Bear in mind that compilation_info.compiler_flags_ does NOT return a
        # python list, but a "list-like" StringVec object
        compilation_info = GetCompilationInfoForFile( filename )
        if not compilation_info:
          return None
    
        final_flags = MakeRelativePathsInFlagsAbsolute(
          compilation_info.compiler_flags_,
          compilation_info.compiler_working_dir_ )
    
        # NOTE: This is just for YouCompleteMe; it's highly likely that your project
        # does NOT need to remove the stdlib flag. DO NOT USE THIS IN YOUR
        # ycm_extra_conf IF YOU'RE NOT 100% SURE YOU NEED IT.
        #try:
        #  final_flags.remove( '-stdlib=libc++' )
        #except ValueError:
        #  pass
      else:
        relative_to = DirectoryOfThisScript()
        final_flags = MakeRelativePathsInFlagsAbsolute( flags, relative_to )
    
      return {
        'flags': final_flags,
        'do_cache': True
      }

##集成Syntastic

YCM很早就支持集成[Syntastic](https://github.com/scrooloose/syntastic)了，上面demo里面，代码前的出现红色叉叉，就是YCM结合Syntastic爆出的语法错误。刚开始用YCM的时候，更看重其代码补全功能，Syntastic没放在心上，结果发现越用越离不开了。当编写C++代码的时候，每次光标悬停2秒钟以上的时候，YCM都会在后台扫描你当前的文件，你刚刚输入的代码有什么编译错误，马上就能显示出来，及时的改掉，不再积累到最后编译的时候。当然这是现代IDE的标配功能，vim中也有插件可以实现，但是有了YCM后，再用vundle安装Syntastic，甚至不用任何配置就实现了这些功能，实在是太方便了

![](/images/syntastic.png)

##YouCompleteMe提供的其他功能

YCM除了提供了基本的补全功能,自动提示错误的功能外,还提供了类似tags的功能:

* 跳转到定义GoToDefinition
* 跳转到声明GoToDeclaration
* 以及两者的合体GoToDefinitionElseDeclaration

可以在.vimrc中配置相应的快捷键.

        nnoremap <leader>gl :YcmCompleter GoToDeclaration<CR>
        nnoremap <leader>gf :YcmCompleter GoToDefinition<CR>
        nnoremap <leader>gg :YcmCompleter GoToDefinitionElseDeclaration<CR>

另外,YCM也提供了丰富的配置选项,同样在.vimrc中配置.具体请参考

        let g:ycm_error_symbol = '>>'
        let g:ycm_warning_symbol = '>*'

同时,YCM可以打开location-list来显示警告和错误的信息:YcmDiags.个人关于ycm的配置如下:

        " for ycm
        let g:ycm_error_symbol = '>>'
        let g:ycm_warning_symbol = '>*'
        let g:ycm_confirm_extra_conf = 0
        nnoremap <leader>gl :YcmCompleter GoToDeclaration<CR>
        nnoremap <leader>gf :YcmCompleter GoToDefinition<CR>
        nnoremap <leader>gg :YcmCompleter GoToDefinitionElseDeclaration<CR>
        nmap <leader>gd :YcmDiags<CR>

YCM提供的跳跃功能采用了vim的jumplist,往前跳和往后跳的快捷键为Ctrl+O以及Ctrl+I.

##总结

YouCompleteMe是我用过的最爽的一个自动补全的插件了.之前使用acp时,遇到大文件基本上就卡死了,以至于都不怎么敢使用.由于YCM使用的时C/S结构,部分使用vim脚本编写,部分认为原生代码,使得跑起来速度飞快.

抛弃Vim自带的坑爹的补全吧,抛弃ctags吧,抛弃cscope吧,YCM才是终极的补全神器.

最后祝大家码年顺利,一码平川,码到功成.
