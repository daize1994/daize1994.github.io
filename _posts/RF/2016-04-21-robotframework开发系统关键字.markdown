---
layout:     post
title:      "robotframework开发系统关键字"
subtitle:   " \"robotframework开发系统关键字\""
date:       2016-04-21 12:00:00
author:     "daveze"
header-img: "img/post-bg-2016.jpg"
tags:
    - Robot Framework
---

## 背景：
第三方库无法满足测试需求

## 实现：

1.首先，建立一个文件夹，用于存放所有开发的文件，该文件夹名必须和你想定义的Library名完全一致，这里取名为：My_PostRqruestLibrary；

2.新建一个py文件，用于开发系统关键字，文件名随意，在该文件内就是写我们的关键字（方法）了，这里我们命名为 post_test_android_crash.py，内容大致：

```
#-*-coding:utf-8-*-

import httplib,urllib
import time
import json
import sys

class MyCustomLibrary():
    '''
    这个是一个自定义库
    '''    
    def send_crash(self,jsonfile):
        '''
        发送一条crash日志，该关键字需要传入一个json文件
        '''
        for i in range(1):
            #获取当前路径
            dir = sys.path[0]            
            report = open(jsonfile)
            headers={
                    'Accept': '*/*',
                    'Connection': 'keep-alive',
                    'X-Requested-With': 'XMLHttpRequest',
                    'Accept-Encoding': 'gzip, deflate',
                    'Accept-Language': 'zh-cn',
                    'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8',
                    'Connection': 'keep-alive',
                    
                }
            conn = httplib.HTTPConnection("192.168.100.200",8080)
            conn.request(method="post",url="/crash",body=report,headers=headers)
            response = conn.getresponse()
            #print response.read()
            print response.status,response.reason
            conn.close()

```

注：

- 可以有多个类，类里面的方法将会是关键字，类名这里不做要求，符合规范即可

- 以'''this is a decription'''样式描述的将是库和关键字的说明

- 方法中的入参数将作为关键字的必要参数（除非在方法中指定了默认值）

- 为了使他人更了解相应文件的作用，该文件可以存放在子文件夹中

- 其它与正常开发python代码一致


3.新建一个version.py文件，用于描述版本号，内容为：
```
VERSION='0.1'
```

4.新建一个__init__.py文件，用于初始化库，其内容大致为：

```
#-*-coding:utf-8-*-
from post_test_android_crash import MyCustomLibrary    #导入已经写好的class
from version import VERSION     #导入已经写好的VERSION

__version__=VERSION    #初始化刚才定义的version

class My_PostRqruestLibrary(MyCustomLibrary):    # 新建个类“My_PostRqruestLibrary”，继承在post_test_android_crash.py中已经写好的类“MyCustomLibrary”
    ROBOT_LIBRARY_SCOPE='GLOBAL'    #此句作用是指该库运行的时候会作用在全局。    # 设置这个类中的关键字全局有效
```

注：

- 这里的类名必须和文件夹名完全一致，也就是要和Library名字一致

- 如果有多个关键字类，一起继承即可（python支持继承多个父类）

- 如果关键字类存放在子文件夹中，需要从子文件夹中导入，例如：`from keywords import *`导入keywords文件夹中的所有文件

- __init__文件请存在最外层文件夹目录


5.完毕，将这个文件夹整体复制到***/Lib/site-packages文件夹或者在导入库时直接使用库路径（推荐后者）


6.现在就可以像导入正常的Library一样导入自己写的库


7.文件夹内容为：
<img class="shadow" src="/img/in-post/RF/rf开发系统关键字-目录内容.png" />

8.这并不是唯一的，也不是必须的方式，只是一个相对规范的自定义关键字库，实际请按需选择。

    
