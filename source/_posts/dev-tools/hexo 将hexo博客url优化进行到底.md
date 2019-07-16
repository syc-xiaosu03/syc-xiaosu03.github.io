---
title: Hexo 将hexo博客url优化进行到底
entitle: 'permalink-optimize-hexo'
author: 小苏
avatar: /images/favicon.png
authorLink: 'https://www.???.com'
authorAbout: 'https://about.???.com'
authorDesc: 在写bug的康庄大道上一骑绝尘
categories: 开发工具
timestamp: 1528003174
date: 2018-06-03 13:19:34
tags: 
	- hexo
	- 网站
keywords:
photos:
	- /img/2017/hexo-blog-basic.jpg
description: 半个月前，患有强迫症晚期的我又拾起了hexo。这次通过阅读源码，加上了一直想要的时间戳变量。希望你们喜欢！
---

## 事件源自2017年元旦时的一篇博文

[在hexo博客中打造相对完美的URL](https://shuoit.net/front-end/hexo-links/1483800845.html)

那篇文章，我向大家介绍了如何在hexo博客中打造一个相对好看、好用的URL链接。然而遗憾的是，**时间戳**在permalink中没法直接使用。当时说了一个笨办法，就是模板中手动去加时间戳，然后文章生成是再取出来。不知道使用过的朋友有没有喷我...

时隔一年多，我又准备玩hexo了，理由是被类似**为知笔记**这种东西伤透了心。

然而也是一年多过去了，官方并没有做这样的支持，那我就不高兴了。在强迫症的驱使下，我读了他的源码，发现加这个时间戳相当简单，所以我义不容辞的提了这个[PR](https://github.com/hexojs/hexo/pull/3162)。这是半个月前发生的事情，在我写这篇文章的时候，官方还没有Merge。所以你如果想在**permalink**中使用时间戳。办法就是人肉把**node_module**文件夹下指定的文件做修改。怎么改？改哪个文件？改成啥样？请直接看那个PR。

如果一切顺利，你现在就能愉快的玩耍了。比如：

> https://shuoit.net/front-end/permalink-optimize-hexo/1528003174.html

当然别忘了配置站点**_config.yml**

> permalink: :category/:entitle/:timestamp.html

最后，祝读者们儿童节快乐！


