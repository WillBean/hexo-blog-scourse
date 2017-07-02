---
title: Ubuntu云服务器开启telnet实现远程登录 
date: 2017-04-02 14:35:14
tags: 
	- Configure
	- Telnet
	- Ubuntu
	- Cloud Server
---

### 一、安装telnet服务
```bash
sudo apt-get install xinetd telnetd  
```

### 二、修改/etc/xinetd.conf，在# log_type = SYSLOG daemon info下面添加内容

```bash
instances = 60   
log_type = SYSLOG authpriv   
log_on_success = HOST PID   
log_on_failure = HOST   
cps = 25 30 
```

### 三、编辑/etc/xinetd.d/telnet文件（没有可自行创建此文件），添加以下内容

```bash
# default: on   
# description: The telnet server serves telnet sessions; it uses \   
# unencrypted username/password pairs for authentication.   
service telnet   
{   
disable = no   
flags = REUSE   
socket_type = stream   
wait = no   
user = root   
server = /usr/sbin/in.telnetd   
log_on_failure += USERID   
}  
```

### 四、重启机器或网络

```bash
sudo /etc/init.d/xinetd restart  
```

### 五、通过telnet命令测试是否配置成功

### 六、Windows下开启telnet服务

### 七、使用CMD连接Linux服务器