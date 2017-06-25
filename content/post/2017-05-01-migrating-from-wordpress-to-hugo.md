---
title: Migrating from WordPress to HUGO
date: '2017-05-01'
slug: ''
categories: [HUGO, Wordpress, R]
tags: []
lastmodifierdisplayname: Claudia Vitolo
lastmodifieremail: cvitolodev@gmail.com
---

Few years ago I started blogging, mainly to collect notes and interesting experiments for my PhD. I used wordpress and I found it very convenient at that time. Nowdays, using R for just about everything, I could not resist to the temptation of making my own blog using blogdown and HUGO. However I did not want to maintain two blogs so I had to:

  1. export my wordpress content
  2. convert this content to markdown
  3. set up a hugo blog
  
#### Export wordpress content

I started googling 'how to migrate from wordpress to markdown'. I found out that it is possible to export the entire wordpress blog (in xml format) using the exporter tool in the wordpress admin dashboard. Just log in to your wordpress -> My Sites -> Settings -> Export -> click on the button 'Export all'.

Let's call the downloaded file wp.xml 

#### Convert wordpress content to markdown

Amongst the various options I decided to use for [exitwp](https://github.com/thomasf/exitwp), it is a simple and very efficient solution to convert the xml file to a series of markdown files. 

I cloned the githup repository locally, pasted the wp.xml file in the exitwp/wordpress-xml directory, and run

#### Set up a hugo blog

I have built this website using [blogdowm](https://github.com/rstudio/blogdown) and the [docdock theme](https://themes.gohugo.io/theme/docdock) for [HUGO](https://gohugo.io/). 
