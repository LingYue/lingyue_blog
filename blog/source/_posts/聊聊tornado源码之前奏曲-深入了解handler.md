---
title: Tornado源码之handler
date: 2016-05-13 11:49:52
tags: Tornado源码分析
categories: Tornado系列
toc: true
comments: true
---


在深度聊tornado之前，我们先要聊聊torndao中非常重要的handler。
<!-- more -->


# 重提hello world

我们在[tornado简历](http://lingyue.github.io/2016/05/04/tornado%E7%9A%84%E7%AE%80%E5%8E%86/)一文中曾重点介绍过程序员世界经典的的hello world，本文将旧事重提，再次介绍torndao的hello world，以便管中窥豹，一叶知秋去了解torndao和一般的tornado应用程序的代码结构，以及本文的重点heandler。

下面的hello world是我在tornado中的demos中获取的，torndao的demos自带有很多经典的demo：

```python
#!/usr/bin/env python
#
# Copyright 2009 Facebook
#

import tornado.httpserver
import tornado.ioloop
import tornado.options
import tornado.web

from tornado.options import define, options

define("port", default=8888, help="run on the given port", type=int)


class MainHandler(tornado.web.RequestHandler):
    def get(self):
        self.write("Hello, world")


def main():
    tornado.options.parse_command_line()
    application = tornado.web.Application([
        (r"/", MainHandler),
    ])
    http_server = tornado.httpserver.HTTPServer(application)
    http_server.listen(options.port)
    tornado.ioloop.IOLoop.current().start()


if __name__ == "__main__":
    main()
```

 - **xxHandler类型的函数：** xxHandler主要是针对映射的url的一些具体的实现。
 - **main函数说明：** 应用程序执行的时候，会先创建一个Application实例并传递给HTTPServer实例，之后启动这个实例，此时，http server就启动了。tornado.httpserver模块用来支持非阻塞的HTTP Server。启动服务器后，接下来就是启动IOLoop实例了，IOLoop主要是一个事件循环机制，配合非阻塞的HTTP Server工作，后面会详细解决。
 - **Application：** 这个是tornado中一个男二号，很重要的一个模块，定义在web.py中，后面我们会深入的分析这个模块的。torndao中解释，Application类实际上是一个Handler的集合，这些Handler组成了一个web应用程序的。

# torndao中的handler

torndao中的handler其实就是Django中的View层的函数，即MVC模式中的V层，是真正处理一些用户的请求，并根据用户的请求，返回用户所需要的信息的函数，可以理解为tornado一个真正干活的地方。

我们知道，当你在浏览器中打来一个网站的时候，他会提交一个URL，表示“把这个地址的数据给我”，当然这中间涉及到DNS解析等各种中间环节才能到你服务器，此处略去。你这个url的数据可能是一个网页，如https://tornado/index.htm；可能是一张图片，如https://tornaado/tornado.png。这些数据传递给浏览器的时候，经过浏览器的渲染，就是你所看到的网站的东西。
对于每个url，应用服务可以设置一个handler来处理，如https://tornado/index.htm请求就交给handler_1_htm来处理；https://tornaado/tornado.png就交给handler_2_png来处理，这些handler函数的作用就是负责根据客户端的请求的内容通过http协议返回给客户端。**这就是tornado中的handler。**

这些handler相对于静态网页而言，就很有优势了，可以动态展示，比如根据每个人的工号，从数据库中取出这个人的基本信息展示在浏览器，没个人看到的网页都是不一样的，就是说就成动态的了。其实torndao开发网站，绝大多数时候，其实都是在开发handler。

至于URL如何和handler对应起来，这就是所谓的映射需要干的活，如hello中就是通过：
```python
application = tornado.web.Application([
        (r"/", MainHandler),
    ])
```
这样每个请求url和handler组成一个tuple，如**(r"/", MainHandler)**，就把handler与用户请求之间的映射关系建立起来了，显然Application的handlers是一个list，每个list中的元素是一个tuple，这样组成了handler和url的映射map。
上述中的tuple表示，当用户请求根目录的时候，就请MainHandler这个handler来处理了，你所在浏览器中看到的结果，就是MainHandler处理的结果了。

当然，不是说每个浏览器地址栏中的url并不一定表示这个服务器上一定有这个文件，它可以是web服务器虚拟映射的一个路径。

以上就是Handler的主要介绍，看看，其实handler还是蛮简单的嘛。雄关漫步真如铁，而今漫步从头越，加油，少年！
学Python，我们是认真的。


