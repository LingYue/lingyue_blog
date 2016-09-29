---
title: Tornado源码剖析系列文章目录
date: 2016-05-04 19:26:05
tags: Tornado源码分析
categories: Tornado系列
toc: true
comments: true
---

tornado源码剖析目录。
<!-- more -->

在山人分析Tornado之前，可能有部分同学要问他，为干嘛要分析他的源码呢？我会用不久行了吗？
那么我们就来看看我剖析Tornado源码中我们能学习到什么呢？

- Tornado是Python业界比较有名的web框架，也是处理高并发的利器，那么学习了这种源码以后，对Tornado的开发将更加的得心应手。
- Tonado几乎囊括了所有重要的网络编程模型，比如阻塞、非阻塞、异步、epoll、HTTP服务等等，在学习了Tornado之后，基本对这块就会非常的了解。
- 我认为这是最重要的：**要提高自己，必须不断的和高手过招，逐渐混入高手的精英圈子，那么你也就是精英了，至少是半个精英。**和高手过招，是最快的提高自己的捷径，兵熊熊一个，将熊熊一窝，跟着什么人，你就会成为什么人。显然Tornado是高手的精华代码，当然是我等小辈趋之若鹜的。Tornado是Python编程的绝佳实习基地。

**tornado核心模块的源码剖析目录及相应源码剖析的链接**

----------


# 核心web framework模块

**[tornado.web](http://demo.pythoner.com/itt2zh/index.html):**包含绝大部分web框架的主要功能。
**[tornado.httpserver](http://demo.pythoner.com/itt2zh/index.html):**一个无阻塞的HTTP服务器的实现，以服务web模块。
**[tornado.template](http://demo.pythoner.com/itt2zh/index.html):**基于Python的web模块的一个模板系统。
**[tornado.escape](http://demo.pythoner.com/itt2zh/index.html):** HTML、JSON、URLs等的编码解码和一些字符串操作。
**[tornado.locale](http://demo.pythoner.com/itt2zh/index.html):**国际化支持，是基于本地化和翻译的支持。

----------


# 主要底层模块
**[tornado.ioloop](http://demo.pythoner.com/itt2zh/index.html):**核心的I/O循环
**[tornado.iostream](http://demo.pythoner.com/itt2zh/index.html):**对非阻塞式的socket的简单封装，以方便一些常用的读写操作。
**[tornado.httpclient](http://demo.pythoner.com/itt2zh/index.html):**非阻塞式HTTP客户端，它用来和web模块、httpserver模块协同工作。
**[tornado.netutil](http://demo.pythoner.com/itt2zh/index.html):**一些网络应用的实现，主要实现TCPServer类。

----------


# 与其他系统的交互的主要模块
**[tornado.auth](http://demo.pythoner.com/itt2zh/index.html):**第三方认证的实现。
**[tornado.websocket](http://demo.pythoner.com/itt2zh/index.html):**实现和浏览器的双向通信。
**[tornado.wsgi](http://demo.pythoner.com/itt2zh/index.html):**与其他Python网络框架、服务器的互相操作。


----------


# 应用的时的主要模块
**[tornado.autoreload](http://demo.pythoner.com/itt2zh/index.html):**生产环境中自动检查代码更新。
**[tornado.gen](http://demo.pythoner.com/itt2zh/index.html):**一个基于生成器的接口，使用该模块保证代码异步运行。
**[tornado.httputil](http://demo.pythoner.com/itt2zh/index.html):**分析HTTP请求内容。
**[tornado.options](http://demo.pythoner.com/itt2zh/index.html):**命令行和配置文件解析工具，针对服务器环境做了优化。
**[tornado.process](http://demo.pythoner.com/itt2zh/index.html):**多进程实现的封装。
**[tornado.stack_context](http://demo.pythoner.com/itt2zh/index.html):**用于异步环境中对回调函数的上下文保存、异常处理。
**[tornado.testing](http://demo.pythoner.com/itt2zh/index.html):**单元测试。


----------

# tornado源码结构

```python
├── tornado
│   ├── auth.py
│   ├── autoreload.py
│   ├── concurrent.py
│   ├── curl_httpclient.py
│   ├── escape.py
│   ├── gen.py
│   ├── http1connection.py
│   ├── httpclient.py
│   ├── httpserver.py
│   ├── httputil.py
│   ├── __init__.py
│   ├── ioloop.py
│   ├── iostream.py
│   ├── _locale_data.py
│   ├── locale.py
│   ├── locks.py
│   ├── log.py
│   ├── netutil.py
│   ├── options.py
│   ├── platform
│   │   ├── asyncio.py
│   │   ├── auto.py
│   │   ├── caresresolver.py
│   │   ├── common.py
│   │   ├── epoll.py
│   │   ├── __init__.py
│   │   ├── interface.py
│   │   ├── kqueue.py
│   │   ├── posix.py
│   │   ├── select.py
│   │   ├── twisted.py
│   │   └── windows.py
│   ├── process.py
│   ├── queues.py
│   ├── simple_httpclient.py
│   ├── speedups.c
│   ├── stack_context.py
│   ├── tcpclient.py
│   ├── tcpserver.py
│   ├── template.py
│   ├── test
│   │   ├── asyncio_test.py
│   │   ├── auth_test.py
│   │   ├── concurrent_test.py
│   │   ├── csv_translations
│   │   │   └── fr_FR.csv
│   │   ├── curl_httpclient_test.py
│   │   ├── escape_test.py
│   │   ├── gen_test.py
│   │   ├── gettext_translations
│   │   │   └── fr_FR
│   │   │       └── LC_MESSAGES
│   │   │           ├── tornado_test.mo
│   │   │           └── tornado_test.po
│   │   ├── httpclient_test.py
│   │   ├── httpserver_test.py
│   │   ├── httputil_test.py
│   │   ├── import_test.py
│   │   ├── __init__.py
│   │   ├── ioloop_test.py
│   │   ├── iostream_test.py
│   │   ├── locale_test.py
│   │   ├── locks_test.py
│   │   ├── log_test.py
│   │   ├── __main__.py
│   │   ├── netutil_test.py
│   │   ├── options_test.cfg
│   │   ├── options_test.py
│   │   ├── process_test.py
│   │   ├── queues_test.py
│   │   ├── resolve_test_helper.py
│   │   ├── runtests.py
│   │   ├── simple_httpclient_test.py
│   │   ├── stack_context_test.py
│   │   ├── static
│   │   │   ├── dir
│   │   │   │   └── index.html
│   │   │   ├── robots.txt
│   │   │   ├── sample.xml
│   │   │   ├── sample.xml.bz2
│   │   │   └── sample.xml.gz
│   │   ├── static_foo.txt
│   │   ├── tcpclient_test.py
│   │   ├── tcpserver_test.py
│   │   ├── templates
│   │   │   └── utf8.html
│   │   ├── template_test.py
│   │   ├── test.crt
│   │   ├── testing_test.py
│   │   ├── test.key
│   │   ├── twisted_test.py
│   │   ├── util.py
│   │   ├── util_test.py
│   │   ├── websocket_test.py
│   │   ├── web_test.py
│   │   └── wsgi_test.py
│   ├── testing.py
│   ├── util.py
│   ├── web.py
│   ├── websocket.py
│   └── wsgi.py
└── tornado_version
```
