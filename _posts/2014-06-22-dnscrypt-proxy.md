---
layout: post
published: true
title: 使用dnscrypt解决dns污染问题 
date: 2014-06-22
tags: dnscrypt dnscrypt-proxy
---

###前言
在开始写这篇文章之前，先要普及一个知识*中国长城防火墙*（英文名：Great Firewall of China），好伟大的样子，是XX专门用来对网民进行网络封锁，闭关锁国的的工具。最近一批google.com, youtube.com, facebook.com, dropbox.com等一批国外知名网站，均无法正常访问，就是拜其所赐。它所实现的主要屏蔽技术有IP封锁，关键字过滤，域名劫持与污染以及HTTPS证书过滤四种。本文主要通过dnscrypt技术反其dns劫持与污染。

先了解一下什么是DNS劫持和DNS污染，其实两者并不是一个概念。大家都知道主机之间的通信通过ip来标识对方主机，但是IP不容易记忆，后来提出了域名的概念，比如www.baidu.com, 域名与IP之间就是通过DNS解析联系起来，实现了域名与IP之间的映射。这份映射关系存储在DNS服务器上。

*什么是DNS劫持*

DNS劫持一般发生在某些网络运营商身上，DNS劫持就是劫持了DNS服务器，获取DNS服务器的控制权。通过某些手段修改这些域名的目的解析IP地址。DNS劫持通过篡改DNS服务器上的数据，给用户返回一个错误的查询结果来实现的。

DNS劫持症状：在某些地区的用户在成功连接宽带后，首次打开任何页面都指向ISP提供的“电信互联星空”、“网通黄页广告”等内容页面。还有就是曾经出现过用户访问Google域名的时候出现了百度的网站。这些都属于DNS劫持

应对DNS劫持，只需要在网络配置中把DNS服务器地址配置成国外的DNS服务器地址就可以解决，比如谷歌提供的DNS服务器地址：8.8.8.8 8.8.4.4

*什么是DNS污染*

DNS污染是一种让一般用户由于得到虚假目标主机IP而不能与其通信的方法，是一种DNS缓存投毒攻击（DNS cache poisoning）。其工作方式是：由于通常的DNS查询没有任何认证机制，而且DNS查询通常基于的UDP是无连接不可靠的协议，因此DNS的查询非常容易被篡改，通过对UDP端口53上的DNS查询进行入侵检测，一经发现与关键词相匹配的请求则立即伪装成目标域名的解析服务器（NS，Name Server）给查询者返回虚假结果。

DNS污染症状：目前一些被禁止访问的网站很多就是通过DNS污染来实现的，例如YouTube、Facebook、DropBox等网站。

对于DNS污染，普通用户是不能够通过简单地设置国外DNS服务器就能解决的。需要用到这篇所讲的重点：DnsCrypt-proxy，我通过这种方式解决了对Dropbox的访问封锁。

先看下谷歌DNS服务器(8.8.8.8)对www.dropbox.com的解析情况
{% highlight bash %}
[root@AY140601130849615931Z dnscrypt-proxy-1.4.0]# dig www.dropbox.com @8.8.8.8

; <<>> DiG 9.8.2rc1-RedHat-9.8.2-0.23.rc1.el6_5.1 <<>> www.dropbox.com @8.8.8.8
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16429
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;www.dropbox.com.		IN	A

;; ANSWER SECTION:
www.dropbox.com.	20803	IN	A	59.24.3.173

;; Query time: 5 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Sun Jun 22 15:44:22 2014
;; MSG SIZE  rcvd: 49
{% endhighlight %}

电信DNS服务器(114.114.114.114)对www.dropbox.com的解析情况
{% highlight bash %}
[root@AY140601130849615931Z dnscrypt-proxy-1.4.0]# dig www.dropbox.com @114.114.114.114

; <<>> DiG 9.8.2rc1-RedHat-9.8.2-0.23.rc1.el6_5.1 <<>> www.dropbox.com @114.114.114.114
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 41685
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;www.dropbox.com.		IN	A

;; ANSWER SECTION:
www.dropbox.com.	31596	IN	A	59.24.3.173

;; Query time: 30 msec
;; SERVER: 114.114.114.114#53(114.114.114.114)
;; WHEN: Sun Jun 22 15:44:33 2014
;; MSG SIZE  rcvd: 49
{% endhighlight %}
