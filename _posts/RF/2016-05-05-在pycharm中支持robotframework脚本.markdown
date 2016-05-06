---
layout:     post
title:      "在pycharm中支持robotframework脚本"
subtitle:   " \"如何在pycharm IDE中方便地编写并执行robot framework测试用例\""
date:       2016-05-05 17:00:00
author:     "daveze"
header-img: "img/post-bg-2016.jpg"
tags:
    - Robot Framework
---

## Why：

- Robot Framework支持.robot、.txt、.tsv格式的脚本文件，在各种网上资源，大家都默认介绍使用RIDE这个IDE去编写脚本，这个IDE由民间大神开发，可以较好的支持脚本开发，但是我在使用过程中发现Bug很多，尤其在OS X系统上。

- 重要的是，我觉得它会误导很多新手以为RIDE就是使用RobotFramework框架必须的一部分。

- 然而，并不是，它也只是一款IDE，如果你不用它，甚至可以用记事本等任何文本编辑器来编写你的robot脚本。

- 但是，它有它的好处，例如可以直观地看到测试用例的组织结构，可以方便地执行测试，可以以填表方式编写脚本等等。所以，这里，希望是你在充分认识了RIDE并且受够了它之后，再来完成下面的操作。



## 目的：

用习惯了Pycharm，所以我更愿意在Pycharm中使用它。下面介绍下如何让Pycharm支持RF脚本，并在RF中方便地运行测试。



## 安装插件：

1.打开Pyhcarm，打开菜单栏>Perferences>打开Plugins>点击Browse reponsitories：

<img class="shadow" src="/img/in-post/RF/在pycharm中支持robotframework脚本/安装插件-图1.png" >

2.输入IntelliBot，搜索并安装：

<img class="shadow" src="/img/in-post/RF/在pycharm中支持robotframework脚本/安装插件-图2.png" >

3.安装后，重启pycharm，打开任意.robot格式的脚本，正常应该可以识别了：

<img class="shadow" src="/img/in-post/RF/在pycharm中支持robotframework脚本/安装插件-图3.png" >

4.但是会发现，第一次安装，并不支持.txt格式的脚本文件，此时需要这么做：

 4.1.打开Perferences>Editor>File Types>找到Robot Feature>在Registered Patterns中添加.txt

<img class="shadow" src="/img/in-post/RF/在pycharm中支持robotframework脚本/安装插件-图4.1.png" >

 4.2.保存即可。

5.此时可以方便地编写脚本，支持自动补全，关键字跳转等。

## 执行测试：

1.脚本编写好了，想执行测试怎么办？可以用pybot命令直接在Pycharm的Terminal中执行。

2.也有更方便的办法：不每次输入命令，每次直接在脚本中选择用例或suite直接执行，下面介绍：

 2.1.配置选择执行单个case：打开Perferences，在Tools中选择External Tools，点击+号新建，按如下信息配置：

<img class="shadow" src="/img/in-post/RF/在pycharm中支持robotframework脚本/执行测试-图1.png" >
其原理就是执行一个pybot命令，只不过这个命令执行的内容是按照我们鼠标的选择来动态变化的。

 2.2.配置执行testsuite：方法一样，新建一个Tools，配置内容如下：

<img class="shadow" src="/img/in-post/RF/在pycharm中支持robotframework脚本/执行测试-图2.png" >

 2.3.配置指令所选Tag，也是和步骤1一样的，只不过把-t换成-i即可。

3.使用配置好的tools执行测试：

 3.1.如下图，选择一个用例执行：

<img class="shadow" src="/img/in-post/RF/在pycharm中支持robotframework脚本/执行测试-图1.1.png" >

 3.2.在testsuite内空白位置或选择该testsuite文件右键执行，即可执行该测试套件：

<img class="shadow" src="/img/in-post/RF/在pycharm中支持robotframework脚本/执行测试-图1.2.png" >

## 完毕！