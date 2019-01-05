### 1. nginx
```
1) 使用官方Nginx yum源
[root@nginx ~]# vim /etc/yum.repos.d/nginx.repo 

[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=0
enabled=1

#安装Nginx
[root@nginx ~]# yum install nginx -y

2) 启动Nginx，并将Nginx加入开机自启
systemctl start nginx
systemctl enable nginx
```
### 2. Django
```
依赖包
[root@web01 ~]# yum install openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel sqlite-devel gcc gcc-c++  openssl-devel zlib zlib-devel -y

1.安装python3
[root@web01 ~]# wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tgz
[root@web01 ~]# tar xf Python-3.6.2.tgz
[root@web01 ~]# cd Python-3.6.2/
[root@web01 Python-3.6.2]# ./configure --prefix=/usr/local/
[root@web01 Python-3.6.2]# make && make install
[root@web01 Python-3.6.2]# ./configure && make && make install

2.安装Django框架和uwsgi

vim  re.txt
asn1crypto==0.24.0
beautifulsoup4==4.6.3
bs4==0.0.1
certifi==2018.4.16
cffi==1.11.5
chardet==3.0.4
Click==7.0
cryptography==2.3.1
Django==1.11.9
Flask==1.0.2
Flask-Cors==3.0.6
gevent==1.3.6
greenlet==0.4.15
idna==2.7
ItsDangerous==1.1.0
Jinja2==2.10
lxml==4.2.6
MarkupSafe==1.0
numpy==1.15.3
Pillow==5.3.0
pycparser==2.18
PyMySQL==0.9.2
pytz==2018.7
requests==2.19.1
selenium==3.141.0
six==1.11.0
urllib3==1.23
virtualenv==16.1.0
Werkzeug==0.14.1
wordcloud==1.5.0

下载文件中的包：
pip3 install -i https://pypi.doubanio.com/simple/ -r re.txt
pip3 install -i https://pypi.doubanio.com/simple/ uwsgi

3.测试uwsgi是否正常，新建 test.py文件，内容如下：
[root@web01 ~]# vim test.py
def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    return [b"Hello Django"]

	
#然后在终端运行： 
切换到普通用户：su test
uwsgi --http :8001 --wsgi-file test.py &


4.测试django是否正常，运行：
[root@web01 ~]# django-admin.py startproject demosite
[root@web01 ~]# cd demosite
到vim settings.py配置ALLOWED_HOSTS = ["*"]
[root@web01 demosite]# python3 manage.py runserver 0.0.0.0:8002
在浏览器内输入：http://127.0.0.1:8002，检查django是否运行正常。（10.0.0.130：8002）
```
### 3.uwsgi与nginx配置
- uwsgi
```
在当前目录的文件下配置
vim /home/test/demosite/uwsgi.ini）
[root@web01 demosite]# vim /root/demosite/uwsgi.ini
[uwsgi]
socket = 127.0.0.1:9999
master = true
workers = 2
max-requests = 1000
buffer-size = 30000
pidfile = /run/uwsgi.pid
daemonize = /var/log/uwsgi.log

uwsgi --ini /root/demosite/uwsgi.ini &      (uwsgi --ini /home/test/demosite/uwsgi.ini &)

查看：ps -ef |grep uwsgi
```
- nginx
```
[root@web01 demosite]# vim /etc/nginx/conf.d/py.conf
server {
	listen 80;
	server_name 10.0.0.100;
	client_max_body_size 100M;

	location / {
		index index.html;
		include uwsgi_params;
		uwsgi_pass 127.0.0.1:9999;
		uwsgi_param UWSGI_SCRIPT demosite.wsgi;
		uwsgi_param UWSGI_CHDIR /root/demosite;
	}
}

重启nginx   systemctl retart nginx
```
### 4. 实际项目部署
- 数据库处理
```
1.1 上传bbs.sql 到/opt下 
1.2 在mysql中创建bbs库,并导入数据库SQL脚本
mysql> create database bbs charset utf8mb4;
mysql> use bbs
mysql> source /opt/bbs.sql
```
- 在django的settings文件中配置
```
ALLOWED_HOSTS = ['*']

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'bbs_two',   #数据库名
        'HOST': "10.0.0.100",
        'USER': 'qqc',
        'PASSWORD': '123',
        'PORT': 3306,
    }
```
- 给qqc用户授权,登陆
```
grant select,update,delete ,insert on bbs_two.* to qqc@'10.0.0.%' identified by '123';

mysql -uqqc -p123 -h10.0.0.100

注：无法登陆在/etc/my.cnf 中的【mysqld】下加入skip_name_resolve
```
- 配置Nginx
```
[root@web01 BBS]# vim /etc/nginx/conf.d/py.conf
server {
listen 80;
server_name 10.0.0.100;
client_max_body_size 100M;

location  /static {
alias /opt/BBS/static/;
}

location /media {
alias /opt/BBS/media;
}

location / {
index index.html;
include uwsgi_params;
uwsgi_pass 127.0.0.1:9090;
uwsgi_param UWSGI_SCRIPT BBS.wsgi;
uwsgi_param UWSGI_CHDIR /opt/BBS;
}
}

注：项目文件夹名为bbs
```
- 配置uwsgi
```
关闭所有已有的uwsgi进程
kill -9 `ps -ef |grep uwsgi|awk {'print $2'}`

[root@web01 BBS]# vim  uwsgi.ini
[uwsgi]
socket = 127.0.0.1:9090
master = true
workers = 2
reload-mercy = 10
vacuum = true
max-requests = 1000
limit-as = 512
buffer-size = 30000

启动uwsgi
uwsgi --ini uwsgi.ini &

重启nginx
systemctl restart nginx
```



















