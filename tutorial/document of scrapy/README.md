# 1.tool commands
scrapy��һЩ��������
## 1.1 �鿴���п�������
scrapy -h

ȫ�����
 ```   
    startproject
    
    settings
    
    runspider
    
    shell
    
    fetch
    
    view
    
    version
 ```

��Ŀ��project-only������
```
    crawl
    check
    list
    edit
    parse
    genspider
    bench
```
## 1.2������Ŀ
`scrapy startproject projectname`

## 1.3ʹ��spider������ȡ
`scrapy crawl spidername`

## 1.4 ����contract���
`scrapy check -l     �鿴����Щ���Լ���
scrapy check`

## 1.5 �г�������Ŀ�е����п��õ�spider
`scrapy list`

## 1.6 ʹ��EDITOR���趨�ı༭���༭������spider��������ֻ�ṩһ����ݷ�ʽ
`scrapy edit spidername`

## 1.7 ʹ��scrapy��������Downloader�����ظ�����url��������ȡ���������͵���׼���
`scrapy fetch --nolog url
    ���磺scrapy fetch --nolog http://www.baidu.com ��������������ҳ������ҳԴ���뷵��`
    
## 1.8 view����Url,��������ҳԴ���뱣����ļ���������ҳ
`scrapy view url`

��������д򿪸�����url�� ����scrapy spider��ȡ������ʽչ�֣��Դ�������Լ��spider����ȡ����ҳ�棬��ȷ��������������ҳ��
    
## 1.9 shell �ն�
`scrapy shell -s 'User-Agent'='...' url`

## 1.10 �����н���ҳ��
`scrapy parse url --callback parsename`

�Ը����Ľ�����������url�������дcallback��ʹ��Ĭ�ϵ�parse��������url

֧�ֵ�ѡ�
* --spider=SPIDER: �����Զ����spider��ǿ��ʹ���ض���spider
* --a NAME=VALUE: ����spider�Ĳ���(���ܱ��ظ�)
* --callback or -c: spider�����ڽ�������(response)�Ļص�����
* --pipelines: ��pipeline�д���item
* --rules or -r: ʹ�� CrawlSpider ����������������������(response)�Ļص�����
* --noitems: ����ʾ��ȡ����item
* --nolinks: ����ʾ��ȡ��������
* --nocolour: ����ʹ��pygments�������ɫ
* --depth or -d: ָ��������������Ĳ����(Ĭ��: 1)
* --verbose or -v: ��ʾÿ���������ϸ��Ϣ

## 1.11 ��ȡscrapy����
`scrapy setting -- get settings.py`

��settings.py�л�ȡ����

## 1.12 ��δ������Ŀ�����������һ����д��python�ļ��е�spider
`scrapy runspider spidername.py`

## 1.13 ��ȡscrapy�汾
`scrapy version`

## 1.14 ����benchmark����
`scrapy bench`

���Լ��scrapy�ڵ�����������ȡ�ٶ�

# 2.�Զ�����Ŀ����

����ͨ��COMMANDS_MODULE������Լ�����Ŀ�������ȡ���е�spider
ͨ��scrapy/commands��Scrapy commandsΪ���˽����ʵ�����

������һ�����ӣ�һ����������е����棨spider��

1.��spiderĿ¼��ͬ��Ŀ¼�´���commands��������__init__.py�ļ����ļ��У�

2.�½�crawlall.py�ļ�
```
from scrapy.commands import ScrapyCommand
from scrapy.crawler import CrawlerRunner
from scrapy.utils.conf import arglist_to_dict
from twisted.python.usage import UsageError


class Command(ScrapyCommand):
    
  requires_project = True

  # ��������Ƕ����﷨
  def syntax(self):
    return '[options]'
   
  # �򵥽�����������
  def short_desc(self):
    return 'Runs all of the spiders'
  # ���������
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

  # ����Ҫ�Ĳ���
  def run(self, args, opts):
    # settings = get_project_settings()
    # �����self.crawler_process����Դ����crawler.py�е�CrawlerProcess���ʵ��
    spider_loader = self.crawler_process.spider_loader
    # spiderӦ�þ���args����б���Ҳ����ͨ��spider_loader.list()����ȡspider�б�
    for spidername in args or spider_loader.list():
      print "*********cralall spidername************" + spidername
      # ����ÿһ��spider
      self.crawler_process.crawl(spidername, **opts.spargs)
    # ��ʼ��������
    self.crawler_process.start()
```



�е��Ʊʼ����ӣ�

http://note.youdao.com/noteshare?id=0011b1165ad88f1482e1206b76976027&sub=0F222EB96F984C7FBF48448B236332EC