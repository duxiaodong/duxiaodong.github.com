---
layout: post
title: Vim-airline安装小结
description: ""
category: "Vim"
tags: [airline powerline]
---
{% include JB/setup %}

https://github.com/bling/vim-airline 是一个非常不错的状态显示工具

前段时间装好了airline插件，但是没呈现应有的效果，原因是没有安装字体。根据官网的提示需要安装powerline的字体，我便照着powerline官网的配置方法来，但是还是没有成功。其原因是我安装了patched fonts，但是没有将terminal的font设置为patched fonts中的一种，所以statusbar会有乱码。下面对安装patched fonts做一个小结。

##一、Ubuntu12.04 下

### 1.Download patched fonts

$mkdir ~/.fonts新建一个font文件夹，然后$cd ~/.fonts进入.fonts文件夹。

    $git clone git@github.com:Lokaltog/powerline-fonts.git
    
将powerline的patched fonts拷贝到~/.fonts目录下，

### 2.Install the patched fonts to your System

运行$fc-cache -vf ~/.fonts安装patched fonts到系统中

### 3.Set Terminal fonts

设置Terminal字体非常重要，我初次配置时，安装patched fonts，但是由于没有set terminal font,所以statusbar显示乱码。设置方法如下：

Edit——Profiles——Default(Edit)——General——Font，选择~/.fonts/powerline下的某一种即可(可选范围一定要在刚才下载的patched fonts中选一种，否则乱码。)，当然也可以采取powerline font installing中的另一种方式，不过我没做尝试，有兴趣的朋友try it。

##二、Mac OS X 下

### 1.Download patched fonts

    $git clone git@github.com:Lokaltog/powerline-fonts.git ~/Library/Fonts/powerline-fonts

### 2.Install the patched fonts to your Systewa
    
打开字体册应用程序，添加下载的字体即可

### 3.Set Terminal fonts or Set Macvim fonts

iterm2下设置字体：

iTerm->Preferences->Profile->Texts

选择poweline的一种字体

如果使用的是Macvim,那么终端设置的字体与airline显示无关，需要在macvim中设置字体。

Macvim下设置字体：

    if has("gui_running")
        set guifont=DejaVu\ Sans\ Mono\ for\ Powerline:h14 //powerline字体
    endif

##FAQ

###1. 不显示git分支

安装vim-fugitive git外包插件即可

    Bundle 'tpope/vim-fugitive'

##参考
- [vim安装airline插件](http://chinacheng.iteye.com/blog/1935564)
- [vim-airline FAQ wiki](https://github.com/bling/vim-airline/wiki/FAQ)
