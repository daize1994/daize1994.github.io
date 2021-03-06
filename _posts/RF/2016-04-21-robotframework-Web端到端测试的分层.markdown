---
layout:     post
title:      "robotframework-端到端测试的分层"
subtitle:   " \"robotframework在进行端到端测试时，进行用例分层的思路\""
date:       2016-04-21 12:00:00
author:     "daveze"
header-img: "img/post-bg-2016.jpg"
tags:
    - Robot Framework
---

## 测试套件层：
一个套件代表一个测试用例集，其包含多条用例，这些用例共同构成了一个功能块，是对测试用例的归类，便于管理和查看。例如修改应用名字和修改应用图标，删除应用的功能构成了“编辑应用信息”功能块。

## 用例层：
是一个具体功能点的所有测试执行步骤，由多个动作层关键字组成，其内容可能是多个动作，以及断言。例如测试“编辑应用名称”的用例由：获取当前应用名称，点击编辑按钮，输入新名称=当前名称+“new”，点击确定，再次获取应用名称，断言是否生效

## 动作层：
关键字形式，这一层存放测试过程每一步执行的最小动作，执行动作由元素和其方法组成，例如“点击登录按钮”的动作，由登录button的元素和其click方法组成。再例如“获取第一篇文章标题text”的动作，由文章标题的元素和gettext方法，以及retrun的结果组成。

## 元素层：
变量形式，依据不同页面分类存放自动化测试过程所有用到的elements，例如：登录button的xpath元素

## 数据层：
存放测试过程需要用到的数据，可以是变量，列表，或者字典，也可以是外部文件；由动作层统一调用。

---

### 这样做的好处：
- 数据层、元素层统一管理数据和页面元素，当所用测试数据或页面元素变化，直接修改这里即可，无需到所有用到此数据和元素的动作中去修改。
- 动作层封装每一个最小动作并用更贴近业务的方式命名，更加容易理解，更容易在测试失败时定位出错步骤，可以被用例层复用，避免相同的操作被写多条相同的语句在不同的用例中。
- 用例层中的步骤是各个很贴近业务的关键字名称组合，阅读上更接近于传统手工测试方式。
- 测试套件层是为了分类，避免用例过多难以管理。
