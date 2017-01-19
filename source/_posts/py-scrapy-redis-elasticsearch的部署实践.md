---
title: py-scrapy-redis-elasticsearch的部署实践
author: Cry Rain
tags:
  - python
  - scrapy
  - redis
  - elasticsearch
categories:
  - python
date: 2017-01-18 17:06:00
---

# py-scrapy-redis-elasticsearch的部署实践

> 由于公司项目需要，需要用到python-scrapy-redis-elasticsearch这种**分布式爬虫**的技术栈，所以现在将整个安装，部署步骤一一记录下来。方便自己或者有相同需要的人。

## 1. 前言：

### 1.1 作用
其实，对于了解过爬虫项目的人来说，python-scrapy已经比较成熟的爬虫框架。它能简单快捷地部署爬虫框架，并通过配置简单的参数，使用Xpath、CSS selector来捕获网页的元素，从而达到抓取，爬取的目的。  
而加入了redis这种noSQL数据库后，可以使scrapy实现分布式的爬虫方案。
最后加入Elasticsearch(es)则是使爬取的数据实时记录在ES中，继而前端页面调用、分析等后续任务，是属于实时查询的过程。

```
graph LR
Python-->Scrapy
Redis-->Scrapy
Scrapy-->Elasticsearch
Elasticsearch-->下游的操作
```
### 1.2 系统环境

> OS:window     
> Python:2.7  
> scrapy:  
> IDE: Pycharm  
> Py-Redis:  
> Elasticsearch:5.1.x

## 2. 下载安装步骤
### 2.1 Python
python现在有两个版本。2.x以及3.x，总体来说，3.x是以后发展的趋势，但是2.x却是更成熟的版本。对于我自己来说。还是采用2.x。
#### 2.1.1 Python下载
python下载的网址是[https://www.python.org/downloads/](https://www.python.org/downloads/)，那里有最新的版本。而我在这里演示的是：[2.7.13](https://www.python.org/ftp/python/2.7.13/python-2.7.13.msi)
#### 2.1.2 Python安装
安装过程就跟普通的程序安装差不多。只需要采用默认的配置，一直下一步就行了。安装完毕后，把python程序以及python的一些脚本添加到系统变量里。
![image](http://od5y2z5kt.bkt.clouddn.com/%E6%95%99%E7%A8%8BQQ%E6%88%AA%E5%9B%BE20170117235931.png)
然后打开命令行，输入python,看到以下内容便说明python安装成功。
```
C:\Users\J>python
Python 2.7.9 (default, Dec 10 2014, 12:28:03) [MSC v.1500 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>>
```
### 2.2 pycharm
pycharm是Jetbrain公司开发的一款用于python开发的软件。这个公司旗下还有很多语言的IDE，可以说在开发领域上十分碉堡的公司。
#### 2.2.1 pycharm下载
pycharm主页：[http://www.jetbrains.com/pycharm/](http://www.jetbrains.com/pycharm/)  
pycharm下载页面在这[http://www.jetbrains.com/pycharm/download/#section=windows](http://www.jetbrains.com/pycharm/download/#section=windows)，有两个版本，一个是Community(社区版)是免费的，是一个轻量的python IDE。还有一个专业版，则是需要收费。所以建议使用Community版本。
[https://download.jetbrains.com/python/pycharm-community-2016.3.2.exe](https://download.jetbrains.com/python/pycharm-community-2016.3.2.exe)
#### 2.2.2 pycharm安装
安装步骤其实还是跟普通的软件安装是一样的。只需要一直默认下一步就可以了。
#### 2.2.3 新建一个python项目
打开pycharm后可以看到类似下图
![image](http://od5y2z5kt.bkt.clouddn.com/%E6%95%99%E7%A8%8BQQ%E6%88%AA%E5%9B%BE20170118004610.png)
此时你就可以新建、打开或者使用版本控制(CVS)程序来实现你的第一个python程序了。
不妨创建个helloworld吧。

### 2.3 scrapy
scrapy的官网是：[https://scrapy.org/](https://scrapy.org/)打开后有提示教你怎么安装。
#### 2.3.0 pip的配置
由于国内连接pypi的源不稳定,所以一般采用国内的镜像.
>如果想配置成默认的源，方法如下：  
>需要创建或修改配置文件（一般都是创建）  
>linux的文件在~/.pip/pip.conf    
>windows在%HOMEPATH%\pip\pip.ini  

修改内容为：
```
[global]
index-url = http://pypi.douban.com/simple
trusted-host = pypi.douban.com
```

#### 2.3.1 scrapy下载安装
由于python有pip的管理安装包，所以我们可以根据[官网](https://doc.scrapy.org/en/1.3/intro/install.html)的提示安装，由于我们在2.1.2里添加了系统变量，所以打开CMD，输入以下命令进行下载安装

```
#安装incremental
pip install incremental
```
下载并安装 [lxml-3.1.2.win32-py2.7.exe](https://pypi.python.org/packages/01/f3/f325c5d8908af822e9027861f107c04bfedd9cc02fc9aa3a4eb352b32917/lxml-3.1.2.win32-py2.7.exe#md5=c089bbd8452480912001c3700ff47f8f)

下载安装 VCForPython27.msi
```
#安装scrapy
pip install scrapy
```
这样就可以顺利安装上scrapy
#### 2.3.2 Scrapy tutorial
在安装完scrapy的时候,我们可以新建一个scrapy项目.而scrapy自带就有一个创建模板.
在你创建的工作目录下输入
```
scrapy startproject <项目名> :
scrapy startproject tutorial
#然后可以看到以下的内容:
C:\Users\lzx\Desktop\python>scrapy startproject tutorial
New Scrapy project 'tutorial', using template directory 'c:\\python27\\lib\\site
-packages\\scrapy\\templates\\project', created in:
    C:\Users\lzx\Desktop\python\tutorial

You can start your first spider with:
    cd tutorial
    scrapy genspider example example.com
#然后输入
cd tutorial
scrapy genspider example example.com
```
便可生成简单的scrapy框架的爬虫.

---

**注意,以下内容摘录自 [http://www.cnblogs.com/txw1958/archive/2012/07/16/scrapy-tutorial.html](http://www.cnblogs.com/txw1958/archive/2012/07/16/scrapy-tutorial.html)**
##### 2.3.2.1 目录结构
这个命令会在当前目录下创建一个新目录tutorial，它的结构如下：
```
T:\tutorial>tree /f
Folder PATH listing
Volume serial number is 0006EFCF C86A:7C52
T:.
│  scrapy.cfg
│
└─tutorial
    │  items.py
    │  pipelines.py
    │  settings.py
    │  __init__.py
    │
    └─spiders
            __init__.py
```
>
>这些文件主要是：   

>scrapy.cfg: 项目配置文件  
>tutorial/: 项目python模块, 代码将从这里导入    
>tutorial/items.py: 项目items文件   
>tutorial/pipelines.py: 项目管道文件    
>tutorial/settings.py: 项目配置文件     
>tutorial/spiders: 放置spider的目录 
 

##### 2.3.2.2 定义Item

>Items是将要装载抓取的数据的容器，它工作方式像python里面的字典，但它提供更多的保护，比如对未定义的字段填充以防止拼写错误。

>它通过创建一个scrapy.item.Item类来声明，定义它的属性为scrpy.item.Field对象，就像是一个对象关系映射(ORM). 
>我们通过将需要的item模型化，来控制从dmoz.org获得的站点数据，比如我们要获得站点的名字，url和网站描述，我们定义这三种属性的域。要做到这点，我们编辑在tutorial目录下的items.py文件，我们的Item类将会是这样
```
from scrapy.item import Item, Field 
class DmozItem(Item):
    title = Field()
    link = Field()
    desc = Field()
```
>刚开始看起来可能会有些困惑，但是定义这些item能让你用其他Scrapy组件的时候知道你的 items到底是什么。

##### 2.3.2.3 我们的第一个爬虫(Spider)

>Spider是用户编写的类，用于从一个域（或域组）中抓取信息。   

>他们定义了用于下载的URL的初步列表，如何跟踪链接，以及如何来解析这些网页的内容用于提取items。 

>要建立一个Spider，你必须为scrapy.spider.BaseSpider创建一个子类，并确定三个主要的、强制的属性：    

>name：爬虫的识别名，它必须是唯一的，在不同的爬虫中你必须定义不同的名字.    
>start_urls：爬虫开始爬的一个URL列表。爬虫从这里开始抓取数据，所以，第一次下载的数据将会从这些URLS开始。其他子URL将会从这些起始URL中继承性生成。  
>parse()：爬虫的方法，调用时候传入从每一个URL传回的Response对象作为参数，response将会是parse方法的唯一的一个参数,  
>这个方法负责解析返回的数据、匹配抓取的数据(解析为item)并跟踪更多的URL。    

>这是我们的第一只爬虫的代码，将其命名为dmoz_spider.py并保存在tutorial\spiders目录下。


```
from scrapy.spider import BaseSpider

class DmozSpider(BaseSpider):
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = [
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
    ]

    def parse(self, response):
        filename = response.url.split("/")[-2]
        open(filename, 'wb').write(response.body)
```

##### 2.3.2.3 运行
为了让我们的爬虫工作，我们返回项目主目录执行以下命令
```
T:\tutorial>scrapy crawl dmoz
```
>scrapy crawl dmoz 命令从dmoz.org域启动爬虫。 你将会获得如下类似输出 
```
T:\tutorial>scrapy crawl dmoz
2012-07-13 19:14:45+0800 [scrapy] INFO: Scrapy 0.14.4 started (bot: tutorial)
2012-07-13 19:14:45+0800 [scrapy] DEBUG: Enabled extensions: LogStats, TelnetConsole, CloseSpider, WebService, CoreStats, SpiderState
2012-07-13 19:14:45+0800 [scrapy] DEBUG: Enabled downloader middlewares: HttpAuthMiddleware, DownloadTimeoutMiddleware, UserAgentMiddleware, RetryMiddleware, DefaultHeadersMiddleware, RedirectMiddleware, CookiesMiddleware, HttpCompressionMiddleware, ChunkedTransferMiddleware, DownloaderStats
2012-07-13 19:14:45+0800 [scrapy] DEBUG: Enabled spider middlewares: HttpErrorMiddleware, OffsiteMiddleware, RefererMiddleware, UrlLengthMiddleware, DepthMiddleware
2012-07-13 19:14:45+0800 [scrapy] DEBUG: Enabled item pipelines:
2012-07-13 19:14:45+0800 [dmoz] INFO: Spider opened
2012-07-13 19:14:45+0800 [dmoz] INFO: Crawled 0 pages (at 0 pages/min), scraped 0 items (at 0 items/min)
2012-07-13 19:14:45+0800 [scrapy] DEBUG: Telnet console listening on 0.0.0.0:6023
2012-07-13 19:14:45+0800 [scrapy] DEBUG: Web service listening on 0.0.0.0:6080
2012-07-13 19:14:46+0800 [dmoz] DEBUG: Crawled (200) <GET http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/> (referer: None)
2012-07-13 19:14:46+0800 [dmoz] DEBUG: Crawled (200) <GET http://www.dmoz.org/Computers/Programming/Languages/Python/Books/> (referer: None)
2012-07-13 19:14:46+0800 [dmoz] INFO: Closing spider (finished)
2012-07-13 19:14:46+0800 [dmoz] INFO: Dumping spider stats:
        {'downloader/request_bytes': 486,
         'downloader/request_count': 2,
         'downloader/request_method_count/GET': 2,
         'downloader/response_bytes': 13063,
         'downloader/response_count': 2,
         'downloader/response_status_count/200': 2,
         'finish_reason': 'finished',
         'finish_time': datetime.datetime(2012, 7, 13, 11, 14, 46, 703000),
         'scheduler/memory_enqueued': 2,
         'start_time': datetime.datetime(2012, 7, 13, 11, 14, 45, 500000)}
2012-07-13 19:14:46+0800 [dmoz] INFO: Spider closed (finished)
2012-07-13 19:14:46+0800 [scrapy] INFO: Dumping global stats:
        {}
```
>注意包含 [dmoz]的行 ，那对应着我们的爬虫。你可以看到start_urls中定义的每个URL都有日志行。因为这些URL是起始页面，所以他们没有引用(referrers)，所以在每行的末尾你会看到 (referer: <None>).      
>有趣的是，在我们的 parse  方法的作用下，两个文件被创建：分别是 Books 和 Resources，这两个文件中有URL的页面内容。 

---

##### 2.3.2.4 scrapy结束
至此,scrapy的爬虫算结束了.

### 2.4 redis
Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。
#### 2.4.1 redis的下载安装：
**此步骤安装在CentOS6.5上**
```
$ wget http://download.redis.io/releases/redis-3.2.6.tar.gz
$ tar xzf redis-3.2.6.tar.gz
$ cd redis-3.2.6
$ make
```
#### 2.4.2 redis的运行：
##### server:
```
src/redis-server ./redis.conf
```
##### client:
```
#连接上client后,可以通过get set 的方法测试
[l@bogon redis-3.2.6]$ src/redis-cli -h 192.168.230.131
192.168.230.131:6379> set 'hello' 'world'
OK
192.168.230.131:6379> get 'hello'
"world"
192.168.230.131:6379> 
```
### 2.5 Elasticsearch
#### 2.5.1 Elasticsearch下载安装
Elasticsearch的下载安装,建议查看过往的教程.[教程](http://).

### 2.6 教程结尾
python-scrapy-redis-elasticsearch的部署实践教程到这里已经结束.后面将会提供实际的例子来展示这技术栈如何爬取网站.