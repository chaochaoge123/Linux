### 1.1 创建目录 mkdir
```
mkdir /data  创建目录
mkdir /data/use -p  递归创建目录
mkdir a{1..10} 建多个目录
```
### 1.2 切换目录 cd
```
1、cd data  切换到data
2、cd ..   返回上一级目录
3、cd /   返回根目录
4、cd -   返回上一次目录
5、cd ~   
```
### 1.3 查看目录下文件 ls
- ls -a   查看目录下所有文件包括.开头的隐藏文件
- ls -l  例出文件的详细信
```
例：
drwxr-xr-x.  2 root root    6 Nov  5  2016 home
lrwxrwxrwx.  1 root root    7 Oct 16 19:19 lib -> usr/lib
lrwxrwxrwx.  1 root root    9 Oct 16 19:19 lib64 -> usr/lib64
```
- ls -i 输出文件的 i 节点的索引信
- ls -d 将目录当做文件一样显示
- ls -p -F 
```
在每个文件名后附上一个字符以说明该文件的类型:
“*”表示可执行的普通文件；
“/”表示目录；
“@”表示符号链接；
“|”表示FIFOs；
“=”表示套接字(sockets)
```
- ls -r  对目录反向排序
- ls -t  以时间排序
- ls -u  以文件上次被访问的时间排序
- ls -S  以文件大小排序
- ls -s  在每个文件名后输出该文件的大小
### 1.4 pwd
- pwd 返回当前工作目录列表
- rmdir  删除空目录
### 1.5 文件创建命令touch
- touch a.txt  创建文件
- 修改文件时间戳
```
-a   或--time=atime或--time=access或--time=use 　只更改存取时间。
-d 　使用指定的日期时间，而非现在的时间。
-m   或--time=mtime或--time=modify 　只更改变动时间。
-t 　使用指定的日期时间，而非现在的时间。
```
### 1.5 增加内容到文件
- 方法1：使用vi命令打开vi编辑器，按i键进入编辑模式，可以输入内容，按esc键退出编辑模式，按:wq保存并退出，按：q退出，按:q!强制退出
- echo
```
> 符号前默认为1
1、重定向，文件不存在就创建,有内容就清空； echo “aaa">a   创建文件a,写入内容aaa
2、追加内容  echo "oldboyedu.com" >> /data/oldboy.txt  将oldboyedu.com追加到已存在的文件中
注：
2>> 错误追加重定向  把内容追加到文件的结尾 
2>  错误重定向     先把文件内容清空，把内容追加到文件的结尾 
```
### 1.6 文件操作 查内容、复制、移动、改名、删除
- 查看单个或者多个文件内容 cat a.txt
- cp qqc /tmp/ 复制文件qqc到tmp目录
- mv 移动文件
- rename 改名 

`rename .htm .html *.htm` 将当前目录下所有以.htm结尾的文件改成以.html
- rm 删除文件 先备份再删除。
替代方法使用mv移动到临时目录
### 1.7 查找find
- `find data/` 列出目录及子目录下的所有文件及文件夹
- `find root/data/ -name *.txt` 查找指定目录下所有包含.txt的文件
- find -type 根据文件类型查找
`d 目录; f 普通文件; s 套接字; l 符号连接; b 设备; c 字符设备;`
- 根据时间搜索
```
-atime用户最近一次访问时间
-mtime文件内容最后一次被修改时间
-ctime文件元数据（例如权限或所有权）最后一次改变的时间
-newer参数，指一个参考文件，然后找出比指定文件更新（修改时间）所有文件
-size基于文件大小的搜索 –size +2k大于2k,-size -2M 小于2M  b-块、c-字节、w-字、k-1024字节、M-1024k、G-1024M
```
- -delete删除找到的文件
- -perm基本文件权限、-user基本于所有权匹配
### 1.8 查看文件内容head、tail 、more、less
- head head取文件的前几行，如果不加参数默认取文件的前10行。常用参数-n后接数字，表示显示前n行;例`head -5`
- tail取文件的后几行，用法同head;
-f参数用于接收文件尾部的变化，常用于查看日志文件更新
- more 加载整个文佳
- less 
```
b  向后翻一页
d  向后翻半页
u  向前滚动半页
y  向前滚动一行
[pagedown]： 向下翻动一页
[pageup]：   向上翻动一页
Q  退出less 命令
```
### 1.9 文本搜索 grep 
- 搜索所指定字符的文本行
`grep "js" oldboy.txt`
- 指定多个文件查找相关的字符
`grep "js" a.txt b.txt c.txt`
- 使用正则表达式需用-E 或者egrep
```
grep -E "^g.*" a.txt
egrep "^g.*" b.txt
```
- 输出包含匹配到的行之外的所有行
`grep -v "js" a.txt`
- 输出包含匹配字符串的行号
`grep -n "js" a.txt`
### 2.0 sed 取行、文本替换
- 删除空行，用正则表达式^$配置空行，d表示删除符合模式的行
`sed "/^$/d" a.txt`
- 删除行首空格
`sed 's#^ * ##g' test.txt `
- 在第2行后面添加一行内容“hello wd!”，a表示添加，后接字符串，添加到当前行的下一行
`sed '2a hello wd!'|head -5`
- 将2-4内容替换成“I am study Linux.”c表示替换，后接字符串，用它替换n1-n2之间的行

























