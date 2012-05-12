---
layout: post
title: "如何在github上搭建博客"
description: ""
category: github
tags: [git,blog]
---
{% include JB/setup %}

<div id="outline-container-1" class="outline-2">
<h2 id="sec-1">如何在github上搭建博客 </h2>
<div class="outline-text-2" id="text-1">

<p>本教程包含了使用Github免费的pages功能搭建个人博客的方法。Github pages后台使用jekyll生成静态页面，所以我们可以方便地使用markdown，textile，或者emacs的org等语法来撰写博客。
</p>
</div>

<div id="outline-container-1.1" class="outline-3">
<h3 id="sec-1.1">Why choose github? </h3>
<div class="outline-text-3" id="text-1.1">

<dl>
<dt>免费&amp;稳定</dt><dd>
目前在国内找个免费稳定的空间有多难相信大家都知道吧
</dd>
<dt>版本历史</dt><dd>
使用git的最大好处就是你可以像管理源代码一样管理你的BLOG
</dd>
<dt>发布方便</dt><dd>
通过Terminal就可以publish
</dd>
<dt>You are a HACKER!</dt><dd>
the way hackers blog!
</dd>
</dl>
</div>

</div>

<div id="outline-container-1.2" class="outline-3">
<h3 id="sec-1.2">Create a new repository </h3>
<div class="outline-text-3" id="text-1.2">

<p>首先你需要注册一个github的帐号,然后create a new repository，名字必须为&lt;你的USERNAME&gt;.github.com。
</p></div>

</div>

<div id="outline-container-1.3" class="outline-3">
<h3 id="sec-1.3">安装Jekyll-Bootstrap </h3>
<div class="outline-text-3" id="text-1.3">




<pre class="example">$ git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.com
$ cd USERNAME.github.com
$ git remote set-url origin git@github.com:USERNAME/USERNAME.github.com.git
$ git push origin master
</pre>



</div>

</div>

<div id="outline-container-1.4" class="outline-3">
<h3 id="sec-1.4">等待 </h3>
<div class="outline-text-3" id="text-1.4">

<p>因为实际上我们使用的是github pages来搭建博客，所以第一次大概需要10分钟左右初始化。欲了解更多，可以查看:<a href="http://help.github.com/pages">http://help.github.com/pages</a>
</p></div>

</div>

<div id="outline-container-1.5" class="outline-3">
<h3 id="sec-1.5">在本地运行Jekyll(非必须) </h3>
<div class="outline-text-3" id="text-1.5">

<p>下载<a href="http://rubyinstaller.org/downloads/">RubyInstaller</a>安装
下载<a href="http://rubyinstaller.org/downloads/">DevKit</a>解压，按照<a href="https://github.com/oneclick/rubyinstaller/wiki/Development-Kit">DevKit安装说明</a>安装，然后在命令行中使用如下命令安装jekyll:
</p>


<pre class="example">$ gem install jekyll
</pre>




<p>
安装完成后，可以使用如下命令运行jekyll:
</p>


<pre class="example">$ cd USERNAME.github.com
$ jekyll --server
</pre>




<p>
现在你可以通过<a href="http://localhost:4000/">http://localhost:4000/</a>访问本地的blog了
</p></div>

</div>

<div id="outline-container-1.6" class="outline-3">
<h3 id="sec-1.6">配置你的博客信息: </h3>
<div class="outline-text-3" id="text-1.6">

<p>在项目目录底下有文件: <code>_config.yml</code>
</p>


<pre class="example">title : Jekyll Bootstrap
author : 
  name : Name Lastname
  email : blah@email.test
  github : username
  twitter: username
  feedburner : feedname

</pre>



<p>
请根据具体情况修改替换上述信息
</p></div>

</div>

<div id="outline-container-1.7" class="outline-3">
<h3 id="sec-1.7">将原wordpress博文数据导入(非必须) </h3>
<div class="outline-text-3" id="text-1.7">

<p>jekyll本身提供了导入wordpress数据的脚本，但是需要先安装２个插件:
</p>


<pre class="example">$ gem install sequel
$ gem install mysql -- --with-mysql-config=&lt;MySql安装目录&gt;\bin\mysql_config
</pre>



<p>
新建一个import目录，将&lt;MySql安装目录&gt;\lib目录下的libmysql.dll复制到&lt;ruby安装目录&gt;\bin目录下，将&lt;ruby安装目录&gt;\lib\ruby\gems\1.9.1\gems\jekyll-0.11.2\lib\jekyll\migrators下的wordpress.rb脚本复制到import目录下(数据库默认是localhost上的，如果需要自定义，请自行修改wordpress.rb脚本)
注:如果mysql.rb报segement fault，请查阅该文章<a href="http://fuyun.org/2010/01/ruby-mysql-adapter-on-windows/">http://fuyun.org/2010/01/ruby-mysql-adapter-on-windows/</a>
</p>


<pre class="example">$ ruby -r './wordpress.rb' -e 'Jekyll::WordPress.process( "&lt;DB Name&gt;", "&lt;User Name&gt;", "&lt;Password&gt;")'
</pre>



<p>
如果有问题的话，查看core-samples里的2011-12-29-jekyll-introduction.md文件，每个post文件的开头格式需要如下:
</p>


<pre class="example">---
layout: post
title: "title"
description: "details"
category: yourcategory
tags: [article,artist]
---
{% include JB/setup %}
</pre>



</div>

</div>

<div id="outline-container-1.8" class="outline-3">
<h3 id="sec-1.8">将原wordpress留言数据导入(非必须) </h3>
<div class="outline-text-3" id="text-1.8">

<p>使用Wordpress的Export功能导出xml，使用Disqus的Import功能导入。注:jekylly是纯静态网页生成器，本身无法支持评论系统，因为它不支持数据库。所以我们需要第三方的社会化评论云平台，我选择了Disqus，当然你也可以选择国内的类似产品。
</p></div>

</div>

<div id="outline-container-1.9" class="outline-3">
<h3 id="sec-1.9">删除sample post </h3>
<div class="outline-text-3" id="text-1.9">

<p>删除目录<sub>posts</sub>/core-samples
</p></div>

</div>

<div id="outline-container-1.10" class="outline-3">
<h3 id="sec-1.10">撰写博客 </h3>
<div class="outline-text-3" id="text-1.10">

<p>使用命令
</p>


<pre class="example">$ rake post title="your title"
</pre>




</div>
</div>

</div>

<div id="outline-container-2" class="outline-2">
<h2 id="sec-2">可能遇到的问题 </h2>
<div class="outline-text-2" id="text-2">


</div>

<div id="outline-container-2.1" class="outline-3">
<h3 id="sec-2.1">编码问题 </h3>
<div class="outline-text-3" id="text-2.1">

<p>jekyll本地默认是不支持utf-8的,所以对中文支持不友好,解决方法是找到=&lt;Ruby安装目录&gt;\lib\ruby\gems\1.9.1\gems\jekyll-0.11.2\lib\jekyll\convertible.rb=文件,将该文件27行(左右)
</p>


<pre class="example">self.content = File.read(File.join(base, name))
</pre>



<p>
改为
</p>


<pre class="example">self.content = File.read(File.join(base, name), :encoding =&gt; "utf-8")
</pre>




<p>
然后在启动jekyll之前在命令行里输入:
</p>


<pre class="example">set LC_ALL=en_US.UTF-8 
set LANG=en_US.UTF-8 
</pre>




<p>
具体可参考<a href="http://joshua-leung.me/Software Tricks/2012/04/30/jekyll-encoding-issue/">http://joshua-leung.me/Software%20Tricks/2012/04/30/jekyll-encoding-issue/</a>
</p><br/></div>
</div>
</div>

<div id="postamble">
<p class="author"> Author: Connor.Weng
</p>
<p class="date"> Date: 2012-05-07 23:44:00</p>
<p class="creator">HTML generated by org-mode 6.33x in emacs 23</p>
</div>