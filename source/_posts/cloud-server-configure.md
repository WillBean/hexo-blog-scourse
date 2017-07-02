---
title: 腾讯云ubuntu服务器配置
date: 2016-11-07 18:59:23
tags: 
	- Configure
	- Cloud Server
	- Ubuntu
---

### 零、域名绑定
从腾讯云服务的云解析里解析域名并绑定服务器公网即可。

### 一、安装mysql

```bash
sudo apt-get install mysql-server
sudo apt-get isntall mysql-client
sudo apt-get install libmysqlclient-dev
```

第一部会提示输入密码。经过以上三部，如无意外，mysql就配置好了。

### 二、安装node

偷个懒先<sudo su>进入root模式，大家不要学我，这样不好不好。(补：一下方法是使用ubuntu自带的node包进行安装，node版本可能比较低，可跳过，使用源码安装)

```bash
apt-get update
apt-get install -y python-software-properties software-properties-common 
add-apt-repository ppa:chris-lea/node.js
apt-get update
apt-get install nodejs
```

安装过程有那么一些些慢。zzzZZZ......

然而，安装完发现node版本低到离谱，只能从官网下载新版本源码，手动安装。如果下载过于缓慢，可以先上传到git，再从git上下载。(如https://nodejs.org/dist/v6.10.0/node-v6.10.0.tar.gz)

```bash
apt-get remove nodejs 			# 先将原本的一波带走
wget https://xxxxxxxx 			# 下载node源码
tar zxvf [source] 				# 解压
apt-get install g++ make 		# 安装编译工具
cd [source]
./configure
make 							# 完成之后找到<node>应用程序的地址
make install
ln -s [源地址] /usr/bin/node	# 软链接到/usr/bin/以作用于全局
```

然后准备安装npm，先安装curl：

```bash
apt-get install curl
curl -L https://npmjs.org/install.sh | sh   
ln -s [源地址] /usr/bin/npm
```

然而问题又出现了，npm的下载速度慢得实在蛋疼，故又还得下载cnpm。

```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
ln -s [源地址] /usr/bin/cnpm
```

### 三、安装git

```bash
apt-get update
apt-get install git
```

### 四、安装nginx

```bash
apt-get install nginx 
```

一条命令足以，安装nginx主要用于静态资源的管理和反向代理，其实只有node服务器也是可以的。

安装完成后执行： <i>/etc/init.d/nginx  start</i> 即可启动服务器。

默认的web文件路径为 <em>/usr/share/nginx/html</em>，个人不大习惯，想改一改。

用 <i>ngnix -t</i> 可找到ngnix.conf配置文件的路径，然后用vim编辑。

如果没有server段，则在http{}内自己添加

```
server {   

    listen       80;        

    server_name  www.xx.com;

        #设定本虚拟主机的访问日志      

  #access_log  logs/www.xx.com.access.log  main;

  location / {       

      root   /var/www/;     

      index index.php index.html index.htm;  

  }

  location ~ \.php$ {

      fastcgi_pass 127.0.0.1:9000 ###或者使用socket，要与www.conf中同步

      fastcgi_index index.php

      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

      include fastcgi_params;

  }

}
```

或者直接更改nginx的默认文件<em>/etc/nginx/sites-available/default</em>，修改对应的server、location，<i>/etc/init.d/nginx restart</i> 重启服务器即可

### 五、配置php环境

官网下载php源码包，安装过程基本同node，略……

php源码编译之前要安装libxml2

```
apt-get install libxml2* -y 
```

对于php7，<strong>what is very important!!!</strong>

编译时使用 <i>./configure --enable-fpm</i>

php7自带fpm，无需另外下载。下载好之后开始配置。找到php-fpm.conf.default文件，一般位于<em>/usr/local/etc/</em>下

```
cp php-fpm.conf.default php-fpm.conf
vim php-fpm.conf
```

将error_log注释打开，将include引向正确地址如：<em>include /usr/local/etc/php-fpm.d/*.conf</em>

找到www.conf.default文件，一般位于<em>/usr/local/etc/php-fpm.d/</em>下

```
cp www.conf.default www.conf
vim www.conf
```

将user和group中的nobody改为<em>www-data</em>,执行<i>/usr/local/sbin/php-fpm</i>启动fpm

### 结语

原本配置过一次腾讯云的服务器了，当时弄了好几天才弄好，结果实在是懒，几个月没搭理，就被腾讯给回收了，香菇。。。为了方便以后配置，故整合了一下网络资源，写下了此博客，无奈。。。