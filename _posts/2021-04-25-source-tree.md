---
title: Sourcetree下载gitee出现拒绝访问的情况
featured: images/nginx/nginx.png
layout: post
---

<p>基于阿里云CES服务器和Nginx-1.75.5配置SSL证书的步骤，详情请参见阿里云官方文档：https://help.aliyun.com/document_detail/98728.html</p>


## sourcetree工具clone码云代码服务器的时候，出现403错误：
1、在user/appdata/local/sourcetree/
修改localhost文件，修改用户名
删除password文件，清除原理的密码

