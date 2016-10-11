---
layout:     post
title:      "robotframework自定义关键字获取字典中指定key的值"
subtitle:   " \"robotframework自定义关键字获取字典中指定key的值\""
date:       2016-10-10 12:00:00
author:     "daveze"
header-img: "img/post-bg-2016.jpg"
tags:
    - Robot Framework
---

## 背景：
测试时，需要从接口返回的json数据中获取指定值，如果用rf的Get From Dictionary关键字，会显得很繁琐

## 直接上代码：
```
#!/usr/bin/python
# -*-coding:utf-8-*-

import httplib
import json
import requests
import sys
import time
import urllib2

# 将编码设置为utf-8，防止RF报编码错误
reload(sys)
sys.setdefaultencoding('utf8')


def __getvalue__(self, dictionary, key):
        for k in dictionary:
            # print k
            if k == key:
                self.value.append(dictionary[k])
            if isinstance(dictionary[k], dict):
                self.__getvalue__(dictionary[k], key)
            if isinstance(dictionary[k], list):
                for i in range(len(dictionary[k])):
                    if isinstance(dictionary[k][i], dict):
                        self.__getvalue__(dictionary[k][i], key)

def getValueFromDictByKey(self, dictionary, key):
    """
    从一个字典中根据key获取其value,返回一个所有符合条件的value列表
    :param dictionary:待查找的字典
    :param key:目标key
    """
    self.value = []
    # 如果不是字典,则转成字典
    if isinstance(dictionary, list):
        dictionary = {"results": dictionary}
    self.__getvalue__(dictionary, key)
    return self.value if self.value else [0]
```

### 使用：
先把上面的代码封装成系统关键字(如有不懂，参考<a href="/2016/04/21/robotframework%E5%BC%80%E5%8F%91%E7%B3%BB%E7%BB%9F%E5%85%B3%E9%94%AE%E5%AD%97/">这里</a>)，然后在RF代码中调用即可，调用示例：

```
${response}=    To Json    ${response.content}
${issue_id}=    GetValueFromDictByKey    ${response}    issue_id
${max_timestamp}=    GetValueFromDictByKey    ${response}    max_timestamp
${count}=    GetValueFromDictByKey    ${response}    count
[Return]    ${issue_id}    ${max_timestamp}    ${count}

```

