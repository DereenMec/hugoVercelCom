---
title: "Ubuntu 拨号上网及校园网开启IPV6"
date: 2019-08-07T11:20:09+08:00
lastmod: 2019-08-07T11:20:09+08:00
draft: false
tags: [Ubuntu]
categories: [Ubuntu]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

Ubuntu 18.04下有两种方法实现拨号上网，第一种是通过图形界面添加，需要开启自动连接，并且要关闭以太网的自动连接。（不推荐这种连接方式）这里介绍第二种，通过`pppoeconf`命令进行拨号。

## 关闭以太网

在使用`pppoeconf`命令拨号前，我们可以直接禁用掉以太网络，这样就不会每次都弹“有线网络1”无法连接网络那种怪提示。

## 使用`pppoeconf`进行拨号连接

在终端输入`sudo pppoeconf`

2. 输入你的用户名（由ISP所提供 注意：输入时请先清除输入框中的“username“，否则可能造成验证错误）。 
3. 输入你的密码（由ISP所提供）。 
4. 如果你已经配置了一个PPPoE的连接，会通知你这个连接将会被修改。 
5. 弹出一个选项:你被询问是否需要'noauth'和'defaultroute'选项和去掉'nodetach',这里选择"Yes"。 
6. Use peer DNS - 选择 "Yes". 
7. Limited MSS problem - 选择 "Yes". 
8. 当你被询问是否在需要在进入系统的时候自动连接，你可以选择"Yes"。 
9. 最后，你会被询问是否马上建立连接。 

## 开启ipv6

通过`pppoeconf`配置的拨号网络本身只开启了IPV4，所以我们需要手动修改配置文件，使其支持IPV6。

在终端输入`sudo gedit /etc/ppp/options`，在最后一行添加如下字段并保存：

`+ipv6 ipv6cp-use-ipaddr`

## 修改动态IPV6地址

`sudo gedit /etc/sysctl.d/10-ipv6-privacy.conf`

将net.ipv6.conf.default.use_tempaddr改为0。

## 重启系统或者，重新拨号，你会发现可以使用IPV6了