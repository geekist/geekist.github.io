---
title: Nginx配置SSL方法
featured: images/nginx/nginx.png
layout: post
---

<p>Nginx常用命令</p>

## 启动Nginx

nginx -c /usr/local/nginx/conf/nginx.conf  //-c参数指定了要加载的nginx配置文件路径

## 停止Nginx

步骤1：查询nginx主进程号

ps -ef | grep nginx
在进程列表里 面找master进程，它的编号就是主进程号了。

步骤2：发送信号

复制代码
从容停止Nginx：
kill -QUIT 主进程号  
例如：kill -QUIT 16391

快速停止Nginx：
kill -TERM 主进程号  

强制停止Nginx：
kill -9 主进程号  

## 重启Nginx

/nginx -s reload 