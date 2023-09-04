---
title: Github+hexo搭建博客遇到的坑
date: 2022-04-05 12:41:48
categories:
- 技术
tags:
- hexo
- github
---

## hexo部署问题

按照教程部署完后发现页面仍旧没法更改，折腾大半天后发现github最近将默认分支的名字从master改成了main，而`_config.yml`中一般都将`branch`设置为master，导致master成为了副分支，github pages默认应用主分支main，所以没法更改页面（所以说政治正确真是害死人...大家约定俗成的事说改就改）

## 部署慢问题

还没有发现问题所在，大概只能理解成访问github困难导致即使push成功网页也会刷新不及时。

## 图片CDN问题

nexmoe主题中的图片使用CDN保存，所以无法使用本地路径，但是图片也不能直接保存至`username.github.io`中，因为每次push都会导致仓库中保存的图片被删，所以在github中新建了一个仓库用来保存图片。

## 评论系统

gitment已经停止使用！请用gitalk替代。