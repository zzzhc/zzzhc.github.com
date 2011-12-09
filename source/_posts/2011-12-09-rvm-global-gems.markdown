---
layout: post
title: "rvm global gems"
date: 2011-12-09 19:07
comments: true
categories: ruby
---

用rvm安装多了ruby版本后，一些经常用的gem需要在多个版本下都重装一次，很费事。不过还好rvm早有[解决方案](http://beginrescueend.com/gemsets/initial/)，可以通过编辑~/.rvm/gemsets/global.gems来添加全局的gemsets, 比如必不可少的bundler

```
bundler
```

也可以指定版本

```
bundler -v~>1.0.21
```
