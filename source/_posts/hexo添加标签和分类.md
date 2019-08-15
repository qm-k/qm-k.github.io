---
title: hexo添加标签和分类
date: 2019-08-15 10:24:54
tags:
- code
categories:
- 编程
- hexo
---
# 前言
在我们的博客文章多了以后，需要对其进行归类以方便查找，hexo提供“分类”和“标签”用于文章的归类管理。一下记录如何在hexo框架下添加标签和分类。  
<!--more-->
最终效果如下所示：  

标签效果：  
{% asset_img tags.jpg  %} 
分类效果： 
{% asset_img fenlei.jpg  %} 

# 配置
* 修改配置文件
首先打开 `theme/next/_config.yml`，找到如下配置，删除”categories”和“tags”前的注释符#。
```
menu:
  home: / || home
  #about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
```
保存并关闭文件。
* 生成页面
此时通过`hexo s`可以在主页看到这两个导航，但是点进去后会得到 Cannot GET /tags/ 或Cannot GET /categories/的页面，需要分别为其创建相应的页面。  

进入博客根目录执行
```
$ hexo create page tags
$ hexo create page categories
```
成功后提示
```
INFO  Created: ~/Documents/blog/source/tags/index.md
INFO  Created: ~/Documents/blog/source/categories/index.md
```
分别打开这两个index.md的文件，修改其内容。

tags/index.md  
```
---
title: 标签
date: 2018-12-01 01:12:43
type: "tags"
---
```
categories/index.md  
```
---
title: 分类
date: 2018-12-01 00:52:39
type: "categories"
---
```
保存以后就能成功生成这两个页面。
# 给文章加上分类和标签
上述步骤仅仅是生成了分类和标签的导航栏，实际的分类或标签条目需要在文章中添加才行，打开要分类的文章，在文件头添加“tags”标签或“categories”进行分类。
```  
title: hexo 添加标签和分类 
date: 2018-12-01 01:06:34
categories: 
- hexo
- config
- tags
tags:
- hexo
- tags
```
添加“- xxx”作为categories或tags的条目，在“tags”下添加多个条目表示给文章打了多个标签；而如果在“categories”下增加多个条目，则是一个嵌套的结构，例如上述配置会产生如下效果：  
{% asset_img hexo_categories.jpg  %}

# 部署生效
完成以上所有之后就可一看到效果了。  
```
$ hexo clean #可以不clean
$ hexo g
$ hexo d
```

# 参考资料
https://hexo.io/zh-cn/docs/front-matter