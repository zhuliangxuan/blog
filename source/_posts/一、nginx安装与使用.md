---
title: nginx(1)简介和安装
date: 2018-04-12 17:37:16
type: "tags"
tags: 
  - nginx
toc: true
comments: true
---
## Nginx简介
Nginx (engine x) 是一个高性能的HTTP和反向代理服务器，也是一个IMAP/POP3/SMTP服务器。Nginx是由伊戈尔·赛索耶夫为俄罗斯访问量第二的Rambler.ru站点（俄文：Рамблер）开发的，第一个公开版本0.1.0发布于2004年10月4日。
其将源代码以类BSD许可证的形式发布，因它的稳定性、丰富的功能集、示例配置文件和低系统资源的消耗而闻名。2011年6月1日，nginx 1.0.4发布。
Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，并在一个BSD-like 协议下发行。其特点是占有内存少，并发能力强，事实上nginx的并发能力确实在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、京东、新浪、网易、腾讯、淘宝等。<br/>
参考：[Nginx百度百科](https://baike.baidu.com/item/nginx/3817705?fr=aladdin)
## Nginx的安装
本次使用ubuntu系统进行演示
### 查看安装软件
- 查看安装的所有软件
```
dpkg -l
```
![dpkg-l](/img/dpkg-l.png)
- 查看安装的具体软件
```
dpkg -l | grep nginx
```
![dpkg-l-nginx](/img/dpkg-l-nginx.png)
### 卸载已安装软件
由于本人电脑已安装nginx，先演示卸载软件，再重新安装
```
#卸载命令
sudo apt-get remove nginx nginx-common #卸载删除除了配置文件以外的所有文件。

sudo apt-get purge nginx nginx-common # 卸载所有东东，包括删除配置文件。

sudo apt-get autoremove # 在上面命令结束后执行，主要是卸载删除Nginx的不再被使用的依赖包。

sudo apt-get remove nginx-full nginx-common #卸载删除两个主要的包。

sudo service nginx restart  #重启nginx
```
### 安装nginx
```
sudo apt-get update
sudo apt-get install nginx
```
## Nginx的启动，停止和重启
```
#启动命令
/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
```
访问http://localhost/
![dpkg-l](/img/nginx.png)
```
#停止命令
ps -ef|grep nginx #查看进程号
kill -QUIT 4474 #杀死进程
```
![kill-nginx](/img/kill-nginx.png)
```
#重启方式1
#进入/usr/local/nginx/sbin目录下
./nginx -s reload

#重启方式2
#查找当前nginx进程号
kill -HUP 5170
```
![reload1-nginx](/img/reload1-nginx.png)
参考文章：[nginx启动、重启、关闭](https://blog.csdn.net/w1014074794/article/details/51881050)