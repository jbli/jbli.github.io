---
layout: post
title:  "利用jekyll在github上建个人博客"
date:   2014-03-14 14:14:40
categories: tools
tags:  jekyll  github 博客
---
利用jekyll在github上建个人博客的优势: 

- github做版本管理
- jekyll灵活定制博客主题风格
- 入手足够的简单

jekyll安装和使用
-----------
##安装
```
~ $ gem install jekyll
```
##基本使用
```
~ $ gem install jekyll
~ $ jekyll new myblog
~ $ cd myblog
~/myblog $ jekyll serve --watch
# => Now browse to http://localhost:4000
```

##主题模板
通过 jekyll new 生成的模板比较简陋，网上有许多jekyll 主题的模板，找一款自己喜欢的。<br>
如：
- [掌心](http://www.zhanxin.info/themes.html), 我现在使用的是它的Kunka，源码在github上，可点击[下载](https://github.com/pizn/kunka)

### Kunka使用
注意一定要修改部分_config.yml中

- #Site config部分url，
- follow部分url
- googleAnaly部分id
- #comment config部分的id 
- 其它地方修改根据自己的需求来定。

把自己的博客文章放到_posts目录下，文件名称格式为 "2014-03-14-xxxxx.md"

##测试
进入到博客的根目录下， 执行

```
jekyll serve
```
通过浏览器在127.0.0.1:400 查看

##在github创建仓库
在github上创建仓库 https://github.com/xxx/xxx.github.io, 其中xxx为仓库用户名。<br>
把本地编辑好的博客目录上传到github的xxx.github.io下。<br>
然后通过访问 xxx.github.io ，即可看到个人博客内容。

##注意
- 如查修改博客的内容并上传到github上，但查看xxx.github.io，修改的内容不能显现出来，原因很可能是 博客的语法或格式有问题， 在本地执行 jekyll serve, 一般就会指出错误原因

资源
-------
- [jelly官网](http://jekyllrb.com/docs/home/)
- [jelly官网的中文翻译](http://jekyllcn.com/docs/home/)
- [kunka主题模板](https://github.com/pizn/kunka)
- [GitHub Pages](http://pages.github.com/)





