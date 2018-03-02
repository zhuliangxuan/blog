---
title: 使用github免费搭建程序员博客
date: 2018-03-02 17:37:16
tags: github
---
# 一、准备工作
以windows为例：<br/>
github账号:[https://github.com/](https://github.com/)<br/>
node.js软件:[http://nodejs.cn/download/](http://nodejs.cn/download/)<br/>
git软件:[https://git-scm.com/](https://git-scm.com/)<br/>
# 二、博客搭建
## 1、github新建仓库
仓库名为 用户名.github.io（例如zhuliangxuan.github.io）
## 2、软件安装确认
### 2.1 git安装（一路安装，略）
### 2.2 node.js安装（一路安装，略）
### 2.3 Hexo安装（需git和node.js安装完成）
新建一个文件夹用于作为博客目录，进入该目录，打开git bush命令窗口
```
# Hexo客户端安装
npm i -g hexo

# Hexo服务端安装
npm i hexo-server

# Hexo部署插件安装
npm install hexo-deployer-git --save
```
### 2.4 软件确认
查看git版本
```
git version

//git version 2.16.1.windows.2
```
查看node版本
```
node -v

//v8.9.3
```
查看hexo版本
```
hexo -v

//hexo: 3.6.0
hexo-cli: 1.1.0
os: Windows_NT 10.0.16299 win32 x64
http_parser: 2.7.0
node: 8.9.3
v8: 6.1.534.48
uv: 1.15.0
zlib: 1.2.11
ares: 1.10.1-DEV
modules: 57
nghttp2: 1.25.0
openssl: 1.0.2n
icu: 59.1
unicode: 9.0
cldr: 31.0.1
tz: 2017b

```
## 3、创建ssh key添加到github
### 3.1 生成ssh key
```
ssh-keygen -t rsa -C "zhuliangxuan@zstax.com"
```
将生成的id_rsa.pub用notepad++打开，复制其中的内容
### 3.2 将key添加到github
登录github，点击settings-SSH keys and GPG keys，添加一个ssh key，将复制的内容粘贴过来
### 3.3 验证ssh key
git命令行
```
ssh -T git@github.com

//Hi zhuliangxuan! You've successfully authenticated, but GitHub does not provide shell access.
//表示连接成功
```
## 4、部署
### 4.1 初始化
```
hexo init
```
### 4.2 部署
```
hexo clean
hexo generate
hexo deploy
```
### 4.3 写博客
- 方式一：导入完成的md博客文件，复制粘贴到目录source/_posts中
- 方式二：使用命令 hexo new post “文章名字” 就可新建文章啦，建立好的文章在 source/_posts 中，你可以用markdown语法编辑内容就可以
- 完成之后，重新使用部署命令发布
## 5、访问
访问`https://yourname.github.io`，进行访问。例如[https://zhuliangxuan.github.io/](https://zhuliangxuan.github.io/)
# 三、主题与优化