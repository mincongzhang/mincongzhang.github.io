---
layout: post
title: 用Ruby on Rails写一个网站
category: 后端
description: ruby on rails 和一些创建网站的笔记
tags: ["Rails"]
---

虽然本科毕设用Django写过一个简单的网站用来做在线音频听力测试, 但时间过了挺久, 而且那时写的挺乱, 现在都不敢说用过Django了. 这次有机会用ruby on rails再写一个网站, 赶紧记点笔记.

### MVC框架
以前Django教程就总说这个框架, 下面是看过维基后我的理解:

M(Model):网站的模型, 算法之类
V(View): 图形界面, 前端
C(Controller): 控制器, 转发请求,对请求进行处理

### REST 架构
资源可以被创建、读取、更新和删除，这些操作简称 CRUD, 这些操作正好对应HTTP协议提供的GET、POST、PUT和DELETE方法。


遇到的问题和对应的解决方案:
http://stackoverflow.com/questions/27139007/cant-migrate-database-after-scaffold-section-2-2-ruby-on-rails-tutorial-michae

http://stackoverflow.com/questions/17285873/couldnt-find-file-jquery-in-rails-project

参考链接:
1.Rails入门: http://guides.ruby-china.org/getting_started.html