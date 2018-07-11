# 1.tool commands
scrapy的一些运行命令
## 1.1 查看所有可用命令
scrapy -h

全局命令：
 ```   
    startproject
    
    settings
    
    runspider
    
    shell
    
    fetch
    
    view
    
    version
 ```

项目（project-only）命令
```
    crawl
    check
    list
    edit
    parse
    genspider
    bench
```
## 1.2创建项目
`scrapy startproject projectname`

## 1.3使用spider进行爬取
`scrapy crawl spidername`

## 1.4 运行contract检查
`scrapy check -l     查看有那些可以检测的
scrapy check`

## 1.5 列出当先项目中的所有可用的spider
`scrapy list`

## 1.6 使用EDITOR中设定的编辑器编辑给定的spider，该命令只提供一个快捷方式
`scrapy edit spidername`

## 1.7 使用scrapy下载器（Downloader）下载给定的url，并将获取到的内容送到标准输出
`scrapy fetch --nolog url
    例如：scrapy fetch --nolog http://www.baidu.com 帮助我们下载网页，将网页源代码返回`
    
## 1.8 view请求Url,把它的网页源代码保存成文件，并打开网页
`scrapy view url`

在浏览器中打开给定的url， 并以scrapy spider获取到的形式展现，以此命令可以检测spider所获取到的页面，并确认这是所期望的页面
    
## 1.9 shell 终端
`scrapy shell -s 'User-Agent'='...' url`

## 1.10 命令行解析页面
`scrapy parse url --callback parsename`

以给定的解析函数解析url，如果不写callback则使用默认的parse函数解析url

支持的选项：
* --spider=SPIDER: 跳过自动检测spider并强制使用特定的spider
* --a NAME=VALUE: 设置spider的参数(可能被重复)
* --callback or -c: spider中用于解析返回(response)的回调函数
* --pipelines: 在pipeline中处理item
* --rules or -r: 使用 CrawlSpider 规则来发现用来解析返回(response)的回调函数
* --noitems: 不显示爬取到的item
* --nolinks: 不显示提取到的链接
* --nocolour: 避免使用pygments对输出着色
* --depth or -d: 指定跟进链接请求的层次数(默认: 1)
* --verbose or -v: 显示每个请求的详细信息

## 1.11 获取scrapy设置
`scrapy setting -- get settings.py`

从settings.py中获取设置

## 1.12 在未创建项目的情况下运行一个编写下python文件中的spider
`scrapy runspider spidername.py`

## 1.13 获取scrapy版本
`scrapy version`

## 1.14 运行benchmark测试
`scrapy bench`

可以检测scrapy在电脑上最快的爬取速度

# 2.自定义项目命令

可以通过COMMANDS_MODULE来添加自己的项目命令，如爬取所有的spider
通过scrapy/commands中Scrapy commands为例了解如何实现命令。

以下是一个例子，一条命令开启所有的爬虫（spider）

1.在spider目录的同级目录下创建commands包（带有__init__.py文件的文件夹）

2.新建crawlall.py文件
```
from scrapy.commands import ScrapyCommand
from scrapy.crawler import CrawlerRunner
from scrapy.utils.conf import arglist_to_dict
from twisted.python.usage import UsageError


class Command(ScrapyCommand):
    
  requires_project = True

  # 这个函数是定义语法
  def syntax(self):
    return '[options]'
   
  # 简单介绍这条命令
  def short_desc(self):
    return 'Runs all of the spiders'
  # 增加命令补充
  def add_options(self, parser):
    ScrapyCommand.add_options(self, parser)
    parser.add_option("-a", dest="spargs", action="append", default=[], metavar="NAME=VALUE",
              help="set spider argument (may be repeated)")
    parser.add_option("-o", "--output", metavar="FILE",
              help="dump scraped items into FILE (use - for stdout)")
    parser.add_option("-t", "--output-format", metavar="FORMAT",
              help="format to use for dumping items with -o")

  def process_options(self, args, opts):
    ScrapyCommand.process_options(self, args, opts)
    try:
      opts.spargs = arglist_to_dict(opts.spargs)
    except ValueError:
      raise UsageError("Invalid -a value, use -a NAME=VALUE", print_help=False)

  # 最重要的部分
  def run(self, args, opts):
    # settings = get_project_settings()
    # 这里的self.crawler_process就是源码中crawler.py中的CrawlerProcess类的实例
    spider_loader = self.crawler_process.spider_loader
    # spider应该就在args这个列表中也可以通过spider_loader.list()来获取spider列表
    for spidername in args or spider_loader.list():
      print "*********cralall spidername************" + spidername
      # 开启每一个spider
      self.crawler_process.crawl(spidername, **opts.spargs)
    # 开始运行爬虫
    self.crawler_process.start()
```
3.setting.py
`COMMANDS_MODULE = 'wdzj.commands'`
commands文件夹所在的位置


有道云笔记链接：

http://note.youdao.com/noteshare?id=0011b1165ad88f1482e1206b76976027&sub=0F222EB96F984C7FBF48448B236332EC
