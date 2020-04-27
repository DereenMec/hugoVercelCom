---
title: "LinuxNotes.用户身份与文件权限"
date: 2019-09-09T12:46:25+08:00
lastmod: 2019-09-09T12:46:25+08:00
draft: false
tags: [Linux]
categories: [Linux 学习笔记]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

## 用户身份与能力

好多人的潜意识中，Linux 系统中的管理员就是root。这其实是错误的，Linux 系统的管理员之所以是root，并不是因为它的名字叫root，而是因为该用户的身份号码即UID（User IDentification）的数值为0。在Linux 系统中，UID就相当于每个用户的身份证号码，它具有唯一性，因此可通过UID值来判断用户身份。在RHEL 7系统中，用户身份有下面这些：

- 管理员UID为0：系统管理员用户
- 系统用户UID为1~999：Linux 系统为了避免某个服务程序出现漏洞而被黑客提权至整台服务器，默认服务程序会有独立的系统用户负责运行，进而有效控制被破坏范围
- 普通用户UID从1000开始：是由管理员创建的用于日常工作的用户

为了方便管理属于同一组的用户，Linux 系统中还引入了用户组概念。通过使用用户组号码（GID，Group IDentification），我们可以把多个用户加入同一组中，从而方便为组中的用户统一规划权限或指定人物。

另外，在 Linux 系统中创建每个用户时，将自动创建一个与其同名的基本用户组，而且这个基本用户组只有该用户一个人。如果该用户以后被归纳入其他用户组，则这个其他用户组称之为扩展用户组。一个用户只有一个基本用户组，但是可以有多个扩展用户组，从而满足日常的工作需要。

### useradd 用于创建新的用户

useradd [选项] 用户名

使用 useradd 命令创建用户账户。使用该命令创建用户账户时，默认的用户家目录会被存放在/home 目录中，默认的 Shell 解释器为/bin/bash，而且默认会创建一个与该用户同名的基本用户组。这些默认设置可以根据下表中的useradd 命令参数自行修改。

#### 用户参数及其作用

| 参数 | 含义                                     |
| ---- | ---------------------------------------- |
| -d   | 指定用户的家目录（默认为/home/username） |
| -e   | 账户的到期时间，格式为 YYYY-MM-DD.       |
| -u   | 指定该用户的默认 UID                     |
| -g   | 指定一个初始的用户基本组（必须已存在）   |
| -G   | 指定一个或多个扩展用户组                 |
| -N   | 不创建与用户同名的基本用户组             |
| -s   | 指定该用户的默认 Shell 解释器            |

下面我们创建一个普通用户并指定家目录路径、用户UID以及Shell 解释器。下面的命令中，我们使用了/sbin/nologin 作为终端解释器，这代表着该用户不能登录到系统中：

```bash
[root@linuxprobe ~]## useradd -d /home/linux -u 8888 -s /sbin/nologin linuxprobe2
[root@linuxprobe ~]## id linuxprobe2
uid=8888(linuxprobe2) gid=8888(linuxprobe2) groups=8888(linuxprobe2)
```

### groupadd 命令用于创建用户组

groupadd [选项] 群组名

为了更高效地指派系统中各个用户地权限，在工作中常常会把几个用户加入到同一个组，这样可以针对一类用户统一安排权限。

```bash
[root@linuxprobe ~]## groupadd shabi
```

### usermod 命令用于修改用户的属性

usermod [选项] 用户名

在Linux 系统中，一切都是文件，因此在系统中创建用户也就是修改配置文件的过程。我们可以通过修改/etc/passwd 文件来修改其中的用户参数，也可使用usermod 命令修改已经创建的用户信息。

#### 命令中的参数及作用

| 参数  | 含义                                                         |      |
| ----- | ------------------------------------------------------------ | ---- |
| -c    | 填写用户账户的备注信息                                       |      |
| -d -m | 参数-m 与参数-d 连用，可重新指定用户的家目录并自动把旧的数据转移过去 |      |
| -e    | 账户的到期时间，格式为 YYYY-MM-DD                            |      |
| -g    | 变更所属用户组                                               |      |
| -G    | 变更扩展用户组                                               |      |
| -L    | 锁定用户禁止其登录系统                                       |      |
| -U    | 解锁用户，允许其登录系统                                     |      |
| -s    | 变更默认终端                                                 |      |
| -u    | 修改用户的 UID                                               |      |

```bash
[root@linuxprobe ~]## id linuxprobe
uid=1000(linuxprobe) gid=1000(linuxprobe) groups=1000(linuxprobe),10(wheel)
## 修改用户扩展组
[root@linuxprobe ~]## usermod -G root linuxprobe
[root@linuxprobe ~]## id linuxprobe
uid=1000(linuxprobe) gid=1000(linuxprobe) groups=1000(linuxprobe),0(root)
## 删除上一节中建立的Linuxprobe2用户
[root@linuxprobe ~]## userdel -r linuxprobe2
## 修改用户UID
[root@linuxprobe ~]## usermod -u 8888 linuxprobe
[root@linuxprobe Desktop]## id linuxprobe
uid=8888(linuxprobe) gid=1000(linuxprobe) groups=1000(linuxprobe),0(root)
```

### passwd 命令用于修改用户密码、过期时间、认证信息等

passwd [选项] 用户名

普通用户只能使用passwd 命令修改自身的系统密码，而root 管理员则有权限修改其他所有人的密码，而且不需要输入旧密码。

#### 命令中的参数以及作用

| 参数    | 含义                                                         |
| ------- | ------------------------------------------------------------ |
| -l      | 锁定用户，禁止其登录                                         |
| -u      | 解除锁定，允许用户登录                                       |
| --stdin | 允许通过标准输入修改用户密码， 如echo "NewPassWord" \| passwd --stdin  Username |
| -d      | 使该用户可用空密码登录系统                                   |
| -e      | 强制用户在下次登录时修改密码                                 |
| -S      | 显示用户的密码是否被锁定，以及密码所采用的加密算法名称       |

```bash
## 使用管理员修改普通用户密码，当提示密码不足8位只需再输入一次即可
[root@linuxprobe Desktop]## passwd linuxprobe
Changing password for user linuxprobe.
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: all authentication tokens updated successfully.
## 锁定linuxprobe 用户，不允许其登录
[root@linuxprobe Desktop]## passwd -l linuxprobe 
Locking password for user linuxprobe.
passwd: Success
## 查看锁定状态
[root@linuxprobe Desktop]## passwd -S linuxprobe 
linuxprobe LK 2020-03-29 0 99999 7 -1 (Password locked.)
## 解锁
[root@linuxprobe Desktop]## passwd -u linuxprobe 
Unlocking password for user linuxprobe.
passwd: Success
[root@linuxprobe Desktop]## passwd -S linuxprobe 
linuxprobe PS 2020-03-29 0 99999 7 -1 (Password set, SHA512 crypt.)
```

### userdel 命令用于删除用户

userdel [选项] 用户名

#### 参数及其作用

| 参数 | 含义                     |
| ---- | ------------------------ |
| -f   | 强制删除用户             |
| -r   | 同时删除用户及用户家目录 |

```bash
[root@linuxprobe Desktop]## userdel -r linuxprobe
```

## 文件权限与归属

尽管在Linux 系统中一切都是文件，但是每个文件的类型不尽相同，因此Linux 系统使用了不同的字符来加以区分，常见的字符如下所示。

- -：普通文件
- d：目录文件
- l：链接文件
- b：块设备文件
- c：字符设备文件
- p：管道文件

在 Linux 系统中，每个文件都有所属的所有者和所有组，并且规定了文件的所有者、所有组以及其他人对文件所拥有的可读（ r）、可写（ w）、可执行（x）等权限。对于**一般文件**来说，“可读”表示能够读取文件的实际内容；“可写”表示能够编辑、新增、修改、删除文件的实际内容； “可执行”则表示能够运行一个脚本程序。

对**目录文件**来说，可读”表示能够读取目录内的文件列表；“可写”表示能够在目录内新增、删除、重命名文件；而“可执行”则表示能够进入该目录。

![90b42cc9-ec61-4cfb-9ed3-ad8d9ccd631a.png](https://storage.live.com/items/55AB66419C029DA!4108?authkey=AFBtWHxjrNHYZLw)

文件权限的**数字法基于字符表示（rwx）的权限计算而来**，其目的是简化权限的表示。例如，某个文件的权限为777则代表文件所有者、文件所属组和其他用户都：可读、可写、可执行。

![3490838c-7da5-4e3c-b48c-d9e237f1d2a7.png](https://storage.live.com/items/55AB66419C029DA!4109?authkey=AFBtWHxjrNHYZLw)

34298为文件大小（字节），最近一次修改时间为4月2日凌晨23分，文件名为install.log。

## 文件的特殊权限

在复杂多变的生产环境中，单纯设置文件的 rwx 权限无法满足我们对安全和灵活性的需求，因此便有了 SUID、 SGID 与 SBIT 的特殊权限位。这是一种对文件权限进行设置的特殊功能，可以与一般权限同时使用，以弥补一般权限不能实现的功能。

### SUID （针对二进制文件）

在Linux 中，所有账号的密码记录在/etc/shadow 这个文件中，并且只有root 可以读写这个文件。但是普通用户要想修改自己的密码怎么办呢，这叫要通过给/bin/passwd 这个文件赋予SUID权限，然后普通用户就可以在调用passwd 的过程中变为root 身份。但是，如果调用cat 查看/etc/shadow 内容则不可。

![e978b6e2-c14e-4345-b797-1f5faf988707.png](https://storage.live.com/items/55AB66419C029DA!4110?authkey=AFBtWHxjrNHYZLw)

总结：SUID是一种针对**二进制**程序设置的特殊权限，可以让二进制程序的**执行者**在执行的过程中**临时拥有属主的权限**，当然**前提你得有执行权限**。

### SGID（针对二进制文件和目录）

SGID主要实现下面两种功能：

- 让执行者临时拥有属组的权限（对拥有执行权限的二进制程序进行设置）
- 在某个目录中创建的文件自动继承该目录的用户组（只可以对目录进行设置）

SGID的第一种功能类似于SUID，不同点在于执行程序的用户获取的不再是文件所有者的临时权限，而是获取到文件所属组的权限。

在Linux 系统中，每个文件都有其归属的所有者和所属组，当创建或传送一个文件后，这个文件就会自动归属于执行这个操作的用户。如果现在需要在一个部门内设置共享目录，让部门所有人员都能够读取目录中的内容，那么就可以创建部门共享目录后，在该目录上设置SGID 特殊权限位，这样部门内的任何人员在里面创建的任何文件都会归属于该目录的所属组，而不再是自己的基本用户组。

```bash
[root@linuxprobe ~]## mkdir /public
[root@linuxprobe ~]## ls -ald /public/
drwxr-xr-x. 2 root root 6 Mar 29 12:08 /public/
[root@linuxprobe ~]## chmod -Rf 777 /public/
[root@linuxprobe ~]## chmod -Rf g+s /public/
[root@linuxprobe ~]## ls -ald /public/
drwxrwsrwx. 2 root root 6 Mar 29 12:08 /public/
[root@linuxprobe ~]## su - linuxprobe
[linuxprobe@linuxprobe ~]$ cd /public/
[linuxprobe@linuxprobe public]$ touch test.md
[linuxprobe@linuxprobe public]$ ls -ld test.md 
-rw-rw-r--. 1 linuxprobe root 0 Mar 29 12:13 test.md
```

### SBIT（针对目录）

现在，大学里很多老师都要求学生将作业上传到服务器的特定共享目录中，但总是有“破坏分子”喜欢删除其他同学的作业，这时就要设置SBIT（Sticky Bit）特殊权限位了。SBIT特殊权限位可确保用户只能删除自己所属的文件，不能删除其他用户的文件。

```bash
[root@linuxprobe ~]## chmod -Rf 1777 /public/
[root@linuxprobe ~]## vi /public/test.md
Can not be deleted by others.
[root@linuxprobe ~]## su - linuxprobe 
Last login: Sun Mar 29 17:08:15 CST 2020 on pts/0
[linuxprobe@linuxprobe ~]$ rm -rf /public/test.md 
rm: cannot remove ‘/public/test.md’: Operation not permitted
[linuxprobe@linuxprobe ~]$ ls -ld /public/
drwxrwsrwt. 2 root root 20 Mar 29 17:14 /public/
```

### chmod 和chown

chmod 命令用来设置文件或目录的权限，格式为“chmod [参数] 权限 文件或目录名称”。其中常用的参数有：

- R：递归的将该文件夹和子文件夹以及文件设置为同样的权限
- f：静默模式，不显示大多数的错误

除了设置文件或目录的权限外，还可以设置文件或目录的所有者和所属组，这里使用的命令为 chown，其格式为“chown [参数] 所有者:所属组 文件或目录名称”。

通过在常规权限前再加数字来设置特殊权限，4为SUID，2为SGID，1为SBIT。

也可以通过英文设置：

```
chmod u+s testfile ## 为 testfile 文件加上 SUID 权限。
chmod g+s testdir  ## 为 testdir 目录加上 SGID 权限。
chmod o+t testdir  ## 为 testdir 目录加上 SBIT 权限。
```

## 文件的隐藏属性

Linux 系统中的文件除了具备一般权限和特殊权限之外，还有一种隐藏权限，即被隐藏起来的权限，默认情况下不能直接被用户发觉。

### chattr 命令用于设置文件的隐藏权限

chattr [参数] 文件。如果想要把某个隐藏功能添加到文件上，则需要在命令后面追加“+参数”，如果想要把某个隐藏功能移出文件，则需要追加“-参数”。

#### 参数及其作用

| 参数 | 含义                                                         |      |
| ---- | ------------------------------------------------------------ | ---- |
| i    | 无法对文件进行修改；若对目录设置了该参数，则仅能修改其中的子文件内容而不能新建或删除文件 |      |
| a    | 仅允许补充（追加）内容，无法覆盖/删除内容（ Append Only）    |      |
| S    | 文件内容在变更后立即同步到硬盘（ sync）                      |      |
| s    | 彻底从硬盘中删除，不可恢复（用 0 填充原文件所在硬盘区域）    |      |
| A    | 不再修改这个文件或目录的最后访问时间（ atime）               |      |
| b    | 不再修改文件或目录的存取时间                                 |      |
| D    | 检查压缩文件中的错误                                         |      |
| d    | 使用 dump 命令备份时忽略本文件/目录                          |      |
| c    | 默认将文件或目录进行压缩                                     |      |
| u    | 当删除该文件后依然保留其在硬盘中的数据，方便日后恢复         |      |
| t    | 让文件系统支持尾部合并（ tail-merging）                      |      |
| X    | 可以直接访问压缩文件中的内容                                 |      |

```bash
[root@linuxprobe ~]## echo "For test" > linuxprobe
[root@linuxprobe ~]## rm linuxprobe
rm: remove regular file ‘linuxprobe’? y
[root@linuxprobe ~]## echo "For test" > linuxprobe
[root@linuxprobe ~]## chattr +a linuxprobe 
[root@linuxprobe ~]## rm linuxprobe 
rm: remove regular file ‘linuxprobe’? y
rm: cannot remove ‘linuxprobe’: Operation not permitted
```

### lsattr 命令用于显示文件的隐藏权限

lsattr [参数] 文件。隐藏权限使用普通的ls 命令是看不见的，只能使用lsattr 命令查看隐藏权限。

```bash
[root@linuxprobe ~]## ls -la linuxprobe 
-rw-r--r--. 1 root root 9 Mar 29 17:33 linuxprobe
[root@linuxprobe ~]## lsattr linuxprobe 
-----a---------- linuxprobe
[root@linuxprobe ~]## chattr -a linuxprobe 
[root@linuxprobe ~]## lsattr linuxprobe 
---------------- linuxprobe
[root@linuxprobe ~]## rm linuxprobe 
rm: remove regular file ‘linuxprobe’? y
```

## 文件访问控制列表

我们之前接触到的一般权限、特殊权限、隐藏权限有一个共性——权限是针对某一类用户设置的。如果希望对某个指定的用户进行单独的权限控制，就需要用到文件的访问控制列表（ACL）了。通俗来讲，基于普通文件或目录设置ACL 其实就是针对指定的用户或用户组设置文件或目录的操作权限。另外，**如果针对某个目录设置了ACL，则目录中的文件会继承其ACL；若针对文件设置了ACL，则文件不再继承其所在目录的ACL。**

```bash
[root@linuxprobe ~]## su - linuxprobe 
Last login: Sun Mar 29 17:14:55 CST 2020 on pts/0
[linuxprobe@linuxprobe ~]$ cd /root
-bash: cd: /root: Permission denied
[linuxprobe@linuxprobe ~]$ exit
logout
```

如上所示，普通用户是无法进入root管理员的家目录的。

### setfacl 命令用于管理文件的ACL规则

setfacl [参数] 文件名

文件的ACL提供的是在所有者、所属组、其他人的读/写/执行权限之外的特殊权限控制，使用setfacl 命令可以针对单一用户或用户组、单一文件或目录来进行读/写/执行权限的控制。其中，针对目录文件需要使用-R 递归参数；针对普通文件则使用-m 参数；如果要删除某个文件的ACL，则可以使用-b 参数。下面的例子设置用户linuxprobe 在/root 目录上的权限：

```bash
[root@linuxprobe ~]## setfacl -Rm u:linuxprobe:rwx /root
[root@linuxprobe ~]## su - linuxprobe 
Last login: Sun Mar 29 17:42:15 CST 2020 on pts/0
[linuxprobe@linuxprobe ~]$ cd /root
[linuxprobe@linuxprobe root]$ ls
CheckHosts.sh  delusers.sh  Downloads     ipaddrs.txt  Pictures   users.txt
chkhost.sh     Desktop      example.sh    mkcdrom.sh   Public     Videos
clsscore.sh    Documents    gussprice.sh  Music        Templates
[linuxprobe@linuxprobe root]$ cat users.txt
zhangsan
lisi
wangwu
zhaoliu
wuqi
zhuba
[linuxprobe@linuxprobe root]$ exit
logout
```

那么我们如何知道文件上有哪些ACL呢？常用的ls 命令虽然看不到ACL 表信息，但是却可以看到文件的权限最后一个点（.）变成了加号（+），这就意味着该文件已经设置了ACL了。

```bash
[root@linuxprobe ~]## ls -dl /root/
dr-xrwx---+ 14 root root 4096 Mar 29 17:36 /root/
```

### getfacl 命令用于显示文件上设置的ACL信息

getfacl 文件名称

```bash
[root@linuxprobe ~]## getfacl /root
getfacl: Removing leading '/' from absolute path names
## file: root
## owner: root
## group: root
user::r-x
**user:linuxprobe:rwx**
group::r-x
mask::rwx
other::---
```

## su 命令与sudo 服务

在生产环境中，我们不可能像在学习时一直使用root 管理员账户，这样加大了操作失误导致系统崩溃的可能性。

su 命令可以解决切换用户身份的需求，使得当前用户在不退出登录的情况下，顺畅地切换到其他用户，比如从root 管理员切换至普通用户。使用`su - 用户名`来切换，其中的减号（-）意味着切换用户的同时，把环境变量页变更为新用户的相关信息，而不是包留原始的信息。

💡尽管像这样使用su 命令后，普通用户可以完全切换到root 管理员身份来完成相应工作，但这增大了暴露root 管理员密码的可能性。

取而代之的，我们可以使用sudo 命令把特定命令的执行权限赋予给指定用户，这样既可保证普通用户能完成特定工作，也可以避免泄露root 管理员密码。我们要做的就是合理配置sudo 服务，以边兼顾系统的安全性和用户的便捷性。sudo 服务的配置原则也很简单——**在保证普通用户完成相应工作的前提下，尽可能少地赋予额外的权限。**

sudo 命令用于给普通用户提供额外的权限来完成原本root 管理员才能完成的任务，格式为“sudo [参数] 命令名称”。sudo 服务中可用的参数以及相应的作用如表所示。

| 参数             | 含义                                                     |
| ---------------- | -------------------------------------------------------- |
| -h               | 列出帮助信息                                             |
| -l               | 列出当前用户可执行的命令                                 |
| -u 用户名或UID值 | 以指定的用户身份执行命令                                 |
| -k               | 清空密码的有效时间，下次执行 sudo 时需要再次进行密码验证 |
| -b               | 在后台执行指定的命令                                     |
| -p               | 更改询问密码的提示语                                     |

总的来说，sudo 命令具有如下功能：

- 限制用户执行指定的命令
- 记录用户执行的每一条命令
- 配置文件（/etc/sudoers）提供集中的用户管理、权限与主机等参数
- 验证密码后的5分钟内（默认值）无需再让用户验证密码

如果担心直接修改配置文件会出现问题，则可以使用sufo 命令提供的visudo 命令来配置用户权限。这条命令在配置用户权限时将禁止多个用户同时修改sudoers 配置文件，还可以对配置文件内的参数进行语法检查，并在发现参数错误时进行报错。

💡只有root 管理员才可以使用visudo 命令编辑sudo 服务的配置文件

```bash
[root@linuxprobe Desktop]## visudo
96 ##
97 ### Allow root to run any commands anywhere
98 root ALL=(ALL) ALL
99 linuxprobe ALL=(ALL) ALL
[root@linuxprobe Desktop]## su - linuxprobe 
Last login: Sun Mar 29 17:56:33 CST 2020 on pts/0
[linuxprobe@linuxprobe ~]$ sudo -l
[sudo] password for linuxprobe: 
Matching Defaults entries for linuxprobe on this host:
    requiretty, !visiblepw, always_set_home, env_reset, env_keep="COLORS
    DISPLAY HOSTNAME HISTSIZE INPUTRC KDEDIR LS_COLORS", env_keep+="MAIL PS1
    PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE", env_keep+="LC_COLLATE
    LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES", env_keep+="LC_MONETARY
    LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE", env_keep+="LC_TIME LC_ALL
    LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY",
    secure_path=/sbin\\:/bin\\:/usr/sbin\\:/usr/bin

User linuxprobe may run the following commands on this host:
    (ALL) ALL
[linuxprobe@linuxprobe ~]$ sudo ls /root
CheckHosts.sh  delusers.sh  Downloads	  ipaddrs.txt  Pictures   users.txt
chkhost.sh     Desktop	    example.sh	  mkcdrom.sh   Public	  Videos
clsscore.sh    Documents    gussprice.sh  Music        Templates
```

**考虑到生成环境中不允许某个普通用户拥有整个系统中所有的命令的最高执行权，因此ALL参数就有些不合适了。因此只能赋予普通用户具体的命令以满足工作需求，这也受到了必要的权限约束。如果需要让某个用户只能使用root 管理员的身份执行指定的命令，切记一定要给出该命令的绝对路径，否则系统会识别不出。我们可以使用whereis 命令来找出命令所对应的二进制文件的保存路径。**

```bash
[linuxprobe@linuxprobe ~]$ exit
logout
[root@linuxprobe Desktop]## whereis cat
cat: **/usr/bin/cat** /usr/share/man/man1/cat.1.gz /usr/share/man/man1p/cat.1p.gz
[root@linuxprobe Desktop]## visudo
96 ##
97 ### Allow root to run any commands anywhere
98 root ALL=(ALL) ALL
**99 linuxprobe ALL=(ALL) /usr/bin/cat**
[root@linuxprobe Desktop]## su - linuxprobe 
Last login: Mon Mar 30 19:48:49 CST 2020 on pts/0
[linuxprobe@linuxprobe ~]$ cat /etc/shadow
cat: /etc/shadow: Permission denied
[linuxprobe@linuxprobe ~]$ sudo cat /etc/shadow
[sudo] password for linuxprobe: 
root:$6$qPovRFFl$ZXpVbuZDRay7zsc5mjFneiyKRkoxfo7Z8uT05cEadU7EWIiw3I22xZOMdxn5M6KpEJFLKpI2AWXqYksy97cK2.:18348:0:99999:7:::
bin:*:16141:0:99999:7:::
daemon:*:16141:0:99999:7:::
adm:*:16141:0:99999:7:::
lp:*:16141:0:99999:7:::
sync:*:16141:0:99999:7:::
shutdown:*:16141:0:99999:7:::
。。。省略。。。
```

如果觉得每次进行sudo 操作都要输密码很麻烦，可以为linuxprobe 用户添加NOPASSWD 参数。

```bash
[linuxprobe@linuxprobe ~]$ exit
logout
[root@linuxprobe Desktop]## visudo
96 ##
97 ### Allow root to run any commands anywhere
98 root ALL=(ALL) ALL
99 linuxprobe ALL=NOPASSWD: /usr/sbin/poweroff
[root@linuxprobe Desktop]## su - linuxprobe 
Last login: Mon Mar 30 19:55:06 CST 2020 on pts/0
[linuxprobe@linuxprobe ~]$ sudo cat /etc/shadow
root:$6$qPovRFFl$ZXpVbuZDRay7zsc5mjFneiyKRkoxfo7Z8uT05cEadU7EWIiw3I22xZOMdxn5M6KpEJFLKpI2AWXqYksy97cK2.:18348:0:99999:7:::
bin:*:16141:0:99999:7:::
daemon:*:16141:0:99999:7:::
adm:*:16141:0:99999:7:::
lp:*:16141:0:99999:7:::
。。。省略。。。
```

这时就不用输入密码了😊。