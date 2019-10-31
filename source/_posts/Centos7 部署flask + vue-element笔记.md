---
title: Centos7 部署flask + vue-element笔记
date: 2019-10-31 15:59:42
tags: 
  - centos7
  - vue
  - flask
categories: 
  - flask
  - vue
---

## 目录结构：
- /data/www/oms/server 后端
- /data/www/oms/client  前端


## 部署vue前端 

### 一、安装Nodejs
```
yum -y install nodejs
```

<!--more-->

### 二、编译vue
```
cd /data/www/oms/client
npm install
npm run build
```
## 部署flask后端 
### 一、安装配置virtualenv
```
cd /data/www/oms/server
/usr/local/python3/bin/pip3 install virtualenv
/usr/local/python3/bin/virtualenv --no-site-packages venv
source venv/bin/activate    # 进入虚拟环境
pip install -r requirements.txt
pip install Gunicorn
deactivate    # 退出虚拟环境
```
### 二、安装配置supervistord
```
/usr/local/python3/bin/pip3 install supervisor
/usr/local/python3/bin/echo_supervisord_conf > /etc/supervisord.conf
```
注意修改里面的配置：
```
[include]
files = /etc/supervisord.d/*.ini
```
添加flask进程/etc/supervisord.d/oms.ini
```
[program:oms-app]
; 下面命令中的 app:app 请修改为你实际部署时的项目名称
command=/data/www/oms/server/venv/bin/gunicorn -w 64 --bind=127.0.0.1:17173 manager:app --access-logfile logs/access.log --error-logfile logs/error.log
;
; ; 下面的路径请修改为你创建的项目的根目录
directory=/data/www/oms/server

autostart=true
autorestart=true
stopsignal=QUIT
stopasgroup=true
killasgroup=true
;
; 下面的用户请修改为创建该项目的用户
user=root
;
redirect_stderr=true
;
; log 文件的路径你可以重新自定义
stdout_logfile=/data/www/oms/server/supervisor.log

; 解决编码问题
[supervisord]
environment=LC_ALL='en_US.UTF-8',LANG='en_US.UTF-8'
```
启动：
```
/usr/local/python3/bin/supervisord -c /etc/supervisord.conf
/usr/local/python3/bin/supervisorctl update
/usr/local/python3/bin/supervisorctl status
```
## 配置nginx
```
server
{
        listen       80;
        server_name  oms.lhp.com;
        root  /data/www/oms/client/dist/;
        index index.html index.htm;

        charset utf-8;

        expires  30d;

        location ~ /\.svn/     {  return 403; }
        location ~* \.(sh|xls|doc|log|sh|sql|svn|tar|gz|svn-base) {  root ~; deny all; }

        access_log  /data/nginx_logs/oms_client.access.log;
        error_log   /data/nginx_logs/oms_client.error.log;
}

server {
        listen 10002;
        server_name _;
        return 403;

}

server {
        listen      10002;
        server_name oms.lhp.com;

        access_log  /data/nginx_logs/oms_server.access.log;
        error_log   /data/nginx_logs/oms_server.error.log;

        location /docs {
                allow 183.63.86.194;
                deny all;
        }

        location / {
        # flask中已设置跨域配置，Nginx不需要重复配置
        proxy_hide_header 'Access-Control-Allow-Origin';
        proxy_pass        http://127.0.0.1:17173;
                proxy_http_version 1.1;
                proxy_set_header   Host             $host;
                proxy_set_header   X-Real-IP        $remote_addr;
                proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Host $host;
        }
}

```
reload nginx
```
systemctl reload nginx
```