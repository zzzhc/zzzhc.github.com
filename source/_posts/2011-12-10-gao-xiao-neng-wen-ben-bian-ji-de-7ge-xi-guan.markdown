---
layout: post
title: "高效能文本编辑的7个习惯"
date: 2011-12-10 12:59
comments: true
categories: vim
---

[Seven habits of effective text editing](http://www.moolenaar.net/habits.html)

## 编辑单个文件

### 1. 快速移动

* 查找光标下单词在当前文件的其它位置
  1. \* 向后查找
  2. \# 向前查找
* 搜索文本, /pattern
* 使用%跳转到对应块的结尾/开头，安装[matchit](http://www.vim.org/scripts/script.php?script_id=39)插件效果更好
* 使用gd跳转到变量定义

掌握更多高效编辑命令的三个基本步骤:
* 留意编辑过程中的重复动作和花时间较多的地方
* 找出一个可以更快完成这些操作的命令
* 反复练习，直到形成习惯，不需要思考靠直觉就能敲出命令

### 2. 不要重复输入两次

* 善用.命令重做前一个修改
* 使用自动补全, 安装[supertab](https://github.com/ervandew/supertab)后可用tab键补全
* 录制宏, qa开始录制，q结束宏, @a重放宏

### 3. 自动修复拼写错误

* abbr
* syntax hightlight

## 编辑多个文件

### 4. 很少只在单独一个文件上工作

* ctags, Ctrl+] 跳转到定义处
* :grep, :cn

### 5. 与其它程序协同工作

```
:!command
```

### 6. Text is structured

自动化编译，修改过程
```
:make
:set errorformat=xxx
```

## 磨快你的矩

### 7. 养成习惯

这一点最重要，花大把时间找到合适的命令但很快就忘掉是很不划算的。


