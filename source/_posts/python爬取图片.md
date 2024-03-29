---
title: python 爬取图片
abbrlink: 37771
date: 2017-11-01 14:39:36
tags:
 - 爬虫
categories:
 - python
---
从V2EX 看到有人在爬煎蛋的获赞数高的妹子图，刚好这几天刚接触python,就从那位发贴者的网站爬取他抓取的图片数据，并以txt文件保存至本地。
<!-- more -->
#### 爬虫入口文件
```python
import url_manager
import html_download
import html_parser
import data_output
class Spider(object):
    def __init__(self):
        self.urls = url_manager.UrlManager()
        self.html_download = html_download.HtmlDownload()
        self.html_parser = html_parser.HtmlParser()
        self.html_output = data_output.OutPut()
    def craw(self, root_url, page_url):
        self.urls.add_new_url(root_url)
        i = 1
        while self.urls.has_new_urls():
            new_url = self.urls.get_new_url()
            print 'craw %d : %s' % (i, new_url)
            cont = self.html_download.download(new_url)
            url, data = self.html_parser.paser(page_url, cont)
            self.urls.add_new_url(url)
            self.html_output.create(data)
            # i = i + 1
if __name__ == '__main__':
    root_url = '***'
    page_url = '**'
    obj_Spider = Spider()
    obj_Spider.craw(root_url, page_url)
```
#### 数据采集器
```python
import urllib2
class HtmlDownload(object):
    def download(self, url):
        if url is None:
            return None
        request = urllib2.Request(url)
        request.add_header('User-Agent', 'Mozilla/5/0')
        response = urllib2.urlopen(request)
        if response.getcode() != 200:
            return None
        return response.read()
```
#### 数据处理器
```python
import re
import urlparse
from bs4 import BeautifulSoup
class HtmlParser(object):
    def __init__(self):
        self.old_request_url = set()
    def paser(self, page_url, cont):
        if page_url is None or cont is None:
            return
        soup = BeautifulSoup(cont, 'html.parser', from_encoding='utf-8')
        new_url = self._get_new_url(page_url, soup)
        data = self._get_data(page_url, soup)
        return new_url, data
    def _get_data(self, page_url, soup):
        links = soup.find_all('img')
        list =[]
        for link in links:
            url = link.attrs.get('data-original')
            if url is not None:
                new_full_url = urlparse.urljoin(page_url, url)
                list.append(new_full_url)
        return list
    def _get_new_url(self, page_url, soup):
        links = soup.find_all('a', href=re.compile(r"/meizi/rank/"))
        for link in links:
            if link['href'] not in self.old_request_url:
                new_url = link['href']
            self.old_request_url.add(link['href'])
        new_full_url = urlparse.urljoin(page_url, new_url)
        return new_full_url
```
#### 数据保存
```python
class OutPut(object):
    def create(self, data):
        for i in data:
            links = open("data.txt")
            if i not in links:
                fout = open('data.txt', 'a')
                fout.write(i+'\n')
        fout.close()
```