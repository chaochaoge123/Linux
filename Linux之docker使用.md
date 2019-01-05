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













