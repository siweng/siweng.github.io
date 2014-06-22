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

电信DNS服务器(`114.114.114.114`)对`www.dropbox.com`的解析情况
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

`www.dropbox.com`均被解析到 `59.24.3.173` 这个IP, 这是一个韩国IP，使用站长[ping工具](http://ping.chinaz.com/)检测一下,全部超时，我只截取其中的一部分，如果服务器不通或禁ping，则全部超时。
<img src="/images/2014-06-22/ping-ip.png" alt="ping:59.24.3.173" />

无论我们是从谷歌服务器还是电信服务器拿到的数据包，在到手之前均被长城防火墙进行篡改。为了防止这份数据被篡改，我们需要借助DNSCrypt这款工具保证我们的dns查询数据包不被篡改。

[DNSCrypt](https://github.com/opendns/dnscrypt-proxy/)是OpenDNS发布的加密DNS工具，可加密DNS流量，阻止常见的DNS攻击，如重放攻击、观察攻击、时序攻击、中间人攻击和解析伪造攻击。DNSCrypt支持Mac OS和Windows 以及Linux，是防止DNS污染的绝佳工具。我在Mac OS与Centos上均做了尝试。


###安装DNSCrypt-proxy

在`Mac OS` 与 `Linux` 上的安装非常简单，按照项目主页的文档一步一步操作即可。

#####Mac Os 可使用brew工具一键安装
{% highlight bash %}
brew install dnscrypt-proxy 
{% endhighlight %}

#####Linux安装
{% highlight bash %}
#安装依赖
cd /usr/local/src/
wget "https://download.libsodium.org/libsodium/releases/libsodium-0.5.0.tar.gz"
tar -xzvf libsodium-*.tar.gz
cd libsodium-*
./configure
make
make install
{% endhighlight %}

{% highlight bash %}
ldconfig
echo /usr/local/lib > /etc/ld.so.conf.d/usr_local_lib.conf
cd /usr/local/src/
wget "http://download.dnscrypt.org/dnscrypt-proxy/dnscrypt-proxy-1.4.0.tar.bz2"
bunzip2 -cd dnscrypt-proxy-*.tar.bz2 | tar xvf -
cd dnscrypt-proxy-*
./configure
make
make install
{% endhighlight %}

###启动dnscrypt_proxy
dnscrypt_proxy的用法可以通过`man 8 dnscrypt_proxy`查询

在启动之前，需要注意一项，因为dnscrypt_proxy作为查询代理是对通信加密的，这也要求目的dns服务器也要支持，项目主页提供一份[可用列表](https://github.com/jedisct1/dnscrypt-proxy/blob/master/dnscrypt-resolvers.csv)

启动脚本
{% highlight bash %}
[root@AY140601130849615931Z dnscrypt-proxy-1.4.0]# dnscrypt-proxy -R opendns --local-address=0.0.0.0
[NOTICE] Starting dnscrypt-proxy 1.4.0
[INFO] Initializing libsodium for optimal performance
[INFO] Generating a new key pair
[INFO] Done
[INFO] Server certificate #1380734687 received
[INFO] This certificate looks valid
[INFO] Chosen certificate #1380734687 is valid from [2013-10-03] to [2014-10-03]
[INFO] Server key fingerprint is 227C:86C7:7574:81AB:6AE2:402B:4627:6E18:CFBB:60FA:DF92:652F:D694:01E8:EBF2:B007
[NOTICE] Proxying from 0.0.0.0:53 to 208.67.220.220:443
{% endhighlight%}

最后我们可以看到建立了本机53端口与目的主机443端口的链接。按照官网的提示，如果我们安装成功，当我们执行`dig txt debug.opendns.com`，会看到` debug.opendns.com.  0 IN  TXT "dnscrypt enabled (......)"`这样的输出。

{% highlight bash %}
[root@AY140601130849615931Z dnscrypt-proxy-1.4.0]# dig txt debug.opendns.com @localhost

; <<>> DiG 9.8.2rc1-RedHat-9.8.2-0.23.rc1.el6_5.1 <<>> txt debug.opendns.com @localhost
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 39096
;; flags: qr rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;debug.opendns.com.		IN	TXT

;; ANSWER SECTION:
debug.opendns.com.	0	IN	TXT	"server 7.sin"
debug.opendns.com.	0	IN	TXT	"flags 20 0 2f4 4000800000000000000"
debug.opendns.com.	0	IN	TXT	"id 0"
debug.opendns.com.	0	IN	TXT	"source 112.126.67.25:57552"
debug.opendns.com.	0	IN	TXT	"dnscrypt enabled (7165343751484877)"

;; Query time: 181 msec
;; SERVER: 127.0.0.1#53(127.0.0.1)
;; WHEN: Sun Jun 22 17:07:12 2014
;; MSG SIZE  rcvd: 222
{% endhighlight %}

