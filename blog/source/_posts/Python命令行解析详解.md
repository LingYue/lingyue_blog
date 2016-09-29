---
title: Python命令行解析详解
date: 2016-04-19 19:05:22
tags: Python命令行解析
categories: Python之聚石塔
toc: true
comments: true
---

Python的内建的标准库中提供了一些模块，用于编写用户友好式的命令行接口。本文试图总结Python主流的命令行解析标准库。

> * argparse模块
> * sys.argv
> * getopt模块

<!-- more -->
# argparse模块
**arpgarse**是Python中最强大的一个命令行解析的内建的标准库。程序负责定义好要求的参数，然后**argparse**将从**sys.argv**列表中去解析这些参数。
## 创建解析器
argparse的第一步首先是创建一个解析器，即先创建一个**ArgumentParser**对象，这个对象将用于保存把命令行解析成Python的数据类型所需要的所有的信息。
```python
>>> import argparse
>>> parser = argparse.ArgumentParser(description="Get the baseline data")
>>> print parser
ArgumentParser(prog='', usage=None, description='Get the baseline data', version=None, formatter_class=<class 'argparse.HelpFormatter'>, conflict_handler='error', add_help=True)
```
## 给解析器添加参数
```python
>>> import argparse
>>> parser = argparse.ArgumentParser(description="Get the baseline data")
>>> parser.add_argument('-f', '--file', help='file path')
>>> parser.add_argument('--sum', dest='accumulate', action='store_const',const=sum, default=max,help='sum the integers (default: find the max)')
>>>
"""
输出：
>>> parser.add_argument('-f', '--file', help='file path')
_StoreAction(option_strings=['-f', '--file'], dest='file', nargs=None, const=None, default=None, type=None, choices=None, help='file path', metavar=None)
>>> parser.add_argument('--sum', dest='accumulate', action='store_const',const=sum, default=max,help='sum the integers (default: find the max)')
_StoreConstAction(option_strings=['--sum'], dest='accumulate', nargs=0, const=<built-in function sum>, default=<built-in function max>, type=None, choices=None, help='sum the integers (default: find the max)', metavar=None)
"""
```
给解析器添加参数后，接下来解析参数的时候，解析参数的函数返回的对象中将带有两个属性。
## 解析参数
```python
>>> parser.parse_args()
Namespace(accumulate=<built-in function max>, file=None)
```
ArgumentParser用方法**parse_args()**解析参数，这个函数会检查命令行，把每个参数转换成Python的数据类型，并采取相应的action。绝大部分情况下，解析函数从命令行解析出来的属性建一个简单的**NameSpace**对象。
在脚本中，**parse_args()**的调用一般不带参数，**ArgumentParser**将根据**sys.argv**来自动确定命令行参数。


## 模块的主要对象和主要函数介绍
### ArgumentParser对象的说明
```python
#类原型
class argparse.ArgumentParser(prog=None, usage=None, description=None, epilog=None, parents=[], formatter_class=argparse.HelpFormatter, prefix_chars='-', fromfile_prefix_chars=None, argument_default=None, conflict_handler='error', add_help=True)

#创建一个新的ArgumentParser对象。所有的参数应该以关键字参数传递。下面对参数做简单的介绍，它们是：
"""
prog - 程序的名字（默认：sys.argv[0]），一般根据sys.argv[0]来决定在帮助信息中如何显示程序的名字.
usage - 描述程序用法的字符串（默认：从解析器的参数生成）
description - 参数帮助信息之前的文本（默认：空）
epilog - 参数帮助信息之后的文本（默认：空）,就是帮助信息完了后输出的字符串
parents - ArgumentParser 对象的一个列表，这些对象的参数添加到正在构建的ArgumentParser对象
formatter_class - 定制化帮助信息的类，丰富了帮助信息
prefix_chars - 可选参数的前缀字符集（默认：'-'），就是"-f/--foo"，有些参数可能是'+f'，此时用该参数去指定。
fromfile_prefix_chars - 额外的参数应该读取的文件的前缀字符集（默认：None）
argument_default - 参数的全局默认值（默认：None）
conflict_handler - 解决冲突的可选参数的策略（通常没有必要）
add_help - 给解析器添加-h/–help 选项（默认：True）
"""
```
### add_argument()方法说明
```python
#函数原型
ArgumentParser.add_argument(name or flags...[, action][, nargs][, const][, default][, type][, choices][, required][, help][, metavar][, dest])

#定义应该如何解析一个命令行参数。下面每个参数的简单介绍：
"""
name or flags - 选项字符串的名字或者列表，例如foo 或者-f, --foo。
action - 在命令行遇到该参数时采取的基本动作类型。默认为'store',即保存参数的值。含有计算参数出现的次数，保存参数列表等
nargs - 应该读取的命令行参数数目。
const - 某些action和nargs选项要求的常数值。
default - 如果命令行中没有出现该参数时的默认值。
type - 命令行参数应该被转换成的类型。默认是字符串方式读入命令行参数，有时候可能需要解析成整数，浮点数等类型
choices - 参数可允许的值的一个容器。
required - 该命令行选项是否可以省略（只针对可选参数）。
help - 参数的简短描述。
metavar - 参数在帮助信息中的名字。
dest - 给parse_args()返回的对象要添加的属性名称。
"""
```
### Namespace对象
```python
class argparse.Namespace
```
**parse_args()方法**使用**Namespace**类，用于创建一个保存属性的对象并返回该对象。
这个类设计的很简单，只是**object**这个类的一个可读可打印的字符串的子类。也可以用Python的内建语句**vars()**来以字典的方式查看其属性。
```python
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('-foo')
>>> args = parser.parse_args(['-foo','BAR'])
>>> vars(args)
{'foo': 'BAR'}
```

# getopt模块
getopt模块用于抽出命令行参数和选，是一个命令行参数解析器。兼容C语言的getopt函数。
## 模块的主要对象和主要函数介绍
### getopt.getopt()函数说明
```python
#函数原型
getopt.getopt(args, options[, long_options])
#参数定义：
"""
args：命令行参数列表
options： 短格式参数（string），如：'abc:o'
long_options: 长格式参数（list）,如： ['condition=', 'output-file=', 'testing']
#注解
当args中出现短格式参数或者长格式参数列表没有的参数的时候；或者当短格式参数和长格式参数中定义了必须有的参数，即含有":"或者"="的时候，但是args中没出现该参数的时候，均会报错，异常类为：getopt.GetoptError: option -x not recognized。
"""
```
**getopt.getopt()**函数解析方法：

 - 短格式参数：当字母后面带有":"的时候，表示整个参数是需要带参数的，函数在解析的时候，会把这个命令行参数中紧随-o后面的参数解析为-o的参数。字符后面不带":"的表明该字符是不需要参数的，即不会把紧随这个字符后面的参数解析为该短格式字符的参数。
 - 长格式参数列表：当列表中某个元素的后面后面有"="的时候，表明这个字符串必须有参数，不带参数会报错，getopt会把紧随这个字符串后面的参数解析为这个字符串的参数；如果没有不带"="的参数表明这个字符串是没有参数的，即不会解析紧随这个字符串后面的字符串串为参数。

**getopt.getopt()**函数返回值（返回两个list）：

 - 第一个参数opts：list的元素是每个参数选项和其value的元组
 - 第二个参数args：除了有用参数外的其他命令行的输入，也是一个list

**示例**
短格式参数：
```python
>>> import getopt
>>> args = '-a -b -cfoo -d bar a1 a2'.split()
>>> args
['-a', '-b', '-cfoo', '-d', 'bar', 'a1', 'a2']
>>> opts, args = getopt.getopt(args,'abc:d:')
>>> opts
[('-a', ''), ('-b', ''), ('-c', 'foo'), ('-d', 'bar')]
>>> args
['a1', 'a2']
```
长格式参数：
```python
>>> import getopt
>>> args = '--condition=foo --testing --output-file abc.def -x a1 a2'.split()
>>> args
['--condition=foo', '--testing', '--output-file', 'abc.def', '-x', 'a1', 'a2']
>>> optlist, args = getopt.getopt(args, 'x', [
...     'condition=', 'output-file=', 'testing'])
>>> optlist
[('--condition', 'foo'), ('--testing', ''), ('--output-file', 'abc.def'), ('-x', '')]
>>> args
['a1', 'a2']
```
程序中使用该模块举例：
```python
import getopt
import sys

def usage():
    print("Usage:%s [-h|-o|-v] [--help|--output] args...." %sys.argv[0]);

def main():
    try:
        opts, args = getopt.getopt(sys.argv[1:], "ho:v", ["help", "output="])
    except getopt.GetoptError as err:
        print str(err)
        usage()
        sys.exit(2)
    output = None
    verbose = False
    for o , a in opts:
        if o == '-v':
            verbose = True
        elif o in ("-h", "--help"):
            usage()
            sys.exit()
        else:
            assert False, 'unhandled option'

if __name__ == "__main__":
    main()
```

[argparse模块](http://python.usyiyi.cn/python_278/library/argparse.html)
