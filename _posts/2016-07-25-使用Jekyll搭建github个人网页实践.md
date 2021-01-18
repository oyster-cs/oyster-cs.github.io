---
layout: post
title: "使用Jekyll搭建github个人网页实践"
category: Web
tags: [Jekyll]
date: 2016-07-25
---

拖延了很久，终于下决心重新整一整这个blog。为了整这个，又重新研究了下`jekyll`。

## I. 使用Jekyll

为什么在github上部署个人页面需要jekyll？因为github官方的页面就是用的liquid模板，而jekyII是一个比较流行了使用liquid模板来生成静态页面网站的工具（jekyll用了[liquid](https://github.com/Shopify/liquid)来作为写html模板的工具，快速上手可以参考[这里](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers)。）。它可以像flask一样在你本地运行一个webserver，但你不需要care它后台的逻辑，这些都帮你封装好了，也没有暴露给你，你只需要写好一些符合liquid格式的html文件（liquid可以参考Jekyll给定的一些[变量](http://jekyllrb.com/docs/variables/)和[filter](http://jekyllrb.com/docs/templates/)来写（你看，Jekyll给你的能玩的东西也就这些了，是不是挺“傻瓜”的，当然它并没有限制你写js，但是后端相关的基本都剥离了））以及符合jekyll规定的文件和文件夹命名就可以了，jekyll会根据以下的逻辑来处理这些html模板和markdown的文章：

> 1. Jekyll collects data.
>
>    Jekyll scans the posts directory and collects all posts files as post objects. It then scans the layout assets and collects those and finally scans other directories in search of pages.
>
> 2. Jekyll computes data.
>
>    Jekyll takes these objects, computes metadata (permalinks, tags, categories, titles, dates) from them and constructs one big site object that holds all the posts, pages, layouts, and respective metadata. At this stage your site is one big computed ruby object.
>
> 3. Jekyll liquifies posts and templates.
>
>    Next jekyll loops through each post file and converts (through markdown or textile) and liquifies the post inside of its respective layout(s). Once the post is parsed and liquified inside the the proper layout structure, the layout itself is "liquified".
>
>    Liquification is defined as follows: Jekyll initiates a Liquid template, and passes a simpler hash representation of the ruby site object as well as a simpler hash representation of the ruby post object. These simplified data structures are what you have access to in the templates.
>
> 4. Jekyll generates output.
>
>    Finally the liquid templates are "rendered", thereby processing any liquid syntax provided in the templates and saving the final, static representation of the file.

以上，简单说就是Jekyll会把所有markdown的东西（转换成html格式）放到模板里面，然后render就生成了我们打开网页看到的html文件啦。

至于放到github上，每次我们push一个commit的时候它会自动重新用Jekyll来编译生成新的静态页面的，从而保证了内容是最新的。

<!--break-->

## II. 静态页面 VS. 动态页面

Jekyll生成的网页是静态页面，也就是说你从浏览器里输入了你的博客网站后是直接从服务器（这里就是github）上下载了整个网页的（这里说的网页包括了页面的html文件和所import的javascript文件），服务器并不需要动态地去生成任何东西（比如从一个数据库拿东西之类）。另一个角度来理解，一旦服务器端用Jekyll编译生成了你的页面，这些页面就不会再变了。

而之前比较火的写博客的工具`Wordpress`生成的则是动态页面，也就是说每次你浏览器访问你的博客的时候，页面是会动态变化的，服务器端会帮你去管理一些动态的东西（比如留言版这个东西肯定是动态的，每个新的留言都要动态地放到一个DB里面去），然后整合这些动态的东西到你的页面里面，最后加载到你的浏览器里面。

当然，动态和静态并不是绝对的。比如我可以在Jekyll生成的页面里面用JavaScript来call一个第三方的存储服务，每次去读取第三方存储里面的内容加载到我的页面里去，这样也可以实现一个留言板的功能（随手找一个项目：[https://github.com/klcodanr/Jekyll-Disqus-Forum](https://github.com/klcodanr/Jekyll-Disqus-Forum)）。这里区分动态页面还是静态页面主要是看从服务器端加载到浏览器里的内容是否变化为准，也就是说web server是否提供动态加载的功能。

最后，相比动态页面，静态页面的优点和缺点：

- 优点:
  - 安全：这个是对服务器端而言的，因为只提供静态的文件，所以就没有运行用户提供的代码所带来的风险
  - 轻量：web server只需要提供静态的页面，动态生成的部分被剥离出去了，管理起来也很容易
  -  加载更快：前提当然是你没有使用第三方服务。因为服务器端不需要动态加载其他地方的内容了，响应会更快一些
- 缺点：
  - 需要使用第三方服务来实现动态的内容，提高了需要这些功能的博客主的维护成本

## III. Markdown的选择

github官方推荐使用kramdown，它是markdown语法的超集，添加了许多新的语法支持，也有一些细微的不同（具体不同可参考[http://platinhom.github.io/2015/11/06/Kramdown-note/](http://platinhom.github.io/2015/11/06/Kramdown-note/)）。