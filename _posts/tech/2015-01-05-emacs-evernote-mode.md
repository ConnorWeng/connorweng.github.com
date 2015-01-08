---
layout: post
title: "在windows+emacs中使用evernote-mode编写印象笔记中文note"
description: ""
category: tech
tags: [emacs]
---
{% include JB/setup %}

从[github/evernote-mode](https://github.com/pymander/evernote-mode)上clone下来，放到emacs的load-path目录下

到evernote-mode/ruby目录下，运行命令：

    ruby setup.rb

安装Evernote OAUTH Ruby gems：

    gem install evernote_oauth

配置emacs：

    (require 'evernote-mode)
    (setq evernote-enml-formatter-command '("w3m" "-dump" "-I" "UTF8" "-O" "UTF8"))

测试：

    evernote-browser

报错：

    error in process sentinel: enclient.rb exited abnormally with code 1
    d:\Tools\Ruby193\bin\ruby.exe: No such file or directory -- /usr/bin/enclient.rb (LoadError)

解决报错，将evernote-mode.el中的

    (defvar enh-enclient-command "/usr/bin/enclient.rb"
      "Name of the enclient.rb command")

改为:

    (defvar enh-enclient-command "enclient.rb"
      "Name of the enclient.rb command")

报错：

    Evernote::EDAM::Error::EDAMUserException (parameter: authenticationToken errorCode: BAD_DATA_FORMAT)

解决报错，访问[Evernote](https://dev.evernote.com/doc/articles/authentication.php#devtoken)，得知：

    Developer tokens

    If you are using the API to access only your Evernote account for personal use, you don't need to go through the OAuth authorization process. Instead, you can get a developer token that will allow you to access your account through the API.

    Note that, at this time, developer tokens are not supported by the iOS SDK. Instead, the iOS SDK provides a layer of abstraction on top of the usual OAuth implementation in our other SDKs, making implementing OAuth in your application much easier.

    Learn about developer tokens

我们了解到，如果只是个人使用的话，只要dev token就够了，由于我们是要在生产环境使用的，所以必须获得production dev token，访问[申请](https://app.yinxiang.com/api/DeveloperToken.action)，配置emacs：

    (setq evernote-developer-token "<token>")

还是报同样的错误，突然我意识到使用的是印象笔记，不是evernote，所以enclient.rb中可能需要修改地址才行，我们在`<ruby安装目录>/bin`下找到enclient.rb（运行ruby setup.rb的时候会把该文件拷至该目录下，mac下应该在/usr/bin目录下），将：

    EVERNOTE_HOST       = "www.evernote.com"

改为：

    EVERNOTE_HOST       = "www.yinxiang.com"

还是有报错:

    No version identifier, old protocol client?

经过仔细阅读文档，发现原来还是EVERNOTE_HOST写错了，继续修改enclient.rb中EVERNOTE\_HOST：

    EVERNOTE_HOST       = "app.yinxiang.com"

测试：

    evernote-browser

通过，可以正常显示tags了。但是在新建note的时候还有问题，如果note的标题是中文的话会报错：

    "\xE5" from ASCII-8BIT to UTF-8

很明显是转码出现了问题，根据直觉，修改`enclient.rb:548`，在CreateNoteCommand类的exec_impl方法中修改代码

    def exec_impl
      Formatter.to_ascii @title, @content, *@tag_names
      @title.force_encoding Encoding::UTF_8  # added by Connor Weng

这下新建中文标题的note成功了，但是如果输入中文内容的话，还是会报类似的编码错误。很明显，内容也需要转码，于是又修改代码`enclient.rb:588`：

    def exec_impl
      # Formatter.to_ascii @title, @notebook_guid, @content, *@tag_names
      Formatter.to_ascii @title, @notebook_guid, *@tag_names  # removed parameter content
      @content.force_encoding Encoding::UTF_8  # force encoding

以为万事大吉了，继续尝试保存中文内容，还是同样的错误，非常奇怪。通过阅读enclient.rb的代码，并且打开debug级别的日志输出，分析日志，我们得知报错来自于`<ruby安装目录>/lib/ruby/gems/1.9.1/gems/evernote-thrift-1.25.1/lib/thrift/bytes.rb:81:in 'encode'`，根据日志中的stack backtrace，追踪到文件`<ruby安装目录>/lib/ruby/gems/1.9.1/gems/evernote-thrift-1.25.1/lib/thrift/protocol/base_protocol.rb:275`，在write_type中加入日志输出，得知原来转码失败的原因在于note的title，保存的时候title需要重新转码，于是回到`enclient.rb:599`，加入转码代码：

    if @title
      note.title = @title
    else
      note.title = old_note.title
    end
    note.title.force_encoding Encoding::UTF_8  # added by Connor Weng

终于搞定！可以顺利使用emacs编写中文note。
