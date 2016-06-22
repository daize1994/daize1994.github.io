---
layout:     post
title:      "robotframework中使用Remote远程库"
subtitle:   " \"初探Remote远程库的使用\""
date:       2016-06-21 00:30:00
author:     "daveze"
header-img: "img/post-bg-2016.jpg"
tags:
    - Robot Framework
---

## 为什么要用？

- 当测试库必须存在于被测服务器上时，例如接口测试时，接口地址为服务器内网ip，我们在本地执行是访问不到的。

- 当一个测试既需要jybot运行，又需要pybot执行时。



## 原理：

将所用的测试库"包裹"在一个名为Remote库中，导入robot framework并可通过ip和端口连接上使用。

其中：

- 测试库，是真正执行测试的库

- Remote库，是一个远程库，其中可以包装任何测试库，它可能存放于远程服务器，也可以存在于本地。

- 远程服务器，是启动Remote库的载体，可能是本机也可以是任意可访问的服务器。



## 使用：

1.在已经安装了robotframework的远程服务器上，安装robotremoteserver：

`pip install robotremoteserver`


2.在任意目录下新建一个py文件，作为启动远程库的脚本，这里假设是start_remote.py，内容如下：

```

from robotremoteserver import RobotRemoteServer

from my_test_remote_library import myClass # 导入当前机器下的测试库



myclass= myClass()

RobotRemoteServer(myclass) # 加载测试库

``` 


- 在上述代码中加载测试库时，测试库对象（第一个参数）是必要参数，可选参数有如下：

| Argument        | Default           | Explanation  |
| ------------- |:-------------:| :-----:|
| library      |  | Test library instance or module to host. Mandatory argument.|
| host      | 127.0.0.1      |   Address to listen. Use ``'0.0.0.0'`` to listen to all available interfaces. |
| port | 8270      |    Port to listen. Use ``0`` to select a free port automatically. Can be given as an integer or as a string. |
| port_file |None  |   File to write port that is used. ``None`` means no such file is written.|
|  allow_stop    |    True                |    Allow/disallow stopping the server using ``Stop Remote Server`` keyword.


- 也可以直接在该启动脚本 中编写测试库


3.准备好需要被包装的测试库，即步骤2启动远程库脚本中的my_test_remote_library，测试库可以是其它第三方库，也可以是自定义库，库的内容就是robot framework标准的库格式(类和方法)，例如下面的：

```
class myClass( ):

    def add(self, a, b):

        return (int(a) + int(b))


    def sub(self, a, b):

        return a - b
```

4.包装好后，就要启动这个remote库，执行下面的脚本：

`python start_remote.py `

不带参数启动，就是默认是启动脚本中定义的ip和端口，如果启动脚本也没有定义，则是127.0.0.1:8270


5.启动后，就可以在RF中导入Remote库并使用其中包装好的关键字，例如：

```
Library    Remote   45.58.54.95:1000    #从45.58.54.95:1000机器上导入Remote远程库

*** Test Cases ***
example
    ${res1}    add    1    11
    ${res2}    sub    20    11
    log to console    ${res1}
    log to console    ${res2}
```
这样，就可以在远程服务器使用基本测试库包装一个远程库，并在本地RF脚本中使用它。


6.我们也可以自定义一个需要jython执行的测试库，用jython语言去实现，只不过在启动脚本时用`jython start_remote.py`


参考：[PythonRemoteServer](https://github.com/robotframework/PythonRemoteServer)