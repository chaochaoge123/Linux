### 1. rpm包使用
```
1. 光盘挂载
 mount /dev/cdrom /mnt
 cd  /mnt
 ls
2. 安装rpm包
rpm -ivh vsftpd-3.0.2-22.el7.x86_64.rpm
rpm -Uvh vsftpd-3.0.2-22.el7.x86_64.rpm
2.13 卸载rpm
rpm -e vsftpd-3.0.2-22.el7.x86_64

3. rpm包的查询
[root@python3 ~]# rpm -q vsftpd
vsftpd-3.0.2-22.el7.x86_64
[root@python3 ~]# rpm -q vsftp
package vsftp is not installed
[root@python3 ~]# 
[root@python3 ~]# rpm -qa |grep vsf
[root@python3 ~]# which vim
/usr/bin/vim
[root@python3 ~]# rpm -qf /usr/bin/vim
```
### 2. yum 使用
```
2.1 使用aliyun yum站点源
	Base源
	cd /etc/yum.repos.d/
	mv *.repo /tmp
	wget http://mirrors.aliyun.com/repo/Centos-7.repo
	
	EPEL源
	yum install -y epel-release
	
2.2 使用yum安装软件
	yum install -y openssl openssl-devel 

2.3 软件包查询及组安装
yum list |grep vsftpd
yum grouplist
yum groupinstall "Development Tools"

2.4 卸载软件包
yum remove 

2.5 优化yum源
(1)本地镜像yum源(光盘挂载到/mnt下)
[local]
name=localios
baseurl=file:///mnt
gpgcheck=0

yum clean all
yum install -y vsftpd

(2)局域网yum源(ftp)
1.安装ftp软件
yum install -y vsftpd
2.启动ftp服务
[root@python3 ~]# systemctl start vsftpd
[root@python3 ~]# systemctl enable  vsftpd

(3).创建站点目录,并将光盘软件拷贝其中
[root@python3 ~]# cp -a /mnt/*  /var/ftp/pub/centos7

(4).生成ftp站点的yum源配置文件
[ftp]
name=centos7
baseurl=ftp://10.0.0.100/pub/centos7
gpgcheck=0
```
### 3. 源码包应用
- 解压
```
.zip 
unzip  xxx.zip
.tar 
.tar.gz 
.tgz
.tar.bz2 
.tar.xz
tar xf  xxx.tar
.iso
mount -o loop xxx.iso  /test
.cpio
cpio -idcmv < xxx.cpio
```
- redis安装
```
(1)python3.6源码包安装
	./configure       ------> 定制功能
	 make
	 make install
	 
mysql 源码包
	cmake
	make 
	make install
  
(2) redis 源码包安装 
	make

修改环境变量
vim /etc/profile

添加以下一行:
export PATH=/opt/redis-3.2.10/src:$PATH

生效配置
source /etc/profile

启动服务端：redis: redis-server &
客户端：redis-cli

(3) 让python链接redis
unzip redis-py-master.zip 
cd redis-py-master
python3 setup.py install


对redis的单实例进行连接操作
python3
import redis
r = redis.StrictRedis(host='localhost', port=6379)
r.set('name', 'oldguo')
True
>>>r.get('name')
'oldguo'
```
- python3安装
```
1、安装依赖
yum install wget openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel sqlite-devel gcc-c++ -y
yum install libxml* -y
2、切入目录 cd /usr/local/src
3、下载：wget https://www.python.org/ftp/python/3.6.3/Python-3.6.3.tgz
4、解压：tar zxvf Python-3.6.3.tgz
5、切到解压目录： cd Python-3.6.3   进行：./configure --prefix=/usr/local/python3
6、依次执行 [root@python-linux Python-3.6.3]# make -j2
make install -j2
ln -s /usr/local/python3/bin/python3.6 /usr/bin/python3      加到环境变量
ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```
- mysql安装
```
3.1 解压及制作软连接
tar xf mysql-5.7.20-linux-glibc2.12-x86_64.tar.gz
ln -s mysql-5.7.20-linux-glibc2.12-x86_64 mysql

3.2 编辑环境变量
vim  /etc/profile
添加以下一行:
export PATH=/opt/mysql/bin:$PATH

生效配置:
source /etc/profile

3.3 卸载自带mariadb
yum remove mariadb-libs

3.4 生成配置文件(/etc/my.cnf)
vim /etc/my.cnf
[mysqld]
basedir=/opt/mysql
datadir=/opt/mysql/data
socket=/tmp/mysql.sock
user=mysql
log_error=/var/log/mysql.log
log_bin=/opt/mysql/data/mysql-bin
server_id=100
[mysql]
socket=/tmp/mysql.sock

3.5 创建用户和数据目录,并授权
useradd mysql 
mkdir  /opt/mysql/data 
chown -R mysql.mysql /opt/mysql


3.6 初始化数据
注：5.6版本初始化为5.6 初始化./mysql_install_db --basedir=/opt/mysql --datadir=/opt/mysql/data --user=mysql

[root@python3 ~]# mysqld --initialize-insecure --basedir=/opt/mysql --datadir=/opt/mysql/data --user=mysql
2019-01-04T03:58:03.128958Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2019-01-04T03:58:03.142436Z 0 [ERROR] Could not open file '/var/log/mysql.log' for error logging: Permission denied
2019-01-04T03:58:03.142492Z 0 [ERROR] Aborting

报错解决:
touch /var/log/mysql.log 
chown -R mysql.mysql /var/log/mysql.log


再次初始化:
mysqld --initialize-insecure --basedir=/opt/mysql --datadir=/opt/mysql/data --user=mysql


3.7 启动mysql 
cd /opt/mysql/support-files
./mysql.server start

cp mysql.server /etc/init.d/mysqld

/etc/init.d/mysqld restart


扩展:使用systemctl 管理mysql
vim /etc/systemd/system/mysqld.service

[Unit]
Description=MySQL Server
Documentation=man:mysqld(8)
Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target
After=syslog.target

[Install]
WantedBy=multi-user.target
[Service]
User=mysql
Group=mysql
ExecStart=/opt/mysql/bin/mysqld --defaults-file=/etc/my.cnf
LimitNOFILE = 5000


systemctl restart mysqld

netstat -tulnp |grep 3306



3.8 测试python3链接mysql
创建mysql链接用户
授权：
grant all on *.* to root@'10.0.0.%' identified by '123';

create database bbs charset utf8;

python代码测试:
pip3 install --upgrade pip
pip3 install pymysql

vim testmysql.py
#!/usr/bin/python3
import pymysql
db = pymysql.connect("10.0.0.100","root","123","bbs" )
cursor.execute("SELECT VERSION()")
data = cursor.fetchone()
print ("Database version : %s " % data)
db.close()

python3  testmysql.py 
```


















