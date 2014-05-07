---
layout: post
title:  Vim下自动补齐插件YouComplete安装与配置
description: ""
category: "Vim"
tags: [vim, YCM]
---
{% include JB/setup %}

使用Vim编写程序少不了使用自动补全插件，在Linux下有没有类似VS中的Visual Assist X这么方便快捷的补全插件呢？以前用的是omnicppcomplete,但效果还是不是很好,并且在项目比较大时,会出现卡顿的现象.在使用了YCM实现自动补全之后,完全抛弃了之前的插件.

###YCM整合实现了多种插件:

* clang_complete
* AutoComplPop
* Supertab
* neocomplcache
* Syntastic(类似功能,仅仅针对c/c++/obj-c代码)

###YCM使用效果图

![](/images/ycm.gif)

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

###Ubuntu12.04下快速安装

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

YCM很早就支持集成[Syntastic](https://github.com/scrooloose/syntastic)了.刚开始用YCM的时候，更看重其代码补全功能，Syntastic没放在心上，结果发现越用越离不开了。当编写C++代码的时候，每次光标悬停2秒钟以上的时候，YCM都会在后台扫描你当前的文件，你刚刚输入的代码有什么编译错误，马上就能显示出来，及时的改掉，不再积累到最后编译的时候。当然这是现代IDE的标配功能，vim中也有插件可以实现，但是有了YCM后，再用vundle安装Syntastic，甚至不用任何配置就实现了这些功能，实在是太方便了

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

另外,YCM也提供了丰富的配置选项,同样在.vimrc中配置.同时,YCM可以打开location-list来显示警告和错误的信息:YcmDiags.个人关于ycm的配置如下:

        """"""""""YouCompleteMe""""""""
        nmap <leader>gd :YcmDiags<CR>
        nnoremap <leader>gl :YcmCompleter GoToDeclaration<CR>           " 跳转到申明处
        nnoremap <leader>gf :YcmCompleter GoToDefinition<CR>            " 跳转到定义处
        nnoremap <leader>gg :YcmCompleter GoToDefinitionElseDeclaration<CR>
        let g:ycm_global_ycm_extra_conf = '~/.vim/bundle/YouCompleteMe/cpp/ycm/.ycm_extra_conf.py'
        let g:ycm_error_symbol = '>>'                                   " 编译错误标识符
        let g:ycm_warning_symbol = '>*'                                 " 编译警告标识符
        let g:ycm_confirm_extra_conf=0                                  " 关闭加载.ycm_extra_conf.py提示
        let g:ycm_collect_identifiers_from_tags_files=1                 " 开启 YCM 基于标签引擎
        let g:ycm_min_num_of_chars_for_completion=2                     " 从第2个键入字符就开始罗列匹配项
        let g:ycm_cache_omnifunc=0                                      " 禁止缓存匹配项,每次都重新生成匹配项
        let g:ycm_seed_identifiers_with_syntax=1                        " 语法关键字补全
        let g:ycm_complete_in_comments = 1                              " 在注释输入中也能补全
        let g:ycm_complete_in_strings = 1                               " 在字符串输入中也能补全
        let g:ycm_collect_identifiers_from_comments_and_strings = 0     " 注释和字符串中的文字也会被收入补全
        " let g:ycm_semantic_triggers = {}
        " let g:ycm_semantic_triggers.c = ['->', '.', ' ', '(', '[', '&']
        set completeopt=longest,menu                                    " 让Vim的补全菜单行为与一般IDE一致(参考VimTip1228)
        autocmd InsertLeave * if pumvisible() == 0|pclose|endif         " 离开插入模式后自动关闭预览窗口
        inoremap <expr> <CR>       pumvisible() ? "\<C-y>" : "\<CR>"    " 回车即选中当前项
        
        " YCM 补全菜单配色
        " highlight Pmenu ctermfg=2 ctermbg=3 guifg=SeaGreen guibg=darkgreen    " 菜单
        " highlight PmenuSel ctermfg=2 ctermbg=3 guifg=SeaGreen guibg=darkgreen " Select
        
YCM提供的跳跃功能采用了vim的jumplist,往前跳和往后跳的快捷键为Ctrl+O以及Ctrl+I.

##总结

YouCompleteMe是我用过的最爽的一个自动补全的插件了.之前使用acp时,遇到大文件基本上就卡死了,以至于都不怎么敢使用.由于YCM使用的时C/S结构,部分使用vim脚本编写,部分认为原生代码,使得跑起来速度飞快.
