---
title: 基于Scrapy爬虫开发
date: 2018-08-11 14:39:35
tags: 学习笔记
description: Scrapy框架学习

---

## Scrapy简介

Scrapy是由Python开发的一个快速,高层次的web抓取框架，一般用于抓取web站点，并从页面中提取结构化的数据。
用户只需要定制开发几个模块就可以轻松的实现一个爬虫，用来抓取网页内容以及各种图片，非常之方便。
Scrapy 使用了 Twisted'twɪstɪd异步网络框架来处理网络通讯，可以加快我们的下载速度，不用自己去实现异步框架，并且包含了各种中间件接口，可以灵活的完成各种需求。

## Scrapy安装（for Mac）

```
pip install scrapy
```

安装过程中可能会比较慢，或是在Collecting Twisted的时候卡住，可以考虑先安装Twisted或是配个国内的镜像源加速。

## 如何开始Scrapy爬虫

### 创建一个项目

我们用命令行的方式创建一个scrapy项目

```
scrapy startproject tutorial
```

然后进入项目目录，根据内置模版创建一个爬取中国商务部网站的爬虫，这个爬虫名字叫mofocm ，爬虫允许在mofcom.gov.cn 域名下爬取

```
cd tutorial
scrapy genspider mofcom mofcom.gov.cn
```

此时在spiders目录下会生成mofcom.py文件，我们主要是在这个模块下对得到的网页内容进行解析，这个模块下定义了parse方法，默认对得到的Response对象提取出我们需要的结构化的数据。

在mofcom.py中我们可以看到，其包含了一个用于下载的初始URL，如何跟进网页中的链接以及如何分析页面中的内容， 提取生成 item 的parse方法。我们创建的Spider中，继承 scrapy.Spider 类，其包含了三个属性：name,start_urls,parse()方法

- name： 用来标识spider 的名字
- start_urls: 包含了Spider在启动时进行爬取的url列表
- parse()方法：是spider的一个方法。 被调用时，每个初始URL完成下载后生成的 Response 对象将会作为唯一的参数传递给该函数。 该方法负责解析返回的数据(response data)，提取数据以及生成需要进一步处理的URL的 Request 对象

下面给出一个示例代码，保存在在mofcom.py中
 
```python
import scrapy
class MofcomSpider(scrapy.Spider):
    name = "mofcom"
    allowed_domains = ["mofcom.gov.cn"]
    start_urls = [    "http://www.mofcom.gov.cn/article/ae/ai"
    def parse(self, response):
          for sel in response.xpath('//li/a'):
            item = TutorialItem()
            item['title'] = sel.xpath('./@title').extract()
            site = sel.xpath('./@href').extract()[0]
            if not site.startswith('http'):
                url = "http://www.mofcom.gov.cn" + site
            else:
                url = site
            item['link'] = url
            yield item    
```
这会对从‘http://www.mofcom.gov.cn/article/ae/ai‘ 得到的response进行解析。scrapy在运行爬虫的时候，首先会根据start_urls 中的URL生成Request，Request对象经过调度，执行生成 scrapy.http.Response 对象并送回给spider parse() 方法。

### 定义item

```python
class TutorialItem(Object):
     title = scrapy.Field()
     link = scrapy.Field()
```
为了从得到的页面中提取出结构化的数据，我们必须定义相应的item，item是Python字典Dict类型。(字段即是我们之前用Field赋值的属性)

一般来说，Spider将会将爬取到的数据以item对象的形式返回。

### 自定义的pipeline的实现

在提取出相应的数据之后，我们可能还要对得到的数据进行存储，去重或是过滤等操作，此时就需要借助pipeline组件。

例如我们需要将我们得到的数据输出为json文件：
```python
import json
import codecs
from collections import OrderedDict

class JsonWithEncodingPipeline(object):
    def __init__(self):
        self.file = codecs.open('data_utf8.json', 'w', encoding='utf-8')

    def process_item(self, item, spider):
        line = json.dumps(OrderedDict(item), ensure_ascii=False, sort_keys=False) + "\n"
        self.file.write(line)
        #print line
        return item

    def close_spider(self, spider):
        self.file.close()
```
这会将你提取的数据输出成data_utf8.json文件。

每个item pipiline组件必须实现process_item(item, spider)，这个方法定义对提取出来的item进行的操作，该方法必须返回一个 Item (或任何继承类)对象， 或是抛出 DropItem 异常，而之后被丢弃的item将不会被之后的pipeline组件所处理。

另外还可以实现其他方法
open_spider(spider)，当spider被开启时，这个方法被调用；
close_spider(spider)，当spider被关闭时，这个方法被调用。

为了启用Pipeline组件，你还要在setting文件中将它的类添加到 ITEM_PIPELINES 配置
```python
ITEM_PIPELINES = {
    'tutorial.pipelines.JsonWithEncodingPipeline': 300,
}
```
其中300 并没有什么具体的含义，只是为了在多个pipeline是用来进行排序,判断那个pipelines先运行

### 运行爬虫

setting文件中将ROBOTSTXT_OBEY 默认为True，就是要遵守robots.txt 的规则， 现在要改为为false，不然无法爬取。
如果一切都准确无误的话，就可以开始运行爬虫了，在项目跟目录执行
```shell
scrapy crawl mofcom
```
此时爬虫变开始爬取网站数据

### Selector

当我们抓取网页时，做的最常见的任务是从HTML源码中提取数据。Scrapy使用了一种基于 XPath 和 CSS 表达式机制: Scrapy Selectors，从网页提取数据，它们通过特定的 XPath 或者 CSS 表达式来“选择” HTML文件中的某个部分。
例如
```python
response.xpath('//li/a')
```
这会提取出HTML文档li节点内所有的a元素
Selector有几个个基本的方法
- XPath() 返回XPath表达式所对应的节点的selector list列表
- css() 返回css表达式所对应的节点的selector list列表
- extract()  序列化该节点为unicode字符串并返回list
- extract_first() 序列化该节点为unicode字符串并返回list的第一个字符串
- re() 根据传入的正则表达式对数据进行提取，返回unicode字符串list列表

### Scrapy shell

Scrapy终端是一个交互终端，供您在未启动spider的情况下尝试及调试您的爬取代码，测试提取数据的代码，在shell中你可以用它来测试你的selector是否能够正确地提取出相关的数据。我们可以在scrapy shell 中测试我们的selector是否能够提取出我们要的数据，而不用之前启动爬虫调试，这样的话大大减少了我们的工作量

举个例子，还是那个商务网站：
现在先在命令行里面敲
```
scrapy shell http://www.mofcom.gov.cn/article/ae/ai
```
这样进入shell,当shell载入后，您将得到一个包含response数据的本地 response 变量。通过查看源码，我们可以发现当前页面的新闻信息都在 //li/a 中。我们可以通过以下代码提取出在 li节点内的所有a元素
```python
sel = response.xpath('//li/a')
```

对应的每一条新闻的标题则是
```python
sel.xpath('./@title').extract_first()
```

对应的每一条新闻的链接则是
```python
sel.xpath('./@href').extract_first()
```

这样我们便可以Scrapy shell中用Selector得到的结果用来调试我们的代码。

有时如果想在spider的某个位置中查看被处理的response， 以确认自己期望的response到达特定位置。这个可以通过 scrapy.shell.inspect_response 函数来实现。只需要在parse某个位置加上
```python
from scrapy.shell import inspect_response
inspect_response(response, self)
```
spider运行到该处的时候，就会进入shell进行调试，如果你想查看response或是selector是否正确，可以在shell中查看使用selector提取出的数据。点击Ctrl-D进行退出，爬虫会继续执行。

### scrapy.Spider

scrapy.Spider是最简单的spider。每个其他的spider必须继承自该类(包括Scrapy自带的其他spider以及您自己编写的spider),其主要作用就是有个默认的实现方法 start_requests()，该方法会读取start_urls中的URL生成Request，然后爬虫会根据返回的结构response调用回调函数parse。
我们的spiders并没有实现start_requests()，这是调用了默认的start_requests(),这个会默认使用回调函数parse。如果你不想使用parse，或是自己还实现了其他方法例如parse_xxx,你也可以在start_request中去更改，
```python
yield scrapy.Request(URL, callback=self.parse_XXX)
```

其实在parse解析函数中出了可以提取item，也可以产生request，这样产生的Request会经过scrapy引擎的调度继续去请求获取网页数据，得到的数据又会被回调函数解析，像上面的代码产生得到的数据会被parse_XXX 解析。

我们可以以这样的方式进入到下一层网页爬取更详细的数据，例如如果我想爬取当前的URL得到的所有链接所指向的新闻内容，我们可以在parse中加入
```python
yield scrapy.Request(item['link'], callback=self.parse_more,meta={'item': item})
```
这个会产生一个Request请求每一条新闻的内容，在TutorialItem添加desc属性，另外我们还需要实现一下parse_more

```python
def parse_more(self,response):
            item = response.meta['item']
            # populate more `item` fields
            sel = response.xpath('//div[@id="zoom"]').xpath('string(.)').extract_first().strip()
            item['desc'] = sel
            yield item
```
那么爬虫会爬取进一步爬取新闻的内容信息。

### CrawlSpider

CrawlSpider爬取一般网站常用的spider。其定义了一些规则(rule)来提供跟进link的方便的机制，这个爬虫能够根据规则对一些符合规则对链接进行跟进。它也是从scrapy.Spider 继承过来的，所以除了scrapy.Spider 的属性外，其提供了一个新的属性:rules。这是一个包含一个(或多个) Rule 对象的集合(list）。
Rule对象
```python
class scrapy.spiders.Rule(link_extractor, callback=None, cb_kwargs=None, follow=None, process_links=None, process_request=None)
```
Rule对象中包含一个LinkExtractor对象,其定义了如何从爬取到的页面提取链接。它定义了allow和deny属性，它会对符合要求的链接跟进，并在提取出符合rules中Rule的链接，进行相应的处理，而follow则是决定是否跟进链接。rules中的规则优先级是总是前面的Rule优先。

rules
```python
 rules = (
        Rule(LinkExtractor(
            allow=(r'article/ae/[a-z]*?/2018[0-9]*?/[0-9]*?\.shtml', ),
            deny=(r'http://www.mofcom.gov.cn/article/ae/slfw/2018[0-9]*?/[0-9]*?\.shtml', 		  r'http://www.mofcom.gov.cn/article/ae/ztfbh/2018[0-9]*?/[0-9]*?\.shtml')),
            callback='parse_content'),

        Rule(LinkExtractor(allow=('/article/ae/ai', '/article/ae/ag')),
             follow=True),
    )

```
以上定义的两个Rule对象，会先考虑在原网页中提取出符合正则表达式的链接，像第一个Rule，提取出相关链接后，会调用parse_content回调函数，而第二个Rule会继续跟进提取出来的链接。

需要注意的是：当编写爬虫规则时，请避免使用 parse 作为回调函数。 由于 CrawlSpider 使用 parse 方法来实现其逻辑，如果覆盖了 parse 方法，CrawlSpider 将会运行失败

如果要了解多一些东西的话还是需要看scrapy文档 https://doc.scrapy.org/en/latest/
