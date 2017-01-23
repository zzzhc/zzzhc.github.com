---
layout: post
title: "Ack - Better Grep"
date: 2011-12-16 11:17
comments: true
categories: misc
---

Ack是一个给程序员用的grep, 使用perl regular expressions, 而不是POSIX/GNU subset.

## why ack?

* 使用perl regular expressions, 忘掉grep那套不人性的pattern吧
* 速度快, 默认只搜索程序代码文件
* 自动忽略.svn, .git, CVS这类目录, 默认递归搜索子目录
```bash
# with grep
$ grep pattern $(find . -type f | grep -v '\.svn')
or
$ grep -R --exclude-dir .svn pattern
# with ack
$ ack pattern
```
* 支持搜索指定文件类型
```bash
# 只搜索ruby code
$ ack --ruby pattern
```

## install

```bash
# mac
$ brew install ack
# ubuntu
$ sudo apt-get install ack-grep
```

## integrate into vim

* install ack.vim
* set grepprg=ack in vimrc

## custom ack

Ack默认包含很多filetype => extensions的设置，但新出现的语言不一定支持。好在ack提供--type-add TYPE=.EXTENSION[,.EXT2[,...]], --type-set TYPE=.EXTENSION[,.EXT2[,...]]来扩展。经常用的可以加到~/.ackrc里, 我的.ackrc:

```bash
$ cat ~/.ackrc
--type-add
ruby=.haml,.ru
--type-add
css=.scss,.sass,.less
--type-add
js=.coffee
```

