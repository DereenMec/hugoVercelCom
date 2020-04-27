---
title: "LinuxNotes.Vim 编辑器和Shell 命令脚本"
date: 2019-09-04T12:46:25+08:00
lastmod: 2019-09-04T12:46:25+08:00
draft: false
tags: [Linux]
categories: [Linux 学习笔记]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

## Vim 文本编辑器

Vim 之所以能得到广大厂商与用户的认可，原因在于 Vim 编辑器中设置了三种模式—命令模式、末行模式和编辑模式，每种模式分别又支持多种不同的命令快捷键，这大大提高了工作效率，而且用户在习惯之后也会觉得相当顺手。要想高效率地操作文本，就必须先搞清这三种模式的操作区别以及模式之间的切换方法（见图 4-1）。

- 命令模式：控制光标移动，可对文本进行复制、粘贴、删除和查找等工作。
- 输入模式：正常的文本录入。
- 末行模式：保存或退出文档，以及设置编辑环境。

![20966919-a161-4875-be0f-b263d6aa9814.png](https://storage.live.com/items/55AB66419C029DA!4100?authkey=AFBtWHxjrNHYZLw)

### Vim 中常用的命令（命令模式）

| 命令 | 作用                                                 |
| ---- | ---------------------------------------------------- |
| dd   | 删除（剪切）光标所在整行                             |
| 5dd  | 删除（剪切）从光标处开始的 5 行                      |
| yy   | 复制光标所在整行                                     |
| 5yy  | 复制从光标处开始的 5 行                              |
| n    | 显示搜索命令定位到的下一个字符串                     |
| N    | 显示搜索命令定位到的上一个字符串                     |
| u    | 撤销上一步的操作                                     |
| p    | 将之前删除（ dd）或复制（ yy）过的数据粘贴到光标后面 |

### Vim 中常用的命令（末行模式）

| 命令          | 作用                                    |
| ------------- | --------------------------------------- |
| :w            | 保存                                    |
| :q            | 退出                                    |
| :q!           | 强制退出（放弃对文档的修改内容）        |
| :wq!          | 强制保存退出                            |
| :set nu       | 显示行号                                |
| :set nonu     | 不显示行号                              |
| :命令         | 执行该命令                              |
| :整数         | 跳转到该行                              |
| :s/one/two    | 将当前光标所在行的第一个 one 替换成 two |
| :s/one/two/g  | 将当前光标所在行的所有 one 替换成 two   |
| :%s/one/two/g | 将全文中的所有 one 替换成 two           |
| ?字符串       | 在文本中从下至上搜索该字符串            |
| /字符串       | 在文本中从上至下搜索该字符串            |

### 配置主机名称

为了便于在局域网中查找某台特定的主机，或者对主机进行区分，除了要有IP地址外，还要为主机配置一个主机名，主机之间可以通过这个类似于域名的名称来相互访问。在Linux 系统中，主机名大多保存在/etc/hostname 文件中。

```bash
[linuxprobe@linuxprobe Desktop]$ cat /etc/hostname 
linuxprobe.com
[linuxprobe@linuxprobe Desktop]$ hostname
linuxprobe.com
```

### 配置网卡信息

> 在 RHEL 5、 RHEL 6 中，网卡配置文件的前缀为 eth，第 1 块网卡为eth0，第 2 块网卡为 eth1；以此类推。而在 RHEL 7 中，网卡配置文件的前缀则以 ifcfg 开始，加上网卡名称共同组成了网卡配置文件的名字，例如 ifcfg-eno16777736；好在除了文件名变化外也没有其他大的区别。

现在有一个名称为 ifcfg-eno16777736 的网卡设备，我们将其配置为开机自启动，并且 IP地址、子网、网关等信息由人工指定，其步骤应该如下所示。

1. 首先切换到/etc/sysconfig/network-scripts 目录中（存放网卡配置文件）
2. 使用Vim 编辑器修改网卡文件
3. 重启网络服务`systemctl restart network`

### 配置Yum 软件仓库

搭建并配置Yum 软件仓库的大致步骤如下：

1. 进入到`/etc/yum.repos.d/`目录中（该目录存放着Yum 软件仓库的配置文件）

2. 使用Vim 编辑器创建一个仓库的配置文件

   [rhel-media]：Yum 仓库唯一标识符，避免与其他仓库冲突

   name=linuxprobe：Yum 软件仓库的名称描述，易于识别仓库用处

   baseurl=file://media/cdrom：提供的方式包括FTP、HTTP、本地

   enable=1：设置此源是否可用；1为可用，0为禁用

   gpgcheck=1：设置此源是否校验文件；1为校验，0为不校验

   gpgkey=file:///media/cdrom/RPM-GPG-KEY-redhat-release：若上面参数开启校验，那么请指定公钥文件地址。

3. 按配置参数的路径挂在光盘，并把光盘挂载信息写入到/etc/fstab 文件中

4. 使用“yum install httpd -y” 命令检查Yum 软件仓库是否已经可用

下面有一个从CDROM 作为软件仓库的例子：

```bash
[root@linuxprobe ~]## cd /etc/yum.repos.d/
[root@linuxprobe yum.repos.d]## vim rhel7.repo
[rhel7]
name=rhel7
baseurl=file:///media/cdrom
enabled=1
gpgcheck=0
[root@linuxprobe yum.repos.d]## mkdir -p /media/cdrom
[root@linuxprobe yum.repos.d]## mount /dev/cdrom /media/cdrom
mount: /dev/sr0 is write-protected, mounting read-only
[root@linuxprobe yum.repos.d]## vim /etc/fstab
/dev/cdrom /media/cdrom iso9660 defaults 0 0
[root@linuxprobe ~]## yum install httpd
Loaded plugins: langpacks, product-id, subscription-manager
………………省略部分输出信息………………
Dependencies Resolved
===============================================================================
Package Arch Version Repository Size
===============================================================================
Installing:
httpd x86_64 2.4.6-17.el7 rhel 1.2 M
Installing for dependencies:
apr x86_64 1.4.8-3.el7 rhel 103 k
apr-util x86_64 1.5.2-6.el7 rhel 92 k
httpd-tools x86_64 2.4.6-17.el7 rhel 77 k
mailcap noarch 2.1.41-2.el7 rhel 31 k
Transaction Summary
===============================================================================
Install 1 Package (+4 Dependent packages)
Total download size: 1.5 M
Installed size: 4.3 M
Is this ok [y/d/N]: y
Downloading packages:
-------------------------------------------------------------------------------
………………省略部分输出信息………………
Complete!
```

## 编写Shell 脚本

> Shell 是一种程序设计语言。作为命令语言，它交互式解释和执行用户输入的命令或者自动地解释和执行预先设定好的一连串的命令；作为程序设计语言，它定义了各种变量和参数，并提供了许多在高级语言中才具有的控制结构，包括循环和分支。

Shell 脚本命令的工作方式有两种：交互式和批处理

### 编写简单脚本

```bash
[root@linuxprobe ~]## vim example.sh
pwd
ls -al
[root@linuxprobe ~]## bash example.sh 
/root
total 44
dr-xr-x---.  6 root root 4096 Mar 28 12:26 .
drwxr-xr-x. 17 root root 4096 Mar 28 09:52 ..
-rw-------.  1 root root 1115 Mar 28 09:51 .bash_history
-rw-r--r--.  1 root root   18 Dec 29  2013 .bash_logout
-rw-r--r--.  1 root root  176 Dec 29  2013 .bash_profile
-rw-r--r--.  1 root root  176 Dec 29  2013 .bashrc
drwx------.  4 root root   29 Mar 26 20:05 .cache
drwx------.  6 root root   52 Mar 26 20:05 .config
-rw-r--r--.  1 root root  100 Dec 29  2013 .cshrc
drwx------.  3 root root   24 Mar 26 09:36 .dbus
-rw-r--r--.  1 root root   11 Mar 28 12:26 example.sh
drwxr-xr-x.  3 root root   18 Mar 26 16:13 .local
-rw-r--r--.  1 root root  129 Dec 29  2013 .tcshrc
-rw-------.  1 root root 1693 Mar 28 12:26 .viminfo
-rw-------.  1 root root   59 Mar 28 09:54 .xauthqzqxkc
```

执行脚本的另一种方法是输入完整路径来执行，**但有可能会因为权限不足而报错，这时只需要修改脚本文件权限即可**

```bash
[root@linuxprobe ~]## ./example.sh
-bash: ./example.sh: Permission denied
[root@linuxprobe ~]## chmod u+x example.sh 
[root@linuxprobe ~]## ./example.sh 
/root
total 44
dr-xr-x---.  6 root root 4096 Mar 28 12:26 .
drwxr-xr-x. 17 root root 4096 Mar 28 09:52 ..
-rw-------.  1 root root 1115 Mar 28 09:51 .bash_history
-rw-r--r--.  1 root root   18 Dec 29  2013 .bash_logout
-rw-r--r--.  1 root root  176 Dec 29  2013 .bash_profile
-rw-r--r--.  1 root root  176 Dec 29  2013 .bashrc
drwx------.  4 root root   29 Mar 26 20:05 .cache
drwx------.  6 root root   52 Mar 26 20:05 .config
-rw-r--r--.  1 root root  100 Dec 29  2013 .cshrc
drwx------.  3 root root   24 Mar 26 09:36 .dbus
-rwxr--r--.  1 root root   11 Mar 28 12:26 example.sh
drwxr-xr-x.  3 root root   18 Mar 26 16:13 .local
-rw-r--r--.  1 root root  129 Dec 29  2013 .tcshrc
-rw-------.  1 root root 1693 Mar 28 12:26 .viminfo
-rw-------.  1 root root   59 Mar 28 09:54 .xauthqzqxkc
```

### 接收用户的参数

除了执行预定义的命令，shell脚本还可以接收用户输入的参数。Linux 系统中的 Shell 脚本语言早就考虑到了这些，已经内设了用于接收参数的变量，变量之间可以使用空格间隔。例如$0 对应的是当前 Shell 脚本程序的名称， $#对应的是总共有几个参数， $*对应的是所有位置的参数值， $?对应的是显示上一次命令的执行返回值，而$1、 $2、 $3……则分别对应着第 N 个位置的参数值，如图所示。

![115e8534-082a-483d-be61-33d059b4a78a.png](https://storage.live.com/items/55AB66419C029DA!4101?authkey=AFBtWHxjrNHYZLw)

```bash
[root@linuxprobe ~]## vim example.sh
#!/bin/bash
echo "当前脚本名称为$0"
echo "总共有$#个参数，分别是$*。 "
echo "第 1 个参数为$1，第 5 个为$5。 "
[root@linuxprobe ~]## sh example.sh one two three four five six
当前脚本名称为 example.sh
总共有 6 个参数，分别是 one two three four five six。
第 1 个参数为 one，第 5 个为 five。
```

### 判断用户的参数

系统在执行mkdir 命令时会判断用户输入的信息，即判断用户指定的文件夹名称是否已经存在，如果存在则提示报错；反之则自动创建。Shell 脚本中的条件测试语法可以判断表达式是否成立，**若条件成立则返回数字0，否则便返回其他随机数值。条件测试语法的执行格式：[ 条件表达式 ]，其中中括号旁边均有一个空格。**

按照测试对象，条件测试语句可以分为4种：

- 文件测试语句；
- 逻辑测试语句
- 整数值比较语句
- 字符串比较语句

#### 文件测试所用的参数

| 运算符 | 作用                       |
| ------ | -------------------------- |
| #NAME? | 测试文件是否为目录类型     |
| -e     | 测试文件是否存在           |
| -f     | 判断是否为一般文件         |
| -r     | 测试当前用户是否有权限读取 |
| -w     | 测试当前用户是否有权限写入 |
| -x     | 测试当前用户是否有权限执行 |

#### 文件测试语句

下面的例子使用文件测试语句来判断/etc/fstab 是否为一个目录类型的文件，然后通过shell 解释器的内设$? 变量显示上一条命令执行后的返回值。如果返回值为0，则存在，反之不存在。

```bash
[root@linuxprobe ~]## [ -d /etc/fstab ]
[root@linuxprobe ~]## echo $?
1
```

再用文件测试语句来判断/etc/fstab 是否为一般文件，如果返回值为0，则代表文件存在，且为一般文件

```bash
[root@linuxprobe ~]## [ -f /etc/fstab ]
[root@linuxprobe ~]## echo $?
0
```

#### 逻辑测试语句

逻辑语句用于对测试结果进行逻辑分析，根据测试结果可实现不同的效果。例如在Shell 终端中逻辑“与”的运算符号&&，它表示当前面的命令执行成功后才会执行它后面的命令；逻辑“或”的运算符号为||，表示当前面的命令执行失败后才会执行它后面的命令；逻辑“非”，在Linux 系统中给的运算符号是一个惊叹号！，它表示把条件测试中的结果取相反值。

```bash
[root@linuxprobe ~]## [ $USER=root ] && echo "user=root"
user=root
[root@linuxprobe ~]## [ $USER=root ] || echo "user=root"
[root@linuxprobe ~]## [ ! $USER=root ] || echo "user=root"
user=root
```

#### 整数比较运算符

💡整数比较运算符仅是对数字的操作，不能将数字与字符串、文件等内容一起操作，而且**不能想当然的使用日常生活中的等号、大于号和小于号等来判断。**这是因为等号与复制命令符冲突，大于小于号与输入重定向符和输出重定向符冲突。

#### 可用的整数比较运算符

| 运算符 | 作用           |
| ------ | -------------- |
| -eq    | 是否等于       |
| -ne    | 是否不等于     |
| -gt    | 是否大于       |
| -lt    | 是否小于       |
| -le    | 是否等于或小于 |
| -ge    | 是否大于或等于 |

```bash
[root@linuxprobe ~]## [ 10 -gt 9 ] && echo "10>9"
10>9
```

下面这个例子，用于判断当前剩余内存是否小于1024MB，如果小于则提示“Insufficient Memory”。

```bash
[root@linuxprobe ~]## free -m
             total       used       free     shared    buffers     cached
Mem:          1826       1140        686          9          1        442
-/+ buffers/cache:        695       1131
Swap:         2063          0       2063
[root@linuxprobe ~]## free -m | grep Mem:
Mem:          1826       1140        686          9          1        442
[root@linuxprobe ~]## free -m | grep Mem: | awk '{print $4}'
686
[root@linuxprobe ~]## FreeMem=`free -m | grep Mem: | awk '{print $4}'`
[root@linuxprobe ~]## echo $FreeMem 
685
[root@linuxprobe ~]## [ $FreeMem -lt 1024 ] && echo "Insufficient Memory"
Insufficient Memory
```

#### 字符串比较语句

#### 常见的字符串比较运算符

| 运算符 | 作用                   |
| ------ | ---------------------- |
| =      | 比较字符串内容是否相同 |
| !=     | 比较字符串内容是否不同 |
| -z     | 判断字符串内容是否为空 |

下面这个例子用于判断当前的语系环境变量值是不是（[en.US](http://en.US)）

```bash
[root@linuxprobe ~]## [ -z $LANG ] && echo "Empty String"
[root@linuxprobe ~]## [ ! $LANG = "en.US" ] && echo "Not en.US"
Not en.US
[root@linuxprobe ~]## echo $LANG
en_US.utf8
```

## 流程控制语句

### if 条件测试语句

![e034faa2-92cb-4146-aea6-08650aea89b7.png](https://storage.live.com/items/55AB66419C029DA!4102?authkey=AFBtWHxjrNHYZLw)

下面这个例子先判断目录“/media/cdrom” 是否存在，如果不存在就创建一个，否则提示“Directory already exists.”

```bash
[root@linuxprobe ~]## vim mkcdrom.sh 
#!/bin/bash
DIR="/media/cdrom"
if [ ! -e $DIR ]
then
mkdir DIR
else
echo "Directory already exists."
fi
[root@linuxprobe ~]## bash mkcdrom.sh 
Directory already exists.
```

下面的语句用来验证某台主机是否在线，然后根据返回值的结果，要么显示主机在线信息，要么显示主机不在线信息。这里的脚本使用ping 命令来测试与主机的连通性。其中-c 参数规定尝试的次数，-i参数定义每个数据包的发送间隔，以及使用-W 参数定义等待超时时间。

```bash
[root@linuxprobe ~]## vim chkhost.sh
#!/bin/bash
ping -c 3 -i 0.2 -W 3 $1 &> /dev/null
if [ $? -eq 0 ]
then
echo "Host $1 is On-line."
else
echo "Host $1 is Off-line."
fi
[root@linuxprobe ~]## bash chkhost.sh 8.8.8.8
Host 8.8.8.8 is On-line.
[root@linuxprobe ~]## bash chkhost.sh 1.1.1.1
Host 1.1.1.1 is On-line.
[root@linuxprobe ~]## bash chkhost.sh 2.2.2.2
```

[/dev/null](https://zh.wikipedia.org/wiki//dev/null) 是一个被称作 Linux 黑洞的文件，把输出信息重定向到这个文件等同于删除数据（类似于没有回收功能的垃圾箱），可以让用户的屏幕窗口保持简洁。

多分支条件语句通过下面这个例子了解，读取用户输入的分数，然后判断在哪个区间内，然后输出如Excellent、Pass、Fail 等提示信息。其中read 语句可以让用户从屏幕输入。

```bash
[root@linuxprobe ~]## vim clsscore.sh 
#!/bin/bash
read -p "Enter your score (0-100): " GRADE
if [ $GRADE -gt 100 ] || [ $GRADE -lt 0 ] ; then
echo "That's impossible!"
elif [ $GRADE -ge 80 ] ; then
echo "You are excellent!"
elif [ $GRADE -ge 60 ] ; then
echo "You are pass."
else
echo "You are fail..."
fi
[root@linuxprobe ~]## bash clsscore.sh 
Enter your score (0-100): 111
That's impossible!
[root@linuxprobe ~]## bash clsscore.sh 
Enter your score (0-100): -1
That's impossible!
[root@linuxprobe ~]## bash clsscore.sh 
Enter your score (0-100): 80
You are excellent!
```

### for 条件循环语句

![b89cd27b-42d1-4fa6-9f0d-46907c7759a6.png](https://storage.live.com/items/55AB66419C029DA!4103?authkey=AFBtWHxjrNHYZLw)

下面的例子，从user.txt 文件中读取多个用户名，然后逐一创建用户账号并设置密码。首先创建用户名称的列表文件users.txt，每个用户名称单独一行。

```bash
[root@linuxprobe ~]## vim users.txt
zhangsan
lisi
wangwu
zhaoliu
wuqi
zhuba
```

接下来编写Shell [脚本Example.sh](http://xn--Example-1w5o308p.sh) 。在脚本中使用read 命令读取用户输入的密码值，然后赋值给PASSWD 变量，并通过-p 参数向用户显示一段信息，告诉用户正在输入的内容作为账户密码。在执行该脚本后，会自动使用从列表文件 users.txt 中获取到所有的用户名称，然后逐一使用“id 用户名”命令查看用户的信息，并使用$?判断这条命令是否执行成功，也就是判断该用户是否已经存在。

```bash
[root@linuxprobe ~]## vim users.txt
zhangsan
lisi
wangwu
zhaoliu
wuqi
zhuba
[root@linuxprobe ~]## vim Example.sh
#!/bin/bash
read -p "Enter The Users Password : " PASSWD
for UNAME in `cat users.txt`
do
id $UNAME &> /dev/null
if [ $? -eq 0 ]
then
echo "Already exists"
else
useradd $UNAME &> /dev/null
echo "$PASSWD" | passwd --stdin $UNAME &> /dev/null
if [ $? -eq 0 ]
then
echo "$UNAME , Create success"
else
echo "$UNAME , Create failure"
fi
fi
done
[root@linuxprobe ~]## bash example.sh 
Enter The Users PASSWORD: 1
zhangsan , Create success
lisi , Create success
wangwu , Create success
zhaoliu , Create success
wuqi , Create success
zhuba , Create success
[root@linuxprobe ~]## tail -6 /etc/passwd 
zhangsan:x:1001:1001::/home/zhangsan:/bin/bash
lisi:x:1002:1002::/home/lisi:/bin/bash
wangwu:x:1003:1003::/home/wangwu:/bin/bash
zhaoliu:x:1004:1004::/home/zhaoliu:/bin/bash
wuqi:x:1005:1005::/home/wuqi:/bin/bash
zhuba:x:1006:1006::/home/zhuba:/bin/bash
```

下面的例子用来检测Host 主机是否在线

```bash
[root@linuxprobe ~]## vim ipaddrs.txt
8.8.8.8
114.114.114.114
119.29.29.29
223.5.5.5
22.222.222.222
[root@linuxprobe ~]## vim CheckHosts.sh
#!/bin/bash
HLIST=$(cat ~/ipaddrs.txt)
for IP in $HLIST
do
ping -c 3 -i 0.2 -W 3 $IP &> /dev/null
if [ $? -eq 0 ] ; then
echo "Host $IP is On-line."
else
echo "Host $IP is Off-line."
fi
done
[root@linuxprobe ~]## bash CheckHosts.sh 
Host 8.8.8.8 is Off-line.
Host 114.114.114.114 is On-line.
Host 119.29.29.29 is On-line.
Host 223.5.5.5 is On-line.
Host 22.222.222.222 is Off-line.
```

### while 条件循环语句

![9432da2b-5b63-43dc-b8f7-18735c047888.png](https://storage.live.com/items/55AB66419C029DA!4104?authkey=AFBtWHxjrNHYZLw)

这里简单以一个猜价格的例子说明

```bash
[root@linuxprobe ~]## vi gussprice.sh
#!/bin/bash
PRICE=$(expr $RANDOM % 1000)
TIMES=0
echo "The price is in 0-1000, guess it!"
while true
do
read -p "Please enter a number:" GUESS
let TIMES++
if [ $GUESS -gt 1000 ] || [ $GUESS -lt 0 ] ; then
echo "That's impossible."
elif [ $GUESS -gt $PRICE ] ; then
echo "Your guess is bigger than the price."
elif [ $GUESS -lt $PRICE ] ; then
echo "Your guess is litter than the price."
else
echo "You are right! The price is $PRICE, and you use $TIMES times."
exit 0
fi
done
[root@linuxprobe ~]## bash gussprice.sh 
The price is in 0-1000, guess it!
Please enter a number:-1
That's impossible.
Please enter a number:500
Your guess is bigger than the price.
Please enter a number:250
Your guess is bigger than the price.
Please enter a number:125
Your guess is bigger than the price.
Please enter a number:75
Your guess is bigger than the price.
Please enter a number:37
Your guess is bigger than the price.
Please enter a number:19
Your guess is bigger than the price.
Please enter a number:9
Your guess is litter than the price.
Please enter a number:14
Your guess is litter than the price.
Please enter a number:17
You are right! The price is 17, and you use 10 times.
```

### case 条件测试语句

![30c44532-9404-4dd9-8532-b7fcd4e3b243.png](https://storage.live.com/items/55AB66419C029DA!4105?authkey=AFBtWHxjrNHYZLw)

case 语句是在多个范围内匹配数据，若匹配成功则执行相关命令并结束整个条件测试；而如果数据不在所列出的范围内，则会去执行星号（ *）中所定义的默认命令。

再上一个猜价格的程序中，有个致命的缺陷，如果用户输入了一个英文字母，在和整数进行比较时就会出现错误。这里通过case 语句来进行判断。

```bash
[root@linuxprobe ~]## vim Checkkeys.sh
#!/bin/bash
read -p "请输入一个字符，并按 Enter 键确认： " KEY
case "$KEY" in
[a-z]|[A-Z])
echo "您输入的是 字母。 "
;;
[0-9])
echo "您输入的是 数字。 "
;;
*)
echo "您输入的是 空格、功能键或其他控制字符。 "
esac
[root@linuxprobe ~]## bash Checkkeys.sh
请输入一个字符，并按 Enter 键确认： 6
您输入的是 数字。
[root@linuxprobe ~]## bash Checkkeys.sh
请输入一个字符，并按 Enter 键确认： p
您输入的是 字母。
[root@linuxprobe ~]## bash Checkkeys.sh
请输入一个字符，并按 Enter 键确认： ^[[15~
您输入的是 空格、功能键或其他控制字符。
```

## 计划任务服务程序

计划任务分为一次性计划任务与长期性计划任务，就像闹钟的只响一次和每周五早上8点响铃一样。

我们可以用at 命令实现一次性计划任务，只需要写成”at 时间“的形式就可以。如果想要查看已设置好但还未执行的一次性计划任务，可以使用”at -l“命令；如果想要系那个其删除，可以用”atrm 任务序号“。

```bash
[root@linuxprobe ~]## at 21:00
at> systemctl restart httpd
at> <EOT>
job 1 at Sat Mar 28 21:00:00 2020
```

上面这种方式是交互式方式创建，我们还可以使用管道符进行非交互式创建一次性计划任务服务。

```bash
[root@linuxprobe ~]## at -l
1	Sat Mar 28 21:00:00 2020 a root
[root@linuxprobe ~]## atrm 1
[root@linuxprobe ~]## echo "systemctl restart httpd" | at 21:00
job 2 at Sat Mar 28 21:00:00 2020
[root@linuxprobe ~]## at -l
2	Sat Mar 28 21:00:00 2020 a root
```

如果我们希望Linux 系统能执行长期计划任务，那么使用Linux 系统中默认启用的crond 服务简直再合适不过了。创建、编辑计划任务的命令为”crontab -e“，查看当前计划任务的命令为”crontab -l“，删除某条计划任务的命令为”crontab -r“。另外，如果我们是以管理员身份登录的系统，还可以在crontab 命令中加上-u 参数来编辑他人的计划任务。

crond 服务设置任务的参数格式为”分、时、日、月、星期 命令“，如果某些字段没有设置，则需要使用星号（*）占位。

![6c24ae29-dd6b-4c3f-ba1f-e0038edbe882.png](https://storage.live.com/items/55AB66419C029DA!4106?authkey=AFBtWHxjrNHYZLw)

需要说明的是，除了用逗号（ ,）来分别表示多个时间段，例如“8,9,12”表示 8 月、 9 月和 12 月。还可以用减号（ -）来表示一段连续的时间周期（例如字段“日”的取值为“12-15”，则表示每月的 12～15 日）。以及用除号（ /）表示执行任务的间隔时间（例如“/2”表示每隔2 分钟执行一次任务）之外。

💡如果在 crond 服务中需要同时包含多条计划任务的命令语句，应每行仅写一条。例如我们再添加一条计划任务，它的功能是每周一至周五的凌晨 1 点钟自动清空/tmp 目录内的所有文件。尤其需要注意的是，在 crond 服务的计划任务参数中，所有命令一定要用绝对路径的方式来写，如果不知道绝对路径，请用 whereis 命令进行查询， rm 命令路径为下面输出信息中加粗部分。

值得注意的是：

- 在crond 服务的配置参数中，可以像 Shell 脚本那样以#号开头写上注释信息，这样在日后回顾这段命令代码时可以快速了解其功能、需求以及编写人员等重要信息。
- 计划任务中的“分”字段必须有数值，绝对不能为空或是*号，而“日”和“星期”字段不能同时使用，否则就会发生冲突