[TOC]


>当你要监视一台Linux机器时，你会有很多的选择。虽然现在有很多产品质量监控方案(比如 Nagios、 Zabbix、 Zenoss)，它们拥有华丽的UI，可扩展监控，易于理解的报告等等，这些方案对于大多数终端用户都太过于强大了。如果你只需检查Linux服务器或桌面的基本状态（比如，CPU负载、内存使用、活跃进程），《Linux就该这么学》推荐考虑一下linux-dash。

## 工具/原料

- Linux的Nginx web服务器
- linux-dash

## 在Debian、Ubuntu或者Linux Mint上设置linux-dash

1. 首先，安装带php-fpm的Nginx web服务器。

`$ sudo apt-get install git nginx php5-json php5-fpm php5-curl`

   为linux-dash创建如下Nginx配置文件/etc/nginx/conf.d/linuxdash.conf。 本例中，我们使用8080端口。
```
   $ sudo vi /etc/nginx/conf.d/linuxdash.conf

   server {

      server_name     $domain_name;

      listen          8080;

      root            /var/www;

      index           index.html index.php;

      access_log      /var/log/nginx/access.log;

      error_log       /var/log/nginx/error.log;

    

      location ~* \.(?:xml|ogg|mp3|mp4|ogv|svg|svgz|eot|otf|woff|ttf|css|js|jpg|jpeg|gif|png|ico)$ {

   ​           try_files $uri =404;

   ​           expires max;

   ​           access_log off;

   ​           add_header Pragma public;

   ​           add_header Cache-Control "public, must-revalidate, proxy-revalidate";

      }

    

      location /linux-dash {

   ​       index index.html index.php;

      }

    

      \# PHP-FPM via sockets

      location ~ \.php(/|$) {

   ​           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

   ​           fastcgi_split_path_info ^(.+?\.php)(/.*)$;

   ​           fastcgi_pass unix:/var/run/php5-fpm.sock;

   ​           if (!-f $document_root$fastcgi_script_name) {

   ​                   return 404;

   ​           }

   ​           try_files $uri $uri/ /index.php?$args;

   ​           include fastcgi_params;

      }

   }
```
2. 禁止默认站点配置。

   $ sudo rm /etc/nginx/sites-enabled/default

   编辑/etc/php5/fpm/pool.d/www.conf来配置php-fpm。确保按如下编辑"user", "group" 和 "listen"指令。你可以不改变剩下的配置。
```
   $ sudo vi /etc/php5/fpm/pool.d/www.conf

    

   user = www-data

   group = www-data

   listen = /var/run/php5-fpm.sock
```
3. 开始下载并安装linux-dash。
```
   $ git clone https://github.com/afaqurk/linux-dash.git

   $ sudo cp -r linux-dash/ /var/www/

   $ sudo chown -R www-data:www-data /var/www

   重启Nginx与php-fpm来完成安装。

   $ sudo service php5-fpm restart

   $ sudo service nginx restart
```
   END

---------------
## 在CentOS、Fedora、RHEL上设置linux-dash

1. 安装Nginx与php-fpm组件。

`$ sudo yum install git nginx php-common php-fpm`

   为linux-dash程序配置Nginx，如下创建/etc/nginx/conf.d/linuxdash.conf
```
   $ sudo vi /etc/nginx/conf.d/linuxdash.conf

   server {

      server_name     $domain_name;

      listen          8080;

      root            /var/www;

      index           index.html index.php;

      access_log      /var/log/nginx/access.log;

      error_log       /var/log/nginx/error.log;

    

      location ~* \.(?:xml|ogg|mp3|mp4|ogv|svg|svgz|eot|otf|woff|ttf|css|js|jpg|jpeg|gif|png|ico)$ {

   ​           try_files $uri =404;

   ​           expires max;

   ​           access_log off;

   ​           add_header Pragma public;

   ​           add_header Cache-Control "public, must-revalidate, proxy-revalidate";

      }

    

      location /linux-dash {

   ​       index index.html index.php;

      }

    

      \# PHP-FPM via sockets

      location ~ \.php(/|$) {

   ​           fastcgi_param  SCRIPT_FILENAME   $document_root$fastcgi_script_name;

   ​           fastcgi_split_path_info ^(.+?\.php)(/.*)$;

   ​           fastcgi_pass unix:/var/run/php-fpm.sock;

   ​           if (!-f $document_root$fastcgi_script_name) {

   ​                   return 404;

   ​           }

   ​           try_files $uri $uri/ /index.php?$args;

   ​           include fastcgi_params;

      }

   }
```
2. 接下来，编辑/etc/php-fpm.d/www.conf来配置php-fpm。在这个文件中，确保按如下编辑"user", "group" 和 "listen"字段。你可以不改变剩下的配置。
```
   $ sudo vi /etc/php-fpm.d/www.conf

   listen = /var/run/php-fpm.sock

   user = nginx

   group = nginx

   在/var/wwwx下载并安装linux-dash。

   $ git clone https://github.com/afaqurk/linux-dash.git

   $ sudo cp -r linux-dash/ /var/www/

   $ sudo chown -R nginx:nginx /var/www
```
3. 最后，重启Nginx和php-fpm，并设置开机自启。
```
   $ sudo service php-fpm restart

   $ sudo service nginx restart

   $ sudo chkconfig nginx on

   $ sudo chkconfig php-fpm on
```
   在本例中，我们已经配置linux-dash使用TCP端口8080。所以确保防火墙没有阻止TCP 8080端口。

-------------------

## 用linux-dash监视Linux机器

1. 要在web浏览器上访问linux-dash，只需在web浏览器中输入http://<linux ip地址>:8080/linux-dash/

   下面是linux-dash的截图。web面板包含了几个小部件，每个都会显示特性的系统参数。你可以通过重安排并且/或者关闭一些小部件来自定义web面板的外观。这里是一个linux-dash创建者的演示站点。

   [![如何从Web浏览器远程监视Linux服务器和桌面](https://imgsa.baidu.com/exp/w=500/sign=fe05f4ed8a0a19d8cb03840503fb82c9/a5c27d1ed21b0ef4296f3502d7c451da80cb3ee2.jpg)](http://jingyan.baidu.com/album/9113f81b60f0c62b3214c7e7.html?picindex=1)

   [![如何从Web浏览器远程监视Linux服务器和桌面](https://imgsa.baidu.com/exp/w=500/sign=b4b081b4b63eb13544c7b7bb961fa8cb/d31b0ef41bd5ad6ee97ec9b68bcb39dbb7fd3ce2.jpg)](http://jingyan.baidu.com/album/9113f81b60f0c62b3214c7e7.html?picindex=2)

   [![如何从Web浏览器远程监视Linux服务器和桌面](https://imgsa.baidu.com/exp/w=500/sign=0b401ae7ac1ea8d38a227404a70b30cf/dcc451da81cb39db8e47ca0ada160924aa1830e2.jpg)](http://jingyan.baidu.com/album/9113f81b60f0c62b3214c7e7.html?picindex=3)

   END

--------------
## 注意事项

- 在CentOS上，需要首先启用EPEL仓库