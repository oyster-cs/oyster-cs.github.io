---
layout: post
title: "小坑：Flask server不能访问"
category: Python
tags: [Flask, 坑]
date: 2016-07-13
---

### Problem

部署Flask server时碰到的一个问题：我在服务器上运行了我的Flask server程序，但是我本地浏览器却不能打开我的网页（http://remote_hostname:5000），把hostname换成ip也不行。然后我又把防火墙关了一遍，还是不行。在本地试图通过tcp连接到远程的5000端口，connection refused了，而连接到其他一些端口是可以成功的。

### Reason

上述表现基本确定不是防火墙的锅，查了下是因为我直接`app.run()`，它的默认参数是host='127.0.0.1'，官方文档也说了：

> Set this to '0.0.0.0' to have the server available externally as well. Defaults to '127.0.0.1'.

### Solution

把app.run()改为app.run(host='0.0.0.0')。