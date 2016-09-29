---
title: Python葵花宝典
date: 2016-04-17 19:48:10
tags: Python基础
categories: Python之聚石塔
toc: true
comments: true
---

基础经济决定上层建筑，扎实的基础知识是优秀工程师的基石，本文记录一些零碎的Python的基础知识。
> * 类、元类
> * 函数
> * Python的编码、乱码问题
> * Python的__call__（前后双下划线）函数
> * 识别编码的办法
> * super函数
> * os模块的文件对象操作部分
> * 文件对象的读写

<!--more-->

# 类

## **什么是类？类的特点是什么？**
 -  **类：** 类就是一组用来描述描述如何生成对象的代码段。**类就是能创建出类实例的对象。**
 - **类的特点：**Python中的类也是对象，只要使用关键字**class**，Python的解释器执行的时候就会在内存中创建一个对象，对象名就是类名。**这个对象（类）自身拥有创建对象（类实例）的能力**，这就是为什么称这个对象为类的原因。但是Python中的类本质上还是一个对象，所以Python中类可以：
- 将类赋给一个变量
- 可以拷贝类
- 可以为他增加属性
- 可以把类作为函数参数去传递
- 类是对象，因此可以动态的去创建类
- 示例：
    ```python
    class Foo(object):
        pass
    Foo.new_aut = 'lingyue'
    foo = Foo()

    def myClass(name):
        if name == 'foo':
            class Foo():
                pass
            return Foo
        else:
            class Bar():
                pass
            return Bar
    s = myClass('foo')
    x = myClass('bar')
    ```

 - 类既然也是对象，那么这个类对象是谁创建的呢？
    -  创建类的**元类**。
## **什么是元类？元类的特点是什么？** [解释元类个人认为最好的文章链接](http://blog.jobbole.com/21351/)

 - **元类：**元类就是创建类（对象）的“东西”，即**元类就是类的类，可以理解为元类就是类工厂**。
 记录开始

## **"__metaclass__"属性是什么？这个属性有什么作用？**

 - **"__metaclass__"**属性的怎么用？

# 函数
## 函数参数传递说明
### 传递参数带星号**(*)**
在传递不确定个数的参数的时候，我们可能会使用（*args，**kwargs）来传递，那么使用星号和不使用星号去给函数传递参数有什么区别？
```python
>>> def func(*args):
...     print args
...     print args[0]
...
>>> a = ('lingyue','jiangtao')
>>> func(a)
(('lingyue', 'jiangtao'),)
('lingyue', 'jiangtao')
>>>
>>> func(*a)
('lingyue', 'jiangtao')
lingyue
>>>
>>> func('lingyue','jiangtao')
('lingyue', 'jiangtao')
lingyue
>>>
```
上面举例可以看出，当不使用星号给函数传递参数的时候，你是怎么传递的，那么函数接受到的参数就是什么样子的，比如上例子中的**func(a)**，传递进来后，函数接受到的就是你所传递的参数，例子中使用的是元组的方式去传递的；那么你用list做参数，函数还是会把参数作为元组去处理，就是说*args永远接受到的是一个元组：
```python
>>> func(['lingyue','jiangtao'])
(['lingyue', 'jiangtao'],)
['lingyue', 'jiangtao']
>>> func(*['lingyue','jiangtao'])
('lingyue', 'jiangtao')
lingyue
```

那么当我们使用引用传参**(func(*a))**的时候，会发生什么呢？
由上面的例子可以看到，使用引用传参的时候，函数解析器实际会把元组给解包，解析成len（a）个参数，它会把你传进去的参数解包成一个元组，而不是原封不动的接受。
所以：
```python
>>> func(*a)
('lingyue', 'jiangtao')
lingyue
```
等价于
```python
>>> func('lingyue','jiangtao')#表示传递两个参数
('lingyue', 'jiangtao')
lingyue
```

# Python的编码、乱码问题

## 为什么产生编码问题？
计算机只能处理01编码，如果要处理非数字的数据结构，只能把其他数据结构转化为01编码，计算机才能处理。美国人在最初发明计算机的时候就把自己的一些字母，如a,b,A,D,以及一些特殊字符、数字等编码到计算机中，一共是127个字母被编译进去。**这个编码就是ASCII编码，**比如大写的A是65等。但是当计算机传入中国的时候，中国语言博大精深，*（计算机在设计时采用8个比特（bit）作为一个字节（byte）*，一个字节要处理中文显然是不够的，而且还不能与ASCII编码重复，至少需要两个字节才能处理一个汉字。所以，中国人就制作了中文的编码方式，就GB2312编码，把中文编译进去。
那么，世界上有形形色色的语言，各国可能都有自己的语言，比如日文、韩文、法文等，各国都有自己的标准，这怎么通信，就比如货币如果没有比率做等价交换，各国就无法通币了，显然没有一个标准就会有各种冲突。于是乎，unicode就产生了。unicode就是一个各国编码的一个通用的桥梁，好比现在美元就是世界通用的货币，英语就是世界通用的语言，*（谁让美国佬强大呢，再过几十年，世界通用标准说不定就中国了）*。有了unicode这个通用编码标准，世界任何国家就可以通过这个编码进行转化，进而进行交流。
**以上就是编码问题的由来和解决办法的知识的简单普及。**

## unicode、UTF-8
我们知道unicode就是一个通用的编码标准了，有了unicode，所有的乱码问题，语言不通问题就解决了。
但是这就万事大吉了？No，上面我们介绍了，如果只是英语，也就是字符串，ASCII是够用的，美国人就是这么用的，只不过，中文博大精深，需要更多的编码字节罢了，至少需要两个字节。我们中国人自古以来，勤俭节约，计算机中什么最珍贵？肯定是CPU和内存嘛，那么问题来了，有个学渣，语文课不好好上，写篇作文，由于大多数汉字学了后都还给老师了，此贼通篇文章绝大多数都是拼音替代，也就是字符串，只有个别是汉字。
此时如果你都用2个或者以上的字节去编码，显然不在信息存储上、信息传输上不合算呐，大多数比特位都是填充的0，要是你真这么编码，老师一定骂你是败家子了。
此时聪明的中国人就想办法了，出现**UTF-8**编码，**所谓的UTF-8编码就是一种可变长度的unicode编码，**
UTF-8编码把一个unicode字符根据不同的数字大小编码成1-6个字节，比如字符就用1个字节，简单中文就用2-3个字节，只有很生僻的字符才会被编码成4-6个字节，此时如果你把有大量字符中夹杂少量汉字的作文传输给老师的时候，就能节省不少的空间，存储和传输都节省不少呢。

注意：
通常python的文件的开头会这么写：
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
```
第一行是遥告诉Linux系统，这是一个Python的可执行程序，window会忽略这行。
第二行注释是为了告诉Python解释器，请按照UTF-8编码来读取源码，否则你在文件中可能就会看到乱码问题。
**以上就是Unicode编码和UTF-8编码的简单介绍。**

## 编码、解码
Python紧跟潮流，也支持Unicode编码方式。Python 2.x的默认编码方式是ASCII方式。Python 3.x默认编码方式是unicode方式。str和unicode都是basestring的子类。严格意义上说，str其实是字节串，它是unicode经过编码后的字节组成的序列。对UTF-8编码的str'汉'使用len()函数时，结果是3，因为实际上，UTF-8编码的'汉'=='\xE6\xB1\x89'。
```python
str  -> decode('the_coding_of_str') -> unicode
unicode -> encode('the_coding_you_want') -> str

#s是code_A的str
s.decode('code_A').encode('code_B') #不同编码转换,使用unicode作为中间编码
```
**unicode才是真正意义上的字符串，由字符组成；str是经过unicode经过编码后的字节组成的一个序列。所以不要对str使用编码(encode)，不要对unicode使用解码(decode),**虽然即使你使用了也不会报错，但是不建议这么做。

字符串在Python内部的表示是unicode编码，因此，**在做编码转换时，通常需要以unicode作为中间编码，即先将其他编码的字符串解码（decode）成unicode，再从unicode编码（encode）成另一种编码。** 但是，Python 2.x的默认编码格式是ASCII，就是说，在没有指定Python源码编码格式的情况下，源码中的所有字符都会被默认为ASCII码。也因为这个根本原因，在Python2.x中经常会遇到UnicodeDecodeError或者UnicodeEncodeError的异常。

### 解码（decode）：
decode函数的作用是将其他编码的字符串转换成unicode编码，如str1.decode('gb2312')，表示将gb2312编码的字符串str1转换成unicode编码。

### 编码（encode）:
encode函数的作用是将unicode编码转换成其他编码的字符串，如str2.encode('utf-8')，表示将unicode编码的字符串str2编码成utf-8编码。

encode、decode语法：
```python
str.encode(encoding='UTF-8',errors='strict') #参数
Decoded string #返回结果

Str.decode(encoding='UTF-8',errors='strict') #参数
Decoded string # 返回结果
```
编码解码使用举例：
```python
#! /usr/bin/env python
#! -*-coding:utf-8-*-
Str = "this is string example....wow!!!"
Str = Str.encode('base64', 'strict')

print "Encoded String: " + Str
print "Decoded String: " + Str.decode('base64', 'strict')


#输出结果：
Encoded String: dGhpcyBpcyBzdHJpbmcgZXhhbXBsZS4uLi53b3chISE=
Decoded String: this is string example....wow!!!
```

### 编码问题常见异常总结：

```python
1、SyntaxError: Non-ASCII character #Python源文件中有非ASCII字符，但是同时没有声明编码格式。
2、UnicodeDecodeError #将其他编码格式转换成unicode编码，但是字符本身的编码格式和decode传入的不相符。如下面：
#!/usr/bin/python
# -*- coding: utf-8 -*-
s = '中文'
s.decode('gb2312') # UnicodeDecodeError: 'gb2312' codec can't decode bytes in position 2-3: illegal multibyte sequence
print s
3、UnicodeEncodeError # 错误的使用decode和encode方法会出现这种异常，比如：使用decode方法将Unicode字符串转化的时候，如下面：
#!/usr/bin/python
# -*- coding: utf-8 -*-
s = u'中文'
s.decode('utf-8') # UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)
print s
```

### 编码问题常见处理方法总结：


 - 遵循PEP标准，声明编码格式

    ```python
    #!/usr/bin/env python
    # -*- coding: <encoding name> -*-
    ```

 - 使用u'中文'替代'中文'

    ```python
    str1 = '中文'
    str2 = u'中文'

    #以上两种字符串变量的主要不同就是字符串编码不一致，str1的编码和Python声明的编码方式是一致的；str2的编码就是unicode格式。如果你的字符串变量中存在非ASCII字符，那么最好使用str2方式，这样你就不需要使用decode解码了，可以直接encode成你所需要的编码格式了，或者直接操作字符串了，避免异常情况出现。
    ```

 - 使用人为的改变文本编码方式
    ```python
    import sys
    reload(sys)
    sys.setdefaultencoding('utf-8')
    #以上三行代码可算是解决编码问题的万能钥匙了，几乎能解决所有的编码问题，但是它会带来一些负面影响。
    ```
 - 先解码成unicode，再编码成所需的格式，decode early, unicode everywhere, encode late
    ```python
    #! /usr/bin/env python
    #! -*-coding:utf-8-*-
    Str = "this is string example....wow!!!"
    Str = Str.encode('base64', 'strict')

    print "Encoded String: " + Str
    print "Decoded String: " + Str.decode('base64', 'strict')
    ```

**[sys.setdefaultencoding('utf-8')的弊端说明的最好的文章](http://blog.ernest.me/post/python-setdefaultencoding-unicode-bytes?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)**

# Python的call（前后双下划线) 函数

##概念
Python中有很多小函数，很有意思，也很有用，比如今天讲的__call__函数。
我们知道在调用一个类型的时候，必须先实例化，然后用对象去调用。但是如果这个类型中实现了__call__函数，那么这个类型就成为可调用的了，也就说，__call__函数相当于重载了括号运算符。

## 举例

 - 类型中不实现__call__的时候，如下：

```python
class Func(object):
    def __init__(self, g):
        self.g = g

    def call(self, t):
        return (self.g * t**2) / 2

e_dpm = Func(2)
seq =  e_dpm.call(22)
```

 - 类型中实现了__call__函数的时候，调用可以如下：

```python
class Func(object):
    def __init__(self, g):
        self.g = g

    def __call__(self, t):
        return (self.g * t**2) / 2

e_dpm = Func(2)
#一下两种方式都可以调用
seq =  e_dpm(22)
seq = e_dpm.__call__(22)
```

## __call__函数总结

对象通过提供__call__(slef, [,*args [,**kwargs]])方法可以模拟函数的行为，即x(arg1, arg2...) 等同于调用x.__call__(self, arg1, arg2)

# 识别编码的方法
有时候在我们没有明确被告知某个字符串、文件等的编码的时候，我们需要识别编码。使用编码方式、编码范围等判断是可行的，比如常用字表等，可惜的是识别率太低了。
这时候我们强大的Python就出来解决全人类了。python提供了一个第三方库，**chardet库**可以用来识别编码。
## 识别编码库chardet库
chardet库是个Python的第三方库，下面我们就来看看用法，很简单：

```python
import chardet

with open('/path/file.txt',r) as f:
    data = f.read()
print chardet.detect(data)

#输出：返回值是一个dict，一个是检测的可信度，一个是检测到的编码
{'confidence': 0.99, 'encoding': 'utf-8'}

#当然也可以直接检测字符串等容器的编码
>>> import chardet
>>> s = 'zhong国'
>>> print chardet.detect(s)
{'confidence': 0.505, 'encoding': 'utf-8'}
```
**备注：[chardet官网](http://chardet.readthedocs.io/en/latest/api/chardet.html#module-chardet)**

# 关于print
## print输出文本中的中文
Python中包含一个中文字符串的列表、字典、文件等的时候，直接print的时候，会出现以下情况：
```python
>>> dicts = {"asdf": "学习Python，我们是认真的"}
>>> print dicts
{'asdf': '\xe5\xad\xa6\xe4\xb9\xa0Python\xef\xbc\x8c\xe6\x88\x91\xe4\xbb\xac\xe6\x98\xaf\xe8\xae\xa4\xe7\x9c\x9f\xe7\x9a\x84'}

#显然上述信息在处理的时候很不方便，此时只需要：

>>> dicts = {"asdf": "学习Python，我们是认真的"}
>>> print dicts
{'asdf': '\xe5\xad\xa6\xe4\xb9\xa0Python\xef\xbc\x8c\xe6\x88\x91\xe4\xbb\xac\xe6\x98\xaf\xe8\xae\xa4\xe7\x9c\x9f\xe7\x9a\x84'}
>>> import json
>>> print json.dumps(dicts, encoding="UTF-8", ensure_ascii=False)
{"asdf": "学习Python，我们是认真的"}
```

## print的工作原理
类UNIX系统中有标准输出和标准错误的说法（缩写为stdout、stderr），它们是建立在Unix系统内的管道（pipe）。对于Python来讲，当你输出的时候，结果就是输出到stdout管道中；当程序出错的时候，结果会输出到stderr管道中，而且**一般来讲这两个管道都是与你正在工作的终端窗口相联。**，当然如果你是在一个基于终端的Python IDE系统上工作，stedout和stderr是**缺省窗口**。

### print会干什么？
```python
>>> for i in range(3):
...     print 'Python2.%d'%i
...
Python2.0
Python2.1
Python2.2
>>>
>>> import sys
>>> for i in range(3):
...     sys.stdout.write('Python2.%d'%i)
...
Python2.0Python2.1Python2.2>>>
>>>
>>> for i in range(3):
...     sys.stderr.write('Python2.%d'%i)
...
Python2.0Python2.1Python2.2>>>
>>>
```
由上述测试可以得出如下结论：

 - stdout和stderr都是一个类文件对象，调用他的write，会打印出任何给出的字符串，**并且不加换行符的输出**。
 - stdout和sterr会把他们的输出发送到同一个地方：Python IDE或者终端（如果你正在终端运行Python）
 - **print所做的工作：**它在字符串末尾加上换行符，然后调用sys.stdout.write()


### 类文件对象是什么？有什么特点？
stdout、stderr都是类文件对象，**类文件对象是任意一个带有read方法的对象，**这个方法带一个可选的size参数，并返回一个字符串。当不用size参数调用时，它从输入源中读出所有东西，并将所有数据作为单个字符串返回。当使用size参数调用时，它从输入源中读出size大小的数据，并返回那些数据；当再次调用时，它从余下的地方开始并返回下一块数据。
**虽然stdout、stderr是类文件对象，但是他比较特殊，它们只有write方法而没有read方法。**但是我们可以通过将他绑定到其他的类文件对象来得到不通的输出位置，默认是输出到屏幕。
如：
```python
import sys
saveout = sys.stdout
fsock = open('out.log', 'w')
sys.stdout = fsock
print 'This message will be logged instead of displayed'
sys.stdout = saveout #要把stdout恢复回去，stderr一般不用恢复
fsock.close()
```

# super()内建函数

**首先声明一点，super是针对新式类的，不适用于经典类。**，这点一定要明确了。

## 经典类怎么初始化继承类？

先看个代码：
```python
class Base(object):
    def __init__(self):
        print "Base created"

class ChildA(Base):
    def __init__(self):
        Base.__init__(self)

class ChildB(Base):
    def __init__(self):
        super(ChildB, self).__init__()

print ChildA()
print ChildB()

#输出结果
Base created
<__main__.ChildA object at 0x7fcca2eafbd0>
Base created
<__main__.ChildB object at 0x7fcca2eafbd0>
```
由此看出，其实super和直接使用继承类去初始化没什么区别，但是你想想，当有一天，你突然想改名父类Base(object)为Root呢，那么对于子类来说，是不是全要改一次呢，显然，如果大量子类继承这个父类的话，你的代码怎么维护。所以使用super显而易见的作用就是维护成本小，当然，这仅仅是它显而易见的作用，还有其他的作用，下面来讲。

## 新式类的super
首先，要更深入的了解super，必须要先要了解**MRO（方法解析顺序）**。
在支持多继承的编程语言中，其方法、属性有可能是定义在当前类中，也有可能是在继承类中，那么问题来了，当有多个继承的时候，比如class Child（root_A,root_B）,那么假如父类中有重复的方法和类，到底使用那个呢？所以必须在调用该方法前先要对当前类和继承类进行搜素，以确定方法所在的位置，而这种搜索顺序就是所谓的MRO。

[MRO知识普及文章推荐](http://hanjianwei.com/2013/07/25/python-mro/),感谢作者。

## super到底干了什么工作？

super本质上干的工作：

```python
def super(cls, inst):
    mro = inst.__class__.mro()
    return mro[mro.index(cls) + 1]
```

上述函数解读，也是super函数的本质：

 - **inst 负责生成 MRO 的 list**
 - **通过 cls 定位当前 MRO 中的 index, 并返回 mro[index + 1]**

是不是还有点丈二和尚摸不着头脑，不要紧，接下来，将举例说明上述函数：


```python
class Root(object):
    def __init__(self):
        print("this is Root")
class B(Root):
    def __init__(self):
        print("enter B")
        super(B, self).__init__()
        print("leave B")
class C(Root):
    def __init__(self):
        print("enter C")
        super(C, self).__init__()
        print("leave C")
class D(B, C):
    pass
d = D()
print(d.__class__.__mro__)

#输出：
enter B
enter C
this is Root
leave C
leave B
(<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.Root'>, <type 'object'>)
```


有上述结果，我们能得到如下结论：

 - super和父类并没有直接的关联，而是指MRO中的下一个类。因为如果直接关联的话，打印出来的结果应该是B之后就是Root类了，但是实质上是C类，因此super是mro[index + 1]。
 - super会根据MRO逐个按顺序的去找__init__(前后双下划线，下同)构造函数，所以会先找自身，发现D没有__init__，接着去找B的，再去找C，再去找Root，再去找元类type。**在MRO中，基类永远出现在派生类后面，如果有多个基类，基类的相对顺序保持不变。**

备注：
上述举例代码中，我们如果给class B()中加二个打印的print，如下：
```python
class B(Root):
    def __init__(self):
        print self.__class__
        print self.__class__.__mro__
        print("enter B")
        super(B, self).__init__()
        print("leave B")
```
猜猜输出的是什么？
结果输出：
```python
<class '__main__.D'>
(<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.Root'>, <type 'object'>)
```
注意：self输出的是D的instance，而不是B的instance。

# sorted排序

内建函数sorted()可对**可迭代序列排序**，返回一个排好序的可迭代序列。

## sorted函数：

### sorted原型和参数
源函数：
```python
sorted(...)
    sorted(iterable, cmp=None, key=None, reverse=False) --> new sorted list
```
参数解析：

 - iterable：是可迭代类型;
 - cmp：用于比较的函数，比较什么由key决定,有默认值，迭代集合中的一项;
 - key：用列表元素的某个属性和函数进行作为关键字，有默认值，迭代集合中的一项;
 - reverse：排序规则. reverse = True 或者 reverse = False，默认升序；
 - 返回值：是一个经过排序的可迭代类型，与iterable一样。

### sorted和sort的区别

 - sort仅仅是list的一个方法，其他可迭代序列无此方法；sorted对所有的可迭代序列有效。
 - sort是就地排序，排完序后list的本身也改变了；sorted是生成一个新的排好序的序列。

举例说明：
```python
>>> lists = [5,3,2,1,4,6]
>>> sorted(lists)
[1, 2, 3, 4, 5, 6]
>>> lists
[5, 3, 2, 1, 4, 6]
>>> lists.sort()
>>> lists
[1, 2, 3, 4, 5, 6]
```

## sorted函数使用方法详解

list.sort()和sorted()都支持key参数，key参数指定一个函数或者表达式等，此函数在**每个元素比较前**被调用。此函数的参数只有一个，并且返回一个值来与其他元素做比较。
key指定的函数可以对每个元素快速的作用，所以这个速度其实是很快的。

在python2.4前，sorted()和list.sort()函数没有提供key参数，但是提供了cmp参数来让用户指定比较函数，cmp参数指定的函数用来进行元素间的比较。python3.0以后这个参数被废除。

比如：

 1. 不区分大小写的进行排序：
```python
>>> sorted("This is a test string from Andrew".split(), key=str.lower)
['a', 'Andrew', 'from', 'is', 'string', 'test', 'This']
```
 2. 按照年龄排队：
```python
>>> student_tuples = [
...         ('john', 'A', 15),
...         ('jane', 'B', 12),
...         ('dave', 'B', 10),
... ]
>>> sorted(student_tuples, key=lambda student: student[2])
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
>>> sorted(student_tuples, key=lambda student: student[2],reverse=True)
[('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
>>>
```
 3. 调用cmp函数来比较大小：
```python
>>> def numeric_compare(x, y):
...     return x - y
...
>>> sorted([5, 2, 4, 1, 3], cmp=numeric_compare)
[1, 2, 3, 4, 5]
>>> def numeric_compare(x, y):
...     return y - x
...
>>> sorted([5, 2, 4, 1, 3], cmp=numeric_compare)
[5, 4, 3, 2, 1]
>>>
```

### 字典排序

 - 按照字典的value中排序：
```python
x = {1: 2, 3: 4, 4: 3, 2: 1, 0: 0}
sorted_value = sorted(x.iteritems(), key=lambda e: e[1])
print sorted_value
sorte_values = sorted(x.iteritems(), key=lambda e: e[1], reverse=True)
print sorte_values

#输出
[(0, 0), (2, 1), (1, 2), (4, 3), (3, 4)]
[(3, 4), (4, 3), (1, 2), (2, 1), (0, 0)]
```

 - list的元素是dict，排序：
```python
x = [{'name': 'Homer', 'age': 9}, {'name': 'Bart', 'age': 10}]
values = sorted(x, key=lambda x: x['name'])
value = sorted(x, key=lambda x: x['age'])
print values
print value

#输出
[{'age': 10, 'name': 'Bart'}, {'age': 9, 'name': 'Homer'}]
[{'age': 9, 'name': 'Homer'}, {'age': 10, 'name': 'Bart'}]
```

 - 按照字典value的某个元素排序：
```python
myDict = {'item1': [7, 1, 9], 'item2': [8, 2, 3], 'item3': [9, 3, 11]}

def sortDic(Dict, valuePostion):
    return sorted(Dict.items(), key=lambda e: e[1][valuePostion])

# 按value的第3个值排序
print sortDic(myDict, 2)
[('item2', [8, 2, 3]), ('item1', [7, 1, 9]), ('item3', [9, 3, 11])]

# 按value的第1个值排序
print sortDic(myDict, 0)
[('item1', [7, 1, 9]), ('item2', [8, 2, 3]), ('item3', [9, 3, 11])]
```

# with上下文管理器


# 获取脚本的执行路径问题
Python中有两种方法能获取到执行脚本的文件路径，一种是**"__file__"**，一种是**"sys.argv[0]"**。
## 主执行文件，获取执行脚本路径
在主执行文件中，**"__file__"**和sys.argv[0]区别不大，都是根据的到执行该脚本的方式不同，而得到绝对路径或者相对路径。
如下所示：
```python
#! /usr/bin/env python
#-*-coding:utf-8-*-

import sys

print "__file__    -------->%s"%__file__
print "sys.argv[0] -------->%s"%sys.argv[0]

#输出结果：
"""
[root@localhost workspace]# ./test.py
__file__    -------->./test.py
sys.argv[0] -------->./test.py
[root@localhost workspace]# /root/workspace/test.py
__file__    -------->/root/workspace/test.py
sys.argv[0] -------->/root/workspace/test.py
[root@localhost workspace]# ~/workspace/test.py
__file__    -------->/root/workspace/test.py
sys.argv[0] -------->/root/workspace/test.py
"""
```
由上面的测试可以看到，此两种获取路径的方式都是由执行脚本的方式不同，而结果是相对路径或者绝对路径，可以通过**os.path.abspath()**函数来取得执行脚本的绝对路径。
以上说明**在主文件中执行的时候，这两种方式没什么区别。**

## 非主执行文件，import其他文件，获取执行脚本路径
```python
# test_1.py脚本，主执行脚本
import sys
import os
print "__file__    -------->%s"%__file__
print "sys.argv[0] -------->%s"%sys.argv[0]

import test_2
test_2.show_path()

# test_2.py脚本，供其他文件引入的脚本，非主执行文件
import sys
import os

def show_path():
    print "show_path:__file__    -------->%s"%__file__
    print "show_path:sys.argv[0] -------->%s"%sys.argv[0]

#输出结果：
"""
__file__    -------->./test_1.py
sys.argv[0] -------->./test_1.py
show_path:__file__    -------->/root/workspace/test_2.py
show_path:sys.argv[0] -------->./test_1.py
"""
```
以上结果说明，**"__file__"**得到的不一定是主文件的执行路径，而是那个脚本中的**__file__**，则得到是那个脚本的路径，而**sys.argv[0]**得到的是永远是主执行文件的路径，这个很好理解，因为sys.argv[0]本质上就是脚本命令行的第一个参数，那么这个脚本是什么，肯定得到的第一个参数就是什么。

**以上结果说明：当需要获取执行脚本的路径的时候，sys.argv[0]比__file__更靠谱一些。**

[super解释非常好的博客](https://laike9m.com/blog/li-jie-python-super,70/)，感谢作者！
