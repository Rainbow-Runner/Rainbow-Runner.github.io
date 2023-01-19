---
title: IPython的介绍与使用
date: 2022-07-27 21:21:32
tags: 
- Pyhton
- IPython
cover: https://s3.bmp.ovh/imgs/2022/07/27/1dedd50919ec98ff.png
---

# IPthon简介

`IPython`是一个Python的交互式shell，比默认的Python Shell好用的多，支持变量自动补全，自动缩进，支持bash shell命令，内置了许多很有用的功能和函数。

IPyhon提供了两个主要的组件：

1. 一个强大的Python交互式shell
2. 供Jupyter Notebook使用的一个Jupyter内核（IPython Notebook）

IPyhthon的主要功能如下：

1. 运行IPthoy控制台
2. 使用IPython作为系统shell
3. 使用历史输入（history）
4. Tab补全
5. 使用%run命令运行脚本
6. 使用%time命令快速测量时间
7. 使用%pdb命令快速debug
8. 使用Pylab进行交互计算
9. 使用IPython Notebook

# 安装IPython

IPython支持Python2.7版本或者3.3以上的版本

`pip install IPython`

以上这条命令可以自动安装IPython以及它的各种依赖包，但是如果想在Notebook中操作IPython的话，需要安装Jupyter

# 使用IPython的两种方式

Python支持所有的Python的标准输入输出，也就是再IDLE中或者Python Shell中能用的，在IPython中都能够使用，唯一的不同之处时IPython会使用`In[x]`和`Out[x]`表示输入输出，并表示出相应的序号。In和Out时保存历史信息的变量。

> Jupyter Notebook

Jupyter Notebook就类似于IPython的编辑器，它是一个文本编辑器，在电脑本地开了一个服务端，将它运行在浏览器上

windows，mac通用启动命令：Jupyter Notebook

![](https://s3.bmp.ovh/imgs/2022/07/27/15616bf30c676181.png)

# IPython基础功能

IPython快捷键



|  <kbd>Ctrl</kbd>+<kbd>P</kbd>/<kbd>up</kbd>   | 后向搜索命令历史中以当前输入的文本开头的命令 |
| :-------------------------------------------: | :------------------------------------------: |
| <kbd>Ctrl</kbd>+<kbd>N</kbd>/<kbd>down</kbd>  | 前向搜索命令历史中以当前输入的文本开头的命令 |
|         <kbd>Ctrl</kbd>+<kbd>R</kbd>          |      按行读取的方向历史搜索（部分匹配）      |
| <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>V</kbd> |               从剪切板粘贴文本               |
|         <kbd>Ctrl</kbd>+<kbd>C</kbd>          |            中止当前正在执行的代码            |
|         <kbd>Ctrl</kbd>+<kbd>A</kbd>          |               将光标移动到行首               |
|         <kbd>Ctrl</kbd>+<kbd>E</kbd>          |               将光标移动到行尾               |
|         <kbd>Ctrl</kbd>+<kbd>K</kbd>          |          删除从光标开始至行尾的文本          |
|         <kbd>Ctrl</kbd>+<kbd>U</kbd>          |           清除当前行的所有文本译注           |
|         <kbd>Ctrl</kbd>+<kbd>F</kbd>          |            将光标向前移动一个字符            |
|         <kbd>Ctrl</kbd>+<kbd>B</kbd>          |            将光标向后移动一个字符            |
|         <kbd>Ctrl</kbd>+<kbd>L</kbd>          |                     清屏                     |

# IPython高级功能

一些常用的高级功能比如：

- Tab键自动补全
- ？：内省、命名空间搜索
- ！：执行系统命令

以及一系列魔术命令

#### 魔术命令 %以%开始的命令

`%run`：执行文件代码

类似于Cpython中在命令行中`python`+`文件路径`

`%paste`：执行剪切板代码

`%timeit`：评估运行时间   `%%time`

`%pdb`：自动调试

#### IPython常用的魔术命令

|         方法         |                             描述                             |
| :------------------: | :----------------------------------------------------------: |
|      %quickref       |                    显示IPython的快速参考                     |
|        %magic        |                  显示所有魔术命令的详细文档                  |
|        %debug        |             从最新的异常跟踪的底部进入交互调试器             |
|        %hist         |                打印命令的输入（可选输出）历史                |
|         %pdb         |                  在异常发生后自动进入调试器                  |
|        %paste        |                   执行剪切板中的Python代码                   |
|       %cpaste        |       打开一个特殊提示符以便手工粘贴待执行的Python代码       |
|        %reset        |           删除interactive命名空间中的全部变量/名称           |
|     %page OBJECT     |                   通过分页器打印输出OBJECT                   |
|    %run scipt.py     |                在IPython中执行一个Python脚本                 |
|   %prun statement    |      通过cProfile执行statement，并打印分析器的输出效果       |
|   %time statement    |                   报告statement的执行时间                    |
|  %timeit statement   | 多次执行statement以计算系统平均执行时间。对那些执行时间非常小的代码很有用 |
| %who、%who is、%whos |   显示interactive命令空间中定义的变量，信息级别/冗余度可变   |
|    %xdel variable    |    删除variable，并尝试清除其在IPython中对象上的一切引用     |



