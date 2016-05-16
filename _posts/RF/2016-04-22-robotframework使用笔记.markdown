---
layout:     post
title:      "robotframework使用笔记"
subtitle:   " \"最开始接触robotframework框架时，一些记录的要点拿出来分享\""
date:       2016-04-22 00:30:00
author:     "daveze"
header-img: "img/post-bg-2016.jpg"
tags:
    - Robot Framework
---

首先，疑问几乎都可以从[官方文档](http://robotframework.org/robotframework/latest/RobotFrameworkUserGuide.html)或[中文译版](http://pan.baidu.com/s/1i4uUo2x)找到解决办法

## 1.case中要用的各关键字名称一样，用例怎么执行？
原文解释：

> If several resource files have a user keyword with the same name, they must be used so that the keyword name is prefixed with the resource file name without the extension (for example, myresources.Some Keyword and common.Some Keyword). Moreover, if several resource files contain the same variable, the one that is imported first is taken into use.

就是说如果名字一样，要使用它们就必须在这些重名的关键字前加上库名，例如common和myresources库中都有叫Some Keyword的关键字，那么应该这样用：
`myresources.Some Keyword ` 、`common.Some Keyword`

## 2.所用关键字在导入的多个内/外部库中均有定义，此关键字怎么执行？
原文：

> ### 处理同名关键字 
Robot Framework 中所用的关键字分为库关键字 和 用户自定义关键字. 前者出自 标准库 或者 扩展库, 
后者既可以在关键字所在文件中创建也可以从源文件中引用。当一个测试用例中用到很多关键字时，难免
这些关键字中会有重名的关键字，本节重点讲述如何处理由于关键字重名而可能造成的冲突。 

> ### 关键字作用域 
  当用到一个同名关键字时，Robot Framework 将要判断哪个关键字在它的作用域中拥有最高的优先级。一个
关键的作用域取决于它是如何创建的，有如下几种方式: 
1、当前文件中创建的关键字。这类关键字最常用而且在所有同名关键字中拥有最高的优先级。 
2、一个源文件(resource)创建的关键字且直接或间接地被其它源文件引用。这类关键字拥有第二高的优先级。 
3、扩展测试库的关键字。当其它地方没有同名关键字时，这这些关键字将被使用。但是，如果在标准库中
有同名的关键字的话将会有警告提示出现。 
4、标准库中的关键字。这类关键字拥有最低的优先级。 

> ### 显式指定关键字 
  仅仅靠关键字的作用域来判断并不是完美的解决方案，因为很有可能在几个不同的库或者源文件里面存有
同名关键字，这样的话 Robot Framework 会机械地按最高优先级来选择关键字，但是有些情况很可能不是用
户所希望看到的。这种情况下，一个可行的解决方案是使用关键字的全名，也就在关键字前用资源文件名
或库名和点分界符作为前缀。 
  对于库关键字来说，长格式意味着使用“库名. 关键字名”的格式。例如，OperatingSystem library 中的 Run
关键字可以描述为 OperatingSystem.Run, 从而避免了与其它地方有 Run 关键字的冲突。如果一个测试库在
一个模块或一个包中，那么模块或包的全名必须加上（例如，com.company.Library.Some Keyword）。如果一
个自定义名字通过命名语法库给定，这个特定的名字也必须用关键字全名中。 
  像库名一样，也要在关键字全名中指定资源文件，这里资源文件名指的是除去扩展名以外的文件基本名。
例如，myresources.html 源文件中关键字 Example 应该这样描述：myresources.Example。注意，当存在同名
资源文件有同名关键字时这种语法将不起作用。在这种情况下，同名资源文件和同名关键字至少改一个。
像正常关键字一样，全名关键字对大小写、空格、下滑线不敏感；
> ### 指定关键字库的搜索顺序
Robot Framework 从 2.1 版起可以用 BuiltIn 库中的 Set Library Search Order 关键字来设定可用测试库的搜索
优先级。当遇到多个关键字的情况，Robot Framework 按设定的搜索优先级来执行关键字。当优先级最高的
库中有所需关键字，则该库中的关键字被执行，若关键字在所有库中找到，则测试执行失败。 
在关键字库的搜索顺序被设定的情况下就没有必要再使用像 LibraryName.Keyword 长格式的关键字名了。
这样就能方便地通过 Set Library Search Order 关键字修改测试库的优先级从而使用不同库中的关键字。这样
就避免了因多个库中有同名关键字而造成的冲突。 
关键字库搜索顺序在测试集级有效这意味着该搜索顺序只有在设定了的库中有效。 


## 3.Resource使用：
3.1.resouce是一个文件，可以在test Directoy、Suit下建立；
3.2.resoure就是用户自己定义的一系列高级别关键字、变量的合集，如果要使用resource，需要和library一样先导入；
3.3.test suit导入resource之后，用户可以在该suit下的case中使用这个resource内的关键字和变量；
3.4.与library不同的是：resource中实现的关键字只能基于library中的已有关键字来封装，而不能实现代码层级的关键字，但是，resource内的关键字在执行时优先级高于library；

### 自定义关键字：

- 新建1个resource文件，在该resource文件下新建关键字：输入关键字名称和参数；
- 编辑该关键字，写入该关键字需要执行的操作（例如我封装某一条case执行的所有步骤）；
- 在需要使用该关键字的suit下导入它，然后在某条case中使用该关键字，即可达到这个关键字所封装的执行动作的效果；
例如：

```
*** Keywords ***
post_sdk_event
    [Arguments]    ${jsonfile}    ${ip}
    ${json}    Get file    ${jsonfile}    #从系统路径获取json文件
    ${headers}    Create Dictionary    Content-Type=application/x-www-form-urlencoded    #定义一个头文件
    Create Session    sdk_post    ${ip}    ${headers}    #创建连接
    ${response}=    Post Request    sdk_post    /crash    data=${json}    #发送post请求，带上json数据
    Should Be Equal As Strings    ${response.status_code}    200    服务器返回状态错误，status!=200    #断言
```

### 自定义变量：
- 自定义的变量可以在RIDE可视化工具中对resource文件右键“add”，有sclar和list两种类型可以添加
- 或直接编辑resource文件，在“*** Variables ***”标签写下变量名和内容：

```
*** Variables ***
${app_id}         eW4ADBg2
```

## 4.关键字执行失败继续执行
**4.1.由关键字控制：**

> 通常任何一个关键字执行失败了，整个测试用例将停止执行。这种行为缩短了测试执行时间，阻止后续的
关键字被挂起，或者其他由被测程序本身状态不正确引发的问题出现。这或许是个缺点，因为后续的关键
字也许会提供更多的被测系统的信息。 
在 RF2.5 之前，在关键字执行失败后，只有使用 BuiltIn 库中的关键字 Run Keyword And Ignore Error 和 Run 
Keyword And Expect Error，测试执行才不会立即终止。使用这些关键字增加了用例的复杂性。在 RF2.5 中，
增加了下面的特性，将使在关键字失败的情况下，用例可继续执行：

>### 由关键字产生的特别的失败 
  库关键字使用异常的方式来上报失败，但它也可以使用具体的异常来通知 RF 核心框架当关键字执行失败的
时候，测试执行继续进行。在测试库 API 章节，异常的产生方式将有介绍。 
当关键字中有一个或多个执行失败，用例执行完后，整个测试用例将被标记失败，如果存在一个以上的失
败，所有的失败信息都将会列举在最终失败信息中： 
Several failures occurred: 
1) First error message. 
2) Second error message ... 
在一个可继续的失败后，如果还有关键字执行失败，那么测试执行同样会停止。所有的失败信息也会列举
在最终失败信息中。失败的关键字中可以得到返回值，将它付给变量后，是一个 Python 的空（None）。 

>### 关键字 Run Keyword And Continue On Failure 
BuiltIn 库中的关键字 Run Keyword And Continue On Failure 可以将任何失败都转换为可继续执行的失败。这
是由框架本身的关键字所保证的。 

>### 在teardown中，默认设置为可继续执行的失败 
  为了保证清理环境的操作能够执行到，所有用例和测试集的 teardown 中，都被设置为可继续执行的失败。
这意味着，teardown 中的所有关键字都会被执行到。 

>### 用例使用模板时，所有顶级的关键字都会被执行
  在使用测试模板时，为了确保所有的组合都要被测试到，数据行中的数据将会全部被执行。在这一应用中，
可继续执行的失败将被限制在顶级的关键字中，如果内部的非可继续执行的失败产生，测试执行将停止。 

**4.2.在关键字的源代码中控制：**

> ### 设定不管失败继续执行 
从 Robot Framework 2.5 开始，也可以让整个测试执行在出现了 failure 的时候继续执行。将一个特殊的属性ROBOT_CONTINUE_ON_FAILURE（值为 True）设置到关键字抛出的异常中，就能很容易的达到这种目的。请看下面展示的例子： 

```
class MyContinua MyContinuableError(RuntimeError):
     ROBOT_CONTINUE_ON_FAILURE = True 
```

> ### 设定失败停止测试执行
从 Robot Framework 2.5 开始，可以通过让一个测试用例失败来停止整个测试运行。将一个特殊的属性ROBOT_EXIT_ON_FAILURE（值为 True）设置到关键字抛出的异常中，就能很容易的达到这种目的。请看下面展示的例子： 

```
class MyFatalError (RuntimeError):
     ROBOT_EXIT_ON_FAILURE = True 
```

## 5.指定执行测试用例
可以通过指定test suit和test case、指定标签(Tag)来选择要执行的用例；
分别在命令行中使用“--suit”、“--test”来指定suit和testcase，“--include (-i)”、“--exclude(-e)”来指定和排除哪些是要执行或不执行的Tag；
例如一个指定执行case的示例：
`pybot --test lanuch_app D:\DEV\robot_framework\Artisan\artisan_sdk\SmokingTest.txt`

## 6.如何决定测试库的控制范围
Robot Framework 试图让测试用例之间保持独立：默认地，它为每个测试用例创建测试库的新实例。然而，这种方式并不总是合适的，因为有时候测试用例应该能够共享一个共通的实例(or状态)。 
**被类实现的测试库**有一个内部状态，该状态可以通过关键字和库构造器的参数来改变，这个状态是通过类的一个属性 “ROBOT_LIBRARY_SCOPE”来设定的，其作用是保证测试库可以控制新的库什么时候被创建。这个属性必须是字符串，并且它只有以下三个值：
- TEST CASE:为每个测试用例创建一个新的实例，这是默认的。
- TEST SUITE:为每个测试集创建一个新的实例。
- GLOBAL:在整个测试执行过程中，只有 1 个实例被创建。它被所有测试用例和测试集共享。由模块创建的库总是 global 的。
建议测试库还要有个特殊的关键字来清除这个状态。这个关键字然后能被使用，比如，在一个测试集的 setup 或者 teardown 被使用，这样的话，就能保证下一个测试集的测试用例能在一个已知的状态下启动。比如，SeleniumLibrary 用 GLOBAL 范围来使不同的测试用例使用同一个浏览器而不需要每次都重新打开，同时，它也有 Close All Browsers 关键字来很容易地关闭所有浏览器。

一个例子：

```
class  ExampleLibrary:  
    ROBOT_LIBRARY_SCOPE = 'TEST SUITE'  
    def __init__(self):
         self._counter = 0  
    def count(self):
         self._counter += 1         
         print self._counter  
    def clear_counter(self):
         self._counter = 0 
```

## 7.testcase中存储变量：
变量名称格式应该是${arg}，放在每条步骤最前面。如下：

```
*** Test Cases ***
android_session
    ${dict}    Create Dictionary     Content-Type=application/x-www-form-urlencoded
    Create Session    sdk    http://192.168.100.200:8080/crash    ${dict}
    ${response}    Post Request    sdk    ${android_session}    ${dict}
    Should Be Equal As Strings    ${response.status_code}    200
```

其中，`${dict} ` 和`${response}`都是变量，存储它们后面语句的结果；

## 8.获取路径和读取文件：
- 读取文件： 
调用“OperatingSystem”这个内置库，使用“Get file”关键字，例如：
```
${crash_json}    Get file    E:\\Crash-Analysis\\crash_report\\android_crash.json
```
- 获取当前的路径：
使用${CURDIR}变量，例如：

<img class="shadow" src="/img/in-post/RF/robot framework使用笔记/RF的内建变量.png">

## 9.打印调试信息
可以使用“Log”关键字，其后可以是任意想打印的信息

## 10.robot framework提取json中的信息：
- RequestsLibrary 的
 - To Json关键字：将字符串格式化为json
- Collections 库的：
 - Get Dictionary Values关键字可以提取json串中的所有value（只能提取第一层）
 - Get Dictionary Keys关键字可以提取json中的所有key（只能提取第一层）
 - Get Dictionary Items关键字可以提取json中的完整key-value
 - Get From Dictionary 关键字可以提取json中指定key的value值（只能提取第一层）
- 如果要单独提取多层json中的子层信息，可以连续使用Get From Dictionary关键字，先提取第一层的value，再在这个value中提取第二层的value，以此类推，例如：

多层json为：

```
{"code":0,"data":{"result":{"issueTotal":20,"crashTotal":113,"earliestTime":1451012853739,"time":-28800000,"latestTime":1452739947011,"reporterTotal":6}}}
```

提取该json中issueTotal的value(即20)的关键字为：

```
${responsedata}    To Json     ${response.content}    #将返回的信息格式化为json
${data}=    Get From Dictionary     ${responsedata}    data    #提取data的value
${result}=    Get From Dictionary     ${data}    result    #提取result的value
${issueTotal}=    Get From Dictionary     ${result}    issueTotal    #提取issueTotal的value
```

## 11.变量的有效范围:
1.直接在testsuit或在resource中定义的变量是默认在所有case中有效的，但是这些变量往往是静态的，无法动态地改变；例如在case1中获取到一个变量的值，需要在case2中使用，这时候可以将case1中的这个变量设置为全局有效或suit下有效，使用Builtin库的Set Global/Suite/Test Variable关键字，实例如下：

```
${init_crash_total}    get_issueCrashReporterTotal    roBMdnpl
Set Suite Variable    ${init_crash_total}
```

这时候在case2中就可以直接用${init_crash_total}变量了

## 12.列表List的创建:
使用Builtin库中的Create List关键字可以创建一个列表，例如：

```
${current_issueCrashReporter_list}	Create List	1	2	3
Log	${current_issueCrashReporter_list}			
```

结果是：${current_issueCrashReporter_list}=[1,2,3]

## 13.自定义关键字
自定义关键字不仅可以在已有的库关键字之上进行封装，同时可以为自定义关键字定义入参和返回值，在RIDE中的目标关键字Settings选项中配置；例如：

```
*** Keywords ***
get_CrashTotal_current
    [Arguments]    ${appkey}    #定义入参appkey
    Create Session    event_query    http://192.168.100.200:10086
    ${response}=    Get Request    event_query    /issueCrashReporterTotal?app%3D${appkey}    #接收appkey
    Should Be Equal As Strings    ${response.status_code}    200
    Log    ${response.content}
    ${responsedata}    To Json    ${response.content}
    ${data}=    Get From Dictionary    ${responsedata}    data
    ${result}=    Get From Dictionary    ${data}    result
    ${issueTotal}=    Get From Dictionary    ${result}    issueTotal
    ${crashTotal}=    Get From Dictionary    ${result}    crashTotal
    ${reporterTotal}=    Get From Dictionary    ${result}    reporterTotal
    [Return]    ${issueTotal}    ${crashTotal}    ${reporterTotal}    #以list形式返回3个值
```

## 14.RF中的字符串拼接;
若要拼接两个字符串，直接把这两个字符串放在一起就可以了，例如：string1string2

## 15.RIDE的bug：
有时候去除勾选用例复选框后，但实际该用例没有去除勾选，导致这条用例一直会被执行，也找不出原因，容易引起困惑，此时需要重启下ride就好了。

## 16.导入库或资源原则：
始终在最原始的位置导入，即在哪个文件中调用的，就在哪个文件中导入所使用的library或resource。
执行test suite的所需要的资源由其所在目录的__init__.robot提供。
