摘要：

- image 镜像
- container容器（手动启动）
- registry 仓库

### 1. 安装

```
1. curl  http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo

2. yum install -y yum-utils device-mapper-persistent-data lvm2

3.yum list docker-ce.x86_64 --showduplicates | sort -r

4.yum install -y --setopt=obsoletes=0 \
docker-ce-17.03.2.ce-1.el7.centos.x86_64 \
docker-ce-selinux-17.03.2.ce-1.el7.centos.noarch

启动：
systemctl daemon-reload
systemctl restart docker

curl  http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

mkdir -p /etc/docker

vim   /etc/docker/daemon.json

	{
		 "registry-mirrors": ["https://68rmyzg7.mirror.aliyuncs.com"]
	}

systemctl restart docker

pull常用镜像
docker pull  centos:6.9
docker pull  centos:7.5.1804
docker pull  nginx

运行镜像环境：
docker run -it  centos:6.9

docker search mongo  查看镜像版本
exit 退出

```



### 2. 镜像常用操作

```
1.1 获取镜像
	docker search centos
	docker pull centos:6.9
	docker pull centos:7.5.1804
	docker pull nginx
	
1.2 查询镜像
	docker images
	docker images -q 
	docker inspect ID/name:tag
1.3 删除镜像
	docker rmi  ID 
	docker rmi `docker images -q`
	docker rmi $(docker images -q)
	
1.4 导入导出镜像
	[root@docker ~]# docker image save nginx（或者是id） >/opt/nginx.tar.gz    导出
	[root@docker ~]# docker image load -i /opt/nginx.tar.gz        导入
	
	1.5 启动容器并获取镜像
	[root@docker ~]# docker  run -d -p 80:80 httpd
	[root@docker ~]# docker ps -a
    [root@docker ~]# docker images
```

### 3. 自定义镜像制作（centos7.5+vim+net-tools+iproute+sshd）

```
1.启动新容器
docker run -it --name "centos7.5" 76d6bc25b8a5

2.优化yum源
mv /etc/yum.repos.d/*.repo /tmp
echo -e "[ftp]\nname=ftp\nbaseurl=ftp://10.0.0.130/pub/centos7\ngpgcheck=0">/etc/yum.repos.d/ftp.repo

3. 安装必须软件包

yum install -y vim net-tools  iproute   openssh-*   -y


4.启动SSHD

 mkdir /var/run/sshd
 echo 'UseDNS no' >> /etc/ssh/sshd_config
 sed -i -e '/pam_loginuid.so/d' /etc/pam.d/sshd
 echo 'root:123456' | chpasswd
 /usr/bin/ssh-keygen -A
 /usr/sbin/sshd -D 
 
 
 注意: 以上操作做完之后,会一直不退出,需要用以下命令退回到宿主机,并不关闭容器
 ctrl p  q

5.制作镜像
docker commit centos7.5 oldguo/centos7_sshd:v2

----------------------------------------------
第二中方法：
vim dockerfile  运行配置文件
在基础镜像上装一些应用
```

### 4. 容器管理

```
类型：
工具类:vim 
docker run -it --name="test_vim"  3fe2fe0dab2e /bin/bash
服务类:nginx 
docker run -d -p 8080:80 --name="discuz" nginx:1.14

启动：
(1)交互式启动
[root@docker ~]# docker run -it --name "testcentos" centos:6.9 /bin/bash
 主要是针对于工具类的容器,一旦exit容器,容器就自动关闭
(2)守护式启动
1.交互式启动容器+Ctrl+p+q
[root@docker ~]# docker run  -it --name "testnginx" nginx /bin/bash
  加ctrl+p+q
[root@docker ~]# docker attach testnginx

查看：
docker ps -a  查看所有容器情况
docker ps -qa  显示正在运行的

删除：
docker rm 容器ID|容器名称  
docker rm -v $(docker ps -aq -f status=exited)   批量删除已关闭
docker rm -f `docker ps -a –q` 批量强制删除所有

docker top nginx
docker inspect nginx
docker attach 容器ID|容器名称（工具类）配合ctrl+p+q
docker exec  -i -t  容器ID|容器名称 /bin/bash(服务类）,一般是做服务类容器调试用
[root@docker ~]# docker exec -it  centos6.9   /bin/bash


docker stop  停止一个容器
docker kill  

开启容器：
docker  start -i
docker  restart 容器ID|容器名称
```

### 5. 私有仓库

```
制作：
docker run -d -p 5000:5000 --restart=always --name registry -v /opt/Registry:/var/lib/registry  registry
#在cd /opt  中查看

vim /etc/docker/daemon.json

{
   "registry-mirrors": ["https://68rmyzg7.mirror.aliyuncs.com"],
   "insecure-registries": ["10.0.0.130:5000"]  
}

注：["10.0.0.130:5000"] 公司镜像服务器地址
systemctl  restart docker


上传镜像：
1. 制作本地镜像并push到
[root@docker ~]# docker tag nginx 10.0.0.100:5000/oldguo/nginx:v1
[root@docker ~]# docker images
[root@docker ~]# docker push 10.0.0.100:5000/oldguo/nginx:v1

2. 异地进行pull镜像
[root@docker ~]# docker pull  10.0.0.100:5000/oldguo/nginx:v1	
```









