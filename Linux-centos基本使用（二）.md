### 1. 基本配置
```
1.常用软件安装
yum install -y bash-completion vim lrzsz wget expect net-tools nc nmap tree dos2unix htop iftop iotop unzip telnet sl psmisc nethogs glances bc

2.主机名和网络解析
hostnamectl set-hostname python3
echo "10.0.0.100 python3">>/etc/hosts或者vim /etc/hosts

3.防火墙设置
关闭：systemctl disable firewalld
看防火墙状态：getenforce 
到vim /etc/selinux/config 改成SELINUX=disabled
```
### 2. 常用操作
- ls -l /var  (命令体 选项 参数)
```
d :目录
f :普通文件
l :链接
b :块设备,磁盘 光驱
c :字符设备
p :管道
s :套接字
```
- 命令提示符
```
[root@qqc ~]#   
[root@qqc ~]$
```
- 常用快捷键
```
trl + c  cancel 取消当前的操作 
ctrl + l  (小写字母L)  clear（命令）
ctrl + d  退出当前用户 
ctrl + r查找（历史命令）。 history|grep
[TAB]:
	1.命令补全
	2.参数补全
ctrl + a  把光标移动到行首 
ctrl + e  把光标移动到行尾
ctrl+ u  把光标到行首的内容删除/剪切 
ctrl + y  粘贴 
delete    光标所在处从前往后删除内容
ctrl + k  把光标到行尾的内容删除/剪切 
ctrl + →  向右移动一个单词 
ctrl + ←  向左移动一个单词
ctrl + s  锁屏 
ctrl + q  解锁
```
- 获取帮助
```
--help
man
info
```
- 重启
```
reboot 
poweroff
```
### 3. 文件管理
- 路径
```
绝对路径:从"/"开始一个具体路径
相对路径:从当前目录开始的具体路径(pwd可以查看当前所在目录)
```
- 目录
```
1.1.1 创建目录:
mkdir /oldboy 
mkdir -p /a/b/c

1.1.2 查询目录
ls -ld /oldboy 
tree /a 
tree -L 2 /

1.1.3 删除目录(危险)
rm -rf /oldboy  
通过文件句柄,有可能可以找回丢失数据(前提:不能重启,不能有大量数据写入)
1.1.4 修改目录(剪切,复制)
mv /root/oldboy/ /tmp
mv /root/oldguo/ /tmp/oldguo.bak
mv oldboy old

cp -r /tmp/old /
cp -a 

1.1.5 切换目录
cd /
cd /oldboy
cd  oldboy 
cd .. 
cd -
cd 
```
- 文件管理
```
1.2.1 文件创建
touch a.txt 

1.2.2 文件删除
rm -rf a.txt

1.2.3 文件查看
ls -l a.txt    看文件属性

(1)小文件内容查看
cat /etc/passwd
cat /etc/passwd /etc/shadow
cat -n /etc/passwd 

(2)分页显示大文件内容
more /var/log/secure
less /var/log/secure 

(3)文件前多少行
head /etc/passwd
 head -n3 /etc/passwd
 head -3 /etc/passwd
(4)文件后多少行
tail -2 /etc/passwd
tail -f /var/log/secure

1.2.4 剪切 复制
mv 命令和目录的操作一样
cp 命令和目录的区别不需要加-r (加上也不报错)

1.2.5 内容修改
重定向 
>   覆盖重定向
cat /etc/passwd >a.txt
>>  追加重定向
cat /etc/passwd >>a.txt
echo "10.0.0.100 python3" >>/etc/hosts
```
- vim 编辑器使用
```
1.查看文件内容
	上下左右光标移动
	page up  page down翻页
	G 光标到达最后一行
	1G 光标到达第一行(gg)
	10G 第10行
	^ 光标到达行首
	$ 光标到达行尾
    / 搜索关键字
	yy 复制光标所在行
	Nyy 复制N行
	dd  删除/剪切
	Ndd 删除/剪切N行
	p  粘贴
	x(del) 删除光标所在字符
    dG  删除光标到文本末尾
	d$  从光标删除到行尾,包括贯标所在字符
	d^  从光标删除到行首,不包括光标所在字符
    r   替换光标所在字符
	ZZ  保存退出
	u   撤销上次操作

2. 编辑模式:
a :在光标之后录入数据
i :在光标之前录入数据
o :在光标下一行开启新行录入数据
A
I
O
ESC 
小技巧:每编辑完一行就ESC,退回到命令模式

3.末行模式
:q!
:wq!
:set nu  
:set nonu
:%s/root/ROOT/g 
```
- 创建链接
`ln -s passwd passwd.lnk`

- 程序管理
```
ps -ef|grep mysql 
kill 1234
kill -9 1234
pkill mysqld
```
- 网络管理
```
ip a 
ifconfig 
ping 
vim /etc/sysconfig/network-scripts/ifcfg-eth0 
systemctl restart network
```












