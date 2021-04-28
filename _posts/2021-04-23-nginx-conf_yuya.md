---
title: Nginx配置
featured: images/nginx/nginx.png
layout: post
---

<p>本文列出了HTML常用标签含义及对应中英文，方便查找和记忆。</p>

```java

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    client_max_body_size 10m;
    #gzip  on;
     
     #===============官网模块 ===========================
    server {
         listen 80;
         server_name www.iyuya.com;
         access_log /data/wwwlogs/access_nginx.log combined;
         gzip on;
        gzip_buffers 32 4K;
        gzip_comp_level 6;
        gzip_min_length 100;
        gzip_types application/javascript text/css text/xml;
        gzip_disable "MSIE [1-6]\."; #配置禁用gzip条件，支持正则。此处表示ie6及以下不启用gzip（因为ie低版本不支持）
        gzip_vary on;

        location ~ .* {
            root /data/website/yuya_web/src;
            index  index.html index.htm;
            if (!-e $request_filename) {
                rewrite ^/(.*) /index.html last;
                break;
                }
        }

    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|mp4|ico)$ {
      expires 30d;
      access_log off;
    }

    location ~ .*\.(js|css)?$ {
      expires 7d;
      access_log off;
    }

     location ~ ^/(\.user.ini|\.ht|\.git|\.svn|\.project|LICENSE|README.md) {
               deny all;
      }
    }
  


    #===============管理后台模块 =========================== 
    server {
   	 listen 80;
         server_name biz.iyuya.com;
    	 access_log /data/wwwlogs/access_nginx.log combined;
    	 root /var/yuya/yuya_admin/yuya-admin/src/main/resources/static;
	 #root /var/oldyuya/yuya-biz/yuya-admin/src/main/resources/static;    	 
#error_page 404 /404.html;
    	 #error_page 502 /502.html;
    	 location /nginx_status {
      	 stub_status on;
      	 access_log off;
      	 allow 127.0.0.1;
      	 deny all;
    }
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|mp4|ico)$ {
      expires 30d;
      access_log off;
    }
    location ~ .*\.(js|css)?$ {
      expires 7d;
      access_log off;
    }

    location ~ {
      proxy_pass http://127.0.0.1:8077;
      include proxy.conf;
    }
    	location ~ ^/(\.user.ini|\.ht|\.git|\.svn|\.project|LICENSE|README.md) {
      		deny all;
  	  }
    }
    #===============老师模块 =========================== 
    server {
    	listen 80;
    	server_name teacher.iyuya.com;
    	access_log /data/wwwlogs/access_nginx.log combined;
    	root /var/yuya/yuya_teachers/target/yuya-release/yuya/webapp;
        #root /var/oldyuya/teacher/target/yuya-release/yuya/webapp;

    	#error_page 404 /404.html;
    	#error_page 502 /502.html;
    	location /nginx_status {
      		stub_status on;
      		access_log off;
      		allow 127.0.0.1;
      		deny all;
        }
    	location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|mp4|ico)$ {
      		expires 30d;
      		access_log off;
    	}
    	location ~ .*\.(js|css)?$ {
      		expires 7d;
      		access_log off;
    	}
    	location ~ {
      		proxy_pass http://127.0.0.1:8088;
      		include proxy.conf;
    	}
    		location ~ ^/(\.user.ini|\.ht|\.git|\.svn|\.project|LICENSE|README.md) {
      		deny all;
    	}
   }

   #===============家长模块 =========================== 
   server {
       listen 80;
       server_name parent.iyuya.com;
       access_log /data/wwwlogs/access_nginx.log combined;
       root /var/yuya/yuya_parent/target/yuya-release/yuya/webapp;
       #root /var/oldyuya/parent/target/yuya-release/yuya/webapp;
       #error_page 404 /404.html;
       #error_page 502 /502.html;
       location /nginx_status {
      		stub_status on;
      		access_log off;
      		allow 127.0.0.1;
      		deny all;
       }
       location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|mp4|ico)$ {
      		expires 30d;
      		access_log off;
       }
       location ~ .*\.(js|css)?$ {
      		expires 7d;
      		access_log off;
       }
       location ~ {
      		proxy_pass http://127.0.0.1:8089;
      		include proxy.conf;
       }
       location ~ ^/(\.user.ini|\.ht|\.git|\.svn|\.project|LICENSE|README.md) {
     	 	deny all;
       }
   }

   #===============消息模块 =========================== 
   server {
       listen 80;
       server_name message.iyuya.com;
       access_log /data/wwwlogs/access_nginx.log combined;
       root /var/yuya/yuya_message/target/yuya-release/yuya/webapp;
       #root /var/oldyuya/message/target/yuya-release/yuya/webapp;

       #error_page 404 /404.html;
       #error_page 502 /502.html;
       location /nginx_status {
       		stub_status on;
      		access_log off;
      		allow 127.0.0.1;
      		deny all;
       }
       location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|mp4|ico)$ {
      		expires 30d;
      		access_log off;
       }
       location ~ .*\.(js|css)?$ {
     	 	expires 7d;
      		access_log off;
       }
       location ~ {
      		proxy_pass http://127.0.0.1:8099;
      		include proxy.conf;
       }
       location ~ ^/(\.user.ini|\.ht|\.git|\.svn|\.project|LICENSE|README.md) {
      		deny all;
       }
   }


    #===============教案中心/家长课堂H5 =========================== 
    server {
        listen       80;
        server_name  html5.qilaimi.com;


      	location ~ .* {
            root /data/html5;
            index  index.html index.htm;
            if (!-e $request_filename) {
                rewrite ^/(.*) /index.html last;
                break;
                }
        }

        location ^~ /api/parent/ {
                rewrite  ^/api/(.*)$ /$1 break;
                proxy_pass   http://127.0.0.1:8089;
        }

        location ^~ /api/teacher/ {
               rewrite  ^/api/(.*)$ /$1 break;
               proxy_pass   http://127.0.0.1:8088;
        }

        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|mp4|ico)$ {
                expires 30d;
                access_log off;
        }
        location ~ .*\.(js|css)?$ {
                expires 7d;
        				access_log off;
        }
        location ~ ^/(\.user.ini|\.ht|\.git|\.svn|\.project|LICENSE|README.md) {
                deny all;
        }
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```