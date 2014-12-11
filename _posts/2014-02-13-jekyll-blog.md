---
layout: post
title: 使用Github和Jekyll搭建自己的博客
description: ""
category: "tools"
tags: [jekyll, github] 
---
{% include JB/setup %}

##前言

本文主要介绍搭建本博客的全部过程,对于一个不懂JS,CSS和HTML的前端小白来说,Fork别人的jekyll theme,然后修改成自己喜欢的样子,过程着实不容易.大概花了两天时间吧,在比较了不同jekyll theme的代码,参考官方说明文档之后,总算弄出个喜欢的样子了.

##在Github上搭建blog的好处

* 空间免费，github托管，稳定又安全；
* 允许本地服务器调试，脱离网络写文章毫无压力，因为可以使用git命令同步来管理文章，版本控制妥妥的，对技术人员来说，一键恢复，实在是神物；
* 可以绑定顶级域名；
* 文章用markdown编写,再也不用为排版困扰；

##Github上的个人主页和项目主页

个人主页：Github为每一个用户分配了一个二级域名username.github.com ，用户为自己的二级域名创建主页很简单，只需要在Github下创建一个名为username.github.com 的版本库，并向其master分支提交网站静态页面即可，其中网站首页为index.html。

项目主页：为项目启用项目主页很简单，只需要在项目版本库中创建一个名为gh-pages的分支，并向其中添加静态网页即可，通过网址http://username.github.com/helloworld （假设项目名为helloworld）访问。

##使用Github Pages功能创建个人博客

1.登陆Github，创建一个名为junevimer.github.com的版本库,(junevimer为自己的github账户名,讲junevimer改为自己的用户名.下同)

2.点击Setting，选择一个自己喜欢的模板，最后点击发布public按钮。

3.耐心等待一段时间（不超过10分钟），登陆http://junevimer.github.com ，会发现自己的个人博客已经生成。

##使用其他模板创建个人博客

1.执行如下命令克隆`git@github.com:zhuoqun/blog.git`版本库到本地(本博客就是在这个主题上修改的)

        $ git clone git@github.com:zhuoqun/blog.git ./junevimer.github.com

2.修改远程仓库为自己的Blog

        $ cd junevimer.github.com
        $ git remote set-url origin git@github.com:Junevimer/junevimer.github.com.git

2.向Github提交修改后的博客

        $ git push -f

4.耐心等待一段时间后，再次打开网址http://username.github.com 看看博客变样了没~

##本地预览博客

在下载了别人的博客模板后，经过自己的修改，上传到Github后，不能生成对应的网页，有可能是修改得不正确。如果不确定自己修改得是否正确，那么就需要自己在本地预览，确保没有错误后再上传到Github。

想要本地预览，就需要使用jekyll生成静态网站，在终端下执行下面的命令安装jekyll,当然先需要安装jekyll的依赖包。

        $ sudo apt-get install ruby1.9.3
        $ sudo gem install jekyll

使用jekyll创建网站，会在网站根目录下生成site目录，上传到Github中时，请先删除site目录。生成成功后，使用jekyll作为内置的Web服务器（默认在端口4000开启Web服务）

        $ cd username.github.com
        $ jekyll serve

在浏览器中输入http://localhost:4000 查看自己修改后的网站。

        如果运行jekyll serve出现错误:
        /var/lib/gems/1.8/gems/commander-4.1.5/lib/commander/runner.rb:365:in `require_program': program version required (Commander::Runner::CommandError)
        from /var/lib/gems/1.8/gems/commander-4.1.5/lib/commander/runner.rb:364:in `each'
        from /var/lib/gems/1.8/gems/commander-4.1.5/lib/commander/runner.rb:364:in `require_program'
        from /var/lib/gems/1.8/gems/commander-4.1.5/lib/commander/runner.rb:52:in `run!'
        from /var/lib/gems/1.8/gems/commander-4.1.5/lib/commander/delegates.rb:7:in `run!'
        from /var/lib/gems/1.8/gems/commander-4.1.5/lib/commander/import.rb:10
        from /usr/local/bin/jekyll:19
        /usr/lib/ruby/vendor_ruby/1.8/rubygems/custom_require.rb:36:in `gem_original_require': no such file to load -- json (LoadError)
        from /usr/lib/ruby/vendor_ruby/1.8/rubygems/custom_require.rb:36:in `require'
        from /var/lib/gems/1.8/gems/jekyll-1.3.0/bin/../lib/jekyll/filters.rb:2
        from /usr/lib/ruby/vendor_ruby/1.8/rubygems/custom_require.rb:36:in `gem_original_require'
        from /usr/lib/ruby/vendor_ruby/1.8/rubygems/custom_require.rb:36:in `require'
        from /var/lib/gems/1.8/gems/jekyll-1.3.0/bin/../lib/jekyll.rb:43
        from /usr/lib/ruby/vendor_ruby/1.8/rubygems/custom_require.rb:36:in `gem_original_require'
        from /usr/lib/ruby/vendor_ruby/1.8/rubygems/custom_require.rb:36:in `require'
        from /var/lib/gems/1.8/gems/jekyll-1.3.0/bin/jekyll:7
        from /usr/local/bin/jekyll:19:in `load'
        from /usr/local/bin/jekyll:19

        可通过 sudo gem install json 解决(UBuntu Flatform)

##域名绑定

根据github pages的[官方主页](http://pages.github.com)说明,要想域名绑定，在代码库的根目录下放一个CNAME文件就行了，文件里写上想要绑定的域名，然后在域名DNS管理的后台网站建立指向就行。如果想把页面绑定到二级域名，需要创建一个CNAME指向。如果要把页面绑定到顶级域名，需要创建一个A指向。不能用CNAME指向顶级域名，否则会造成冲突。

假设我要把页面绑定到二级域名www.junevimer.com ,具体操作如下:

1.在junevimer.github.com添加一个CNAME文件,添加一行

`www.junevimer.com`

2.在域名解析管理中.如图所示解析:

![](/images/2014/2/jekyll-blog.png)

3.过一段(大概上图中TTL)时间后访问www.junevimer.com ,看看是否生成自己的Blog

##主题Fork说明:

本主题Fork自https://github.com/zhuoqun/blog ,@zhuoqun使用的是Jekyll Bootstrap自带的Twitter主题,简单修改了一下,其主要修改如下:

1.首页显示最新的一篇文章。

2.针对中文调整了样式（行距，字体大小等）。

3.修改了永久链接的结构。

4.增加了“关于”页面。

##主题修改说明:

在@zhuoqun的基础上,我做了一些修改,具体修改可以查看源码commit log,主要修改如下:

1.顶部添加Google搜索栏

2.添加多说评论系统(比较适合中国用户使用)

3.修改添加Google Analytics

4.Post页面右侧添加当前博文分类

5.添加社会化分享,JiaThis

6.添加Font-awesome

