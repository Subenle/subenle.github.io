---
layout: post
#标题配置
title:  Mac gem install cannot load zlib
#时间配置
date:   2024-08-02
#大类配置
categories: 杂记
#小类配置
tag: jekyll
---

* content
{:toc}


在按照Jekyll[官网教程](https://jekyllrb.com/docs/installation/macos/)在Mac上进行安装，前面一切都很顺利，最后`gem install jekyll` 的时候，报错：

```
➜  ~ gem install zlib
<internal:/Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/core_ext/kernel_require.rb>:85:in `require': cannot load such file -- zlib (LoadError)
	from <internal:/Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/core_ext/kernel_require.rb>:85:in `require'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/util.rb:48:in `inflate'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/source.rb:147:in `fetch_spec'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/resolver/api_specification.rb:92:in `spec'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/resolver/installer_set.rb:98:in `add_always_install'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/dependency_installer.rb:322:in `resolve_dependencies'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/commands/install_command.rb:201:in `install_gem'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/commands/install_command.rb:226:in `block in install_gems'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/commands/install_command.rb:219:in `each'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/commands/install_command.rb:219:in `install_gems'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/commands/install_command.rb:167:in `execute'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/command.rb:323:in `invoke_with_build_args'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/command_manager.rb:185:in `process_args'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/command_manager.rb:149:in `run'
	from /Users/xxx/.rubies/ruby-3.1.3/lib/ruby/3.1.0/rubygems/gem_runner.rb:51:in `run'
	from /Users/xxx/.rubies/ruby-3.1.3/bin/gem:21:in `<main>'
```

但是本地其实已经安装了zlib的，`brew install zlib` 会提示已经安装了。

解决方案：

安装ruby时手动指定zlib的路径。

```shell-script
ruby-install ruby 3.1.3 -- --with-zlib-dir=$(brew --prefix zlib)
```
