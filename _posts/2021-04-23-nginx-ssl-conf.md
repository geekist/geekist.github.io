---
title: Nginx配置SSL方法
featured: images/nginx/nginx.png
layout: post
---

<p>基于阿里云CES服务器和Nginx-1.75.5配置SSL证书的步骤，详情请参见阿里云官方文档：https://help.aliyun.com/document_detail/98728.html</p>

## step1：下载ssl证书到本地。
登录ssl证书控制台。
在概览页面，单击证书列表上方的证书状态下拉列表，选择已签发。
单击下载，定位Nginx服务器，单击下载。可以得到两个文件：cert-file-name.pem和cert-file-name.key

## step2：在Nginx独立服务器上安装证书。

1、登录Nginx服务器，
这里选择了MobaXterm工具，打开MobaXterm，选择sessions—>new session 进入session settings页面，选择SSH并设置Remote host和Specify username。

2、上传证书文件到/usr/local/nginx/conf目录。
运行 cd /usr/local/nginx/conf 使用工具将文件上传到该目录

3、编辑Nginx的配置文件nginx.conf,修改与证书相关的配置内容。
vim /usr/local/nginx/conf/nginx.conf
i键进入编辑模式，
添加下面的配置：
```java
server {
    listen 443 ssl;
    #配置HTTPS的默认访问端口为443。
    #如果未在此处配置HTTPS的默认访问端口，可能会造成Nginx无法启动。
    #如果您使用Nginx 1.15.0及以上版本，请使用listen 443 ssl代替listen 443和ssl on。
    server_name yourdomain.com; #需要将yourdomain.com替换成证书绑定的域名。
    root html;
    index index.html index.htm;
    ssl_certificate cert/cert-file-name.pem;  #需要将cert-file-name.pem替换成已上传的证书文件的名称。
    ssl_certificate_key cert/cert-file-name.key; #需要将cert-file-name.key替换成已上传的证书密钥文件的名称。
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    #表示使用的加密套件的类型。
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #表示使用的TLS协议的类型。
    ssl_prefer_server_ciphers on;
    location / {
        root html;  #站点目录。
        index index.html index.htm;
    }
}

```
可选配置：
可选：设置HTTP请求自动跳转HTTPS。
如果您希望所有的HTTP访问自动跳转到HTTPS页面，则可以在需要跳转的HTTP站点下添加以下rewrite语句。
使用示例代码前，请注意将yourdomain.com替换成证书绑定的域名。
```java
server {
    listen 80;
    server_name yourdomain.com; #需要将yourdomain.com替换成证书绑定的域名。
    rewrite ^(.*)$ https://$host$1; #将所有HTTP请求通过rewrite指令重定向到HTTPS。
    location / {
        index index.html index.htm;
    }
}
```
按esc退回，:wq! 保存该文件。

4、重启Nginx服务。

cd /usr/local/nginx/sbin  #进入Nginx服务的可执行目录。
./nginx -s reload  #重新载入配置文件。

5、错误处理

Nginx如果未开启SSL模块，配置Https时提示错误“nginx: [emerg] the “ssl” parameter requires ngx_http_ssl_module in /usr/local/nginx/conf/nginx.conf:37”

原因也很简单，nginx缺少http_ssl_module模块，编译安装的时候带上–with-http_ssl_module配置就行了，如果已经安装好了Nginx，则可以按照下面的步骤来配置ssl：

切换到源码包：
cd /usr/local/src/nginx-1.11.3

查看nginx原有的模块

/usr/local/nginx/sbin/nginx -V

在configure arguments:后面显示的原有的configure参数如下：

–prefix=/usr/local/nginx --with-http_stub_status_module

那么我们的新配置信息就应该这样写：

./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module

回车，等待配置完成后，运行命令

make

这里不要进行make install，否则就是覆盖安装

然后备份原有已安装好的nginx

cp /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx.bak

停止Nginx：
停止操作
停止操作是通过向nginx进程发送信号来进行的

步骤1：查询nginx主进程号

ps -ef | grep nginx
在进程列表里 面找master进程，它的编号就是主进程号了。

从容停止Nginx：
kill -QUIT 主进程号  
例如：kill -QUIT 16391

快速停止Nginx：
kill -TERM 主进程号  

强制停止Nginx：
kill -9 主进程号


然后将刚刚编译好的nginx覆盖掉原有的nginx（这个时候nginx要停止状态）

cp ./objs/nginx /usr/local/nginx/sbin/

然后启动nginx

nginx -c /usr/local/nginx/conf/nginx.conf 

仍可以通过命令查看是否已经加入成功

/usr/local/nginx/sbin/nginx -V


