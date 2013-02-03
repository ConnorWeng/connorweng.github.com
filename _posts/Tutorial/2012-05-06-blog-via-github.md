---
layout: post
title: "如何在github上搭建博客"
description: ""
category: Tutorial
tags: [Github,Blog]
---
{% include JB/setup %}

本教程包含了使用Github免费的pages功能搭建个人博客的方法。Github pages后台使用jekyll生成静态页面，所以我们可以方便地使用markdown，textile，或者emacs的org等语法来撰写博客。

## Why choose github?

* 免费&稳定

  目前在国内找个免费稳定的空间有多难相信大家都知道吧

* 版本历史

  使用git的最大好处就是你可以像管理源代码一样管理你的BLOG

* 发布方便

  通过Terminal就可以publish

* You are a HACKER!

  the way hackers blog!

## Create a new repository

首先你需要注册一个github的帐号，然后create a new repository，名字必须为`你的USERNAME.github.com`。

## 安装Jekyll-Bootstrap

    $ git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.com
    $ cd USERNAME.github.com
    $ git remote set-url origin git@github.com:USERNAME/USERNAME.github.com.git
    $ git push origin master

## 等待

因为实际上我们使用的是github pages来搭建博客，所以第一次大概需要10分钟左右初始化。欲了解更多，可以查看:[http://help.github.com/pages](http://help.github.com/pages)

## 在本地运行Jekyll(非必须)

下载[RubyInstaller](http://rubyinstaller.org/downloads/)安装
下载[DevKit](http://rubyinstaller.org/downloads/)解压，按照[DevKit安装说明](https://github.com/oneclick/rubyinstaller/wiki/Development-Kit)安装，然后在命令行中使用如下命令安装jekyll:

    $ gem install jekyll

安装完成后，可以使用如下命令运行jekyll:

    $ cd USERNAME.github.com
    $ jekyll --server

现在你可以通过[http://localhost:4000/](http://localhost:4000/)访问本地的blog了

## 配置你的博客信息:

在项目目录底下有文件: `_config.yml`

    title : Jekyll Bootstrap
    author :
      name : Name Lastname
      email : blah@email.test
      github : username
      twitter: username
      feedburner : feedname

请根据具体情况修改替换上述信息

## 将原wordpress博文数据导入(非必须)

jekyll本身提供了导入wordpress数据的脚本，但是需要先安装２个插件:

    $ gem install sequel
    $ gem install mysql -- --with-mysql-config=MySql安装目录\bin\mysql_config

新建一个import目录，将`MySql安装目录\lib`目录下的`libmysql.dll`复制到`ruby安装目录\bin`目录下，将`ruby安装目录\lib\ruby\gems\1.9.1\gems\jekyll-0.11.2\lib\jekyll\migrators`下的`wordpress.rb`脚本复制到import目录下(数据库默认是localhost上的，如果需要自定义，请自行修改wordpress.rb脚本)
注:如果mysql.rb报segement fault，请查阅该文章[http://fuyun.org/2010/01/ruby-mysql-adapter-on-windows/"](http://fuyun.org/2010/01/ruby-mysql-adapter-on-windows/)

    $ ruby -r './wordpress.rb' -e 'Jekyll::WordPress.process( "<DB Name>", "<User Name>", "<Password>")'

如果有问题的话，查看core-samples里的2011-12-29-jekyll-introduction.md文件，每个post文件的开头格式需要如下:

    ---
    layout: post
    title: "title"
    description: "details"
    category: yourcategory
    tags: [article,artist]
    ---
    {% include JB/setup %}

## 将原wordpress留言数据导入(非必须)

使用Wordpress的Export功能导出xml，使用Disqus的Import功能导入。注:jekylly是纯静态网页生成器，本身无法支持评论系统，因为它不支持数据库。所以我们需要第三方的社会化评论云平台，我选择了Disqus，当然你也可以选择国内的类似产品。

## 删除sample post

删除目录`posts/core-samples`

## 撰写博客

使用命令

    $ rake post title="your title"

## 可能遇到的问题

### 编码问题

jekyll本地默认是不支持utf-8的，所以对中文支持不友好，解决方法是找到`Ruby安装目录\lib\ruby\gems\1.9.1\gems\jekyll-0.11.2\lib\jekyll\convertible.rb` 文件，将该文件27行(左右)

    self.content = File.read(File.join(base, name))

改为

    self.content = File.read(File.join(base, name), :encoding => "utf-8")

然后在启动jekyll之前在命令行里输入:

    set LC_ALL=en_US.UTF-8
    set LANG=en_US.UTF-8

具体可参考[http://joshua-leung.me/Software Tricks/2012/04/30/jekyll-encoding-issue](http://joshua-leung.me/Software%20Tricks/2012/04/30/jekyll-encoding-issue/)

### 中文列表markdown无法解析

原因是jekyll默认markdown引擎对中文支持非常不友好，workaround的解决方案如下:

我们把jekyll的markdown引擎更换为rdiscount，在`_config.yml`中添加:

    markdown: rdiscount
