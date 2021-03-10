# DNSLog注入

DNS（Domain Name System）域名系统

https://www.cloudflare.com/zh-cn/learning/dns/what-is-dns/

## DNS解析

域名是为了方便记忆而专门建立的一套地址转换系统，要访问一台互联网上的服务器，最终还必须通过[IP地址](https://baike.baidu.com/item/IP地址)来实现，域名解析就是将域名重新转换为IP地址的过程。一个域名对应一个IP地址，一个IP地址可以对应多个域名；所以多个域名可以同时被解析到一个IP地址。域名解析需要由专门的域名解析服务器(DNS)来完成。

## DNS原理

**根服务器->顶级域名服务器->二级域名服务器->三级域名服务器**

![img](https://bkimg.cdn.bcebos.com/pic/4e4a20a4462309f72ac61ab6720e0cf3d6cad6ed?x-bce-process=image/watermark,image_d2F0ZXIvYmFpa2UxNTA=,g_7,xp_5,yp_5/format,f_auto)