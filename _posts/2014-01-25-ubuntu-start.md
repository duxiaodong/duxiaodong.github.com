---
layout: post
title: 安装完Ubuntu之后需要做的事情
description: ""
category: "Linux System"
tags: [ubuntu, Linux]
---
{% include JB/setup %}

##Index

* [一. 前言](#前言)
* [二. 应用](#Applications)
  * [Google Chrome](#Chrome)
  * [搜狗输入法](#搜狗输入法)
  * [Vim](#Vim)
  * [Git](#Git)
  * [Dropbox](#Dropbox)
  * [Retext](#Retext)
  * [Goagent](#Goagent)
  * [翻译词典](#翻译词典)
  * [pywebqq](#pywebqq)
* [三. 配置](#Configuration)
  * [禁用笔记本电脑触摸板](#禁用笔记本电脑触摸板)
  * [Dotfiles配置](#Dotfiles配置)
  * [Vim配置](#Vim配置)
* [四. 结语](#结语)

## <span id="前言">一. 前言</span>

前天回到了温州老家,家里的破房子是老爸老妈结婚的时候盖的,装修完以后房间没有网口,路由器所在房间和我的房间还隔了一个楼层,无奈只能准备用无线上网,结果小Y的wifi开关在Ubuntu下打不开.网上google了下,结果被告知在Windows下关闭的wifi需要在Windows下打开,Linux下开启不了.真是坑爹啊!既然要装回下windows打开wifi开关,并且UBuntu也被自己折腾得不知道装了多少乱七八糟的东西,那就重装系统呗!也顺便整理下UBuntu下的一些软件和配置.

安装的Ubuntu系统版本为[ubuntu-12.04.4-desktop-i386.iso](http://releases.ubuntu.com/precise/ubuntu-12.04.4-desktop-i386.iso),语言选择为简体中文,主要是懒得装英文版然后再去下载中文字库.要使用英文版,只要进系统后设置系统语言为English就好了,也不需要再下载中文字库(安装系统过程中会下载的).

废话不多说,下面具体介绍安装好UBuntu系统以后需要安装的Applications和需要配置的东东

## <span id="Applications">二. 应用</span>

***

### <span id="Chrome">Google Chrome</span>

1.下载google chrome deb包

32位：https://dl.google.com/linux/direct/google-chrome-stable\_current\_i386.deb

2.使用终端安装：

        $ cd ~/Downloads
        $ sudo dpkg -i google-chrome-stable_current_i386.deb

这时会提示错误,依赖关系没有满足,无法安装.

3.接下来我们解决依赖关系

        $ sudo apt-get -f install

这样,就会先自动安装依赖的软件,然后自动安装好google-chrome

***

###<span id="搜狗输入法">搜狗输入法</span>

1.卸载原有的输入法，fcitx或ibus。如卸载fcitx：

        $ sudo apt-get remove fcitx*（如不需保留配置文件用purge）
        $ sudo apt-get autoremove（自动卸载依赖软件）
        $ sudo dpkg –get-selections | grep fcitx（查询fcitx相关的软件包是否卸载）

2.安装搜狗拼音输入法

        $ sudo add-apt-repository ppa:fcitx-team/nightly
        $ sudo apt-get update
        $ sudo apt-get install fcitx-sogoupinyin

3.设置 fcitx

        language support --> language--> keyboard input method system --> fcitx

4.注销ubuntu(或直接重启系统)

        Ctrl+Alt+F1 进入文字界面，执行如下命令;（记住：Ctrl+Alt+F7是回到图形界面）
        sudo /etc/init.d/gdm restart

5.向fcitx中加入sougou输入法

在Dash中搜索fctix,打开fctix设置,点击`+`添加搜狗输入法

![](/images/2014/1/ubuntu-start1.png)

6.CTRL+Space切换输入法,大功告成!

***

###<span id="Vim">Vim</span>

1.使用apt-get安装

        $ sudo apt-get update
        $ sudo apt-get install vim

2.从源码安装vim

详细参见博文[从源码安装Vim7.4](/2013/12/17/vim-setup.html)

3.说明

由于我的Vim配置中加入了YCM插件,而该插件需要Vim编译时+python标志,并且需要Vim的版本在7.3.584以上,方法1安装的Vim版本貌似不满足要求(可能可以总过更改源来安装Vim7.4?),所以我选择从源码编译安装Vim

***

###<span id="Git">Git</span>

这段直接copy过来了,就不翻译了.应该都能看懂-_-

If you are eager to download the most recent version of Git, it is generally a good idea to install it from the source. 

Quickly run apt-get update to make sure that you download the most recent packages to your VPS.

        $ sudo apt-get update

Prior to installing Git itself, download all of the required dependancies:

        $ sudo apt-get install libcurl4-gnutls-dev libexpat1-dev gettext libz-dev libssl-dev build-essential

Once they are installed, you can download the latest version of Git from the google code page.

        # wget https://git-core.googlecode.com/files/git-1.8.5.4.tar.gz

After it downloads, untar the file and switch into that directory:

        $ tar -zxf git-1.8.5.4.tar.gz
        $ cd git-1.8.5.4

If you want to do a global install, install it once as yourself and once as root, using the sudo prefix:

        $ make prefix=/usr/local all
        $ sudo make prefix=/usr/local install

If you need to update Git in the future, you can use Git itself to do it.

        $ git clone git://git.kernel.org/pub/scm/git/git.git

Add:添加Git指令补全功能,进入git源码目录下:

        $ sudo cp contrib/completion/git-completion.bash /etc/bash_completion.d

***

###<span id="Dropbox">Dropbox</span>

1.下载dropbox deb包

UBuntu 32位：https://www.dropbox.com/download?dl=packages/ubuntu/dropbox\_1.6.0\_i386.deb

2.安装：

        $ cd ~/Downloads
        $ sudo dpkg -i dropbox_1.6.0_i386.deb
        $ cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86" | tar xzf -
        $ ~/.dropbox-dist/dropboxd

***

###<span id="Retext">Retext</span>

        $ sudo add-apt-repository ppa:mitya57
        $ sudo apt-get update
        $ sudo apt-get install retext

Retext配置可以查看[retext wiki configuration](http://sourceforge.net/p/retext/wiki/Configuration%20file/)

***

###<span id="Goagent">Goagent</span>

参考:[Ubuntu/Linux下安装配置Goagent](/2013/11/30/goagent.html)

***

###<span id="翻译词典">翻译词典</span>

1.在Ubuntu软件中心搜索stardict安装辞典(也可以搜索星际译王)或直接在终端中安装：

        $ sudo apt-get install stardict

2.在Dash中可以找到安装的stardict

3.到http://abloz.com/huzheng/stardict-dic/zh_CN/ 下载需要的词库(推荐朗道字典)

4.安装词典:

        $ cd /usr/share/stardict/dic
        $ sudo tar -xjvf ~/Downloads/stardict-langdao-ec-gb-2.4.2.tar.bz2
        $ sudo tar -xjvf ~/Downloads/stardict-langdao-ce-gb-2.4.2.tar.bz2

5.在stardict中的辞典管理可以看到增加的词库

***

###<span id="pywebqq">pywebqq</span>

说明：安装webQQ3.0应用，用于解决ubuntu系统环境下面的QQ聊天解决方法

1.安装依赖包

        $ sudo apt-get install python-notify python-keybinder python-ctypeslib python-webkit python-glade2

2.下载pywebqq,并解压

        $ wget www.junevimer.com/files/pywebqq.tar.gz
        $ tar -xzvf pywebqq.tar.gz

3.配置软件

解压后，需要修改webqqview.py文件中的一个文件引用地址

修改前：

`libsoup = ctypes.CDLL(‘/usr/lib/libsoup-2.4.so.1′)`

修改后：

`libsoup = ctypes.CDLL(‘/usr/lib/i386-linux-gnu/libsoup-2.4.so.1′)`

这里需要修改是因为ubuntu系统路径有点不一样。如果你在第一次运行时说找不到这个文件，你就需要修改。

4.添加桌面启动程序

`$ sudo vi /usr/share/applications/pywebqq.desktop`

        [Desktop Entry]
        Encoding=UTF-8
        Name=PyWebQQ
        Comment=pywebqq1027
        Exec=python /home/june/QQ/pywebqq.py
        Terminal=false
        Type=Application
        Icon=/home/june/QQ/QQ.png
        StartupNotify=true
        Categories=Office;
        MimeType=application/pywebqq;

启动程序请根据实际路径进行修改(Exec和Icon)

这时你就可以到"Dash主页"里面去搜索pywebqq，运行后把它锁定在unity栏里面。

5.运行效果

![](/images/2014/1/ubuntu-start3.png)

pywebqq将webqq脱离浏览器，并在系统托盘提示消息。用户体验不错！

***

## <span id="Configuration">三. 配置</span>

***

### <span id="禁用笔记本电脑触摸板">禁用笔记本电脑触摸板</span>

方法1：命令行方式，得每次用终端输入命令行设置，不方便。

        $ sudo rmmod psmouse //用来禁用触摸板
        $ sudo modprobe psmouse  //用来启用触摸板

想要永久禁用触摸板可以这样：

        $ sudo gedit /etc/modprobe.d/blacklist.conf
        加入一行
        blacklist psmouse

重启电脑，就可禁用触控板了，以后如果想启用就删除该行。


方法2：利用管理软件

touchpad-indicator管理。（真的不错）

首先更新源：

        $ sudo add-apt-repository ppa:atareao/atareao

更新，然后安装 touchpad-indicator：

        $ sudo apt-get update && sudo apt-get install touchpad-indicator 

安装成功后，进入Dash，搜索touchpad-indicator，点击你会发现好像没有反应，以为坑爹了，其实没有。。。你会发现你的系统托盘多了一个触摸板的小图标。。。具体操作就不说了.非常easy的.由于我自己不怎么用触控板,所以直接选择永久禁用触摸板,可以根据自己的习惯来选择禁用方式.

***

###<span id="Dotfiles配置">Dotfiles配置</span>

从Github上恢复Dotfiles配置

        $ git clone git@github.com:JuneWater/dotfiles.git ~/dotfiles && cd ~/dotfiles
        $ ./bootstrap.sh

###<span id="Vim配置">Vim配置</span>

我的Vim配置可查看[Ubuntu12.04下Vim配置之不折腾版](/2013/12/12/vim-config.html):

1.下载我的vim配置文件

        $ git clone --recursive git@github.com:Junevimer/vim.cfg.git ~/.vim
        $ ln -fs ~/.vim/vimrc ~/.vimrc

2.打开Vim,命令行模式下输入:BundleInstall安装vimrc中配置的插件

安装完以后发现vim-powerline不能正常显示符号，解决方法如下：

        $ mkdir ~/.fonts && cd ~/.fonts
        $ git clone git@github.com:eugeneching/consolas-powerline-vim.git

YouCompleteMe插件通过vundle下载好以后还需要手动编译才能使用,具体操作步骤查看[Vim下自动补全插件YouCompleteMe安装与配置](/2013/12/11/youcompleteme.html)

***

##<span id="结语">四. 结语</span>

这篇博文主要做一个备忘,为以后重装系统时能方便些。部分应用可以选用其他版本代替,如搜狗输入法.官方已推出Linux版本,试用还不错!pywebqq没有收发文件和图片的功能,可以选择pidgin来代替.本文就不再做说明了.等以后有比较多的应用或配置需要更新了再写一篇新的吧.

Updated 2014.5.5 23:16
