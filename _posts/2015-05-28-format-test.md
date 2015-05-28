---
layout: post
title: the latest one
description: the coming blog
keywords: blog
---



<!--
![Seattle](/images/Seattle.png)
-->






In this tutorial, we’ll assume that Scrapy is already installed on your system. If that’s not the case, see Installation guide.
多进程与多线程的乱七八糟的事情多进程与多线程的乱七八糟的事情多进程与多线程的乱七八糟的事情多进程与多线程的乱七八糟的事情多进程与多线程的乱七八糟的事情
We are going to use Open directory project (dmoz) as our example domain to scrape.

This tutorial will walk you through these tasks:

    Creating a new Scrapy project
    Defining the Items you will extract
    Writing a spider to crawl a site and extract Items
    Writing an Item Pipeline to store the extracted Items

Scrapy is written in Python. If you’re new to the language you might want to start by getting an idea of what the language is like, to get the most out of Scrapy. If you’re already familiar with other languages, and want to learn Python quickly, we recommend Learn Python The Hard Way. If you’re new to programming and want to start with Python, take a look at this list of Python resources for non-programmers.
Creating a project

Before you start scraping, you will have set up a new Scrapy project. Enter a directory where you’d like to store your code and then run:

scrapy startproject tutorial

This will create a tutorial directory with the following contents:

tutorial/
    scrapy.cfg
    tutorial/
        __init__.py
        items.py
        pipelines.py
        settings.py
        spiders/
            __init__.py
            ...

These are basically:

    scrapy.cfg: the project configuration file
    tutorial/: the project’s python module, you’ll later import your code from here.
    tutorial/items.py: the project’s items file.
    tutorial/pipelines.py: the project’s pipelines file.
    tutorial/settings.py: the project’s settings file.
    tutorial/spiders/: a directory where you’ll later put your spiders.

Defining our Item

Items are containers that will be loaded with the scraped data; they work like simple python dicts but provide additional protection against populating undeclared fields, to prevent typos.

They are declared by creating a scrapy.Item class and defining its attributes as scrapy.Field objects, like you will in an ORM (don’t worry if you’re not familiar with ORMs, you will see that this is an easy task).

We begin by modeling the item that we will use to hold the sites data obtained from dmoz.org, as we want to capture the name, url and description of the sites, we define fields for each of these three attributes. To do that, we edit items.py, found in the tutorial directory. Our Item class looks like this:

import scrapy

class DmozItem(scrapy.Item):
    title = scrapy.Field()
    link = scrapy.Field()
    desc = scrapy.Field()

This may seem complicated at first, but defining the item allows you to use other handy components of Scrapy that need to know how your item looks.
Our first Spider

Spiders are user-written classes used to scrape information from a domain (or group of domains).

They define an initial list of URLs to download, how to follow links, and how to parse the contents of those pages to extract items.

To create a Spider, you must subclass scrapy.Spider and define the three main mandatory attributes:

    name: identifies the Spider. It must be unique, that is, you can’t set the same name for different Spiders.

    start_urls: is a list of URLs where the Spider will begin to crawl from. So, the first pages downloaded will be those listed here. The subsequent URLs will be generated successively from data contained in the start URLs.

    parse() is a method of the spider, which will be called with the downloaded Response object of each start URL. The response is passed to the method as the first and only argument.

    This method is responsible for parsing the response data and extracting scraped data (as scraped items) and more URLs to follow.

    The parse() method is in charge of processing the response and returning scraped data (as Item objects) and more URLs to follow (as Request objects).

This is the code for our first Spider; save it in a file named dmoz_spider.py under the tutorial/spiders directory:
