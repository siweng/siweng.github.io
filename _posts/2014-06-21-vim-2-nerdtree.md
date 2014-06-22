---
layout: post
published: true
title: Vim每日一篇：为Vim搭建目录树
date: 2014-06-21
tags: vim nerdtree
---

###前言
默认的Vim打开之后是没有目录树窗口的，这对我们日常项目开发十分不方便，但目录树是可以通过插件nerdtree实现，安装该插件之后，通过快捷键可以快速的浏览目录，变更节点，编辑文件，还可以通过其Bookmark功能来实现工程和项目的管理。
在安装该插件之前，需要安装一个vim的插件管理器pathogen.vim，它通过动态改变`runtimepath`,vim在`runtimepath`目录列表下查找加载插件，让你更方便的安装和管理插件。查看vim当前的`runtimepath`可以通过在vim进入底行命令模式输入`:set  runtimepath`命令来查看
{% highlight bash %}
mkdir -p ~/.vim/autoload ~/.vim/bundle
curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim
{% endhighlight %}
autoload目录是vim默认加载插件的目录，用来存在pathogen.vim插件，bundle目录是用来存放其它插件，
添加以下命令到~/.vimrc 开启pathogen插件(.vimrc若不存在，创建一个)
{% highlight bash %}
execute pathogen#infect()
{% endhighlight %}

###安装Nerdtree
进入正题，[nerdtree](https://github.com/scrooloose/nerdtree)插件安装非常方便，在终端执行命令
{% highlight bash %}
cd ~/.vim/bundle
git clone https://github.com/scrooloose/nerdtree.git
{% endhighlight %}
为nerdtree添加调用快捷键，添加以下命令到`~/.vimrc`文件
{% highlight bash %}
"添加快捷键映射到nerdtree"
map <C-n> :NERDTreeToggle<CR>
{% endhighlight %}
重启vim，按ctrl+n键呼起nerdtree窗口，默认在vim视图的左侧

