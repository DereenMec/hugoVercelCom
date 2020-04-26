---
title: "国内可用Anaconda 源的镜像站及换国内源方法"
date: 2019-03-25T11:20:09+08:00
lastmod: 2019-03-25T11:20:09+08:00
draft: false
tags: [Python]
categories: []
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

目前清华开源镜像站和中科大开源镜像站均已发出公告表示已取得Anaconda授权，不久就将重新上线Anaconda软件源（见文末图）。那目前我知道的国内可用Anaconda源的镜像站就有3个，分别是清华、中科大、上交。大家可以分别测试一下下载速度和稳定性，自行选择最优的。

各个镜像站的设置方法如下，这里只添加了3个常用的频道，如果要添加更多频道请自行添加。

**选择下面一个镜像站的代码复制并替换下面文件中的全部内容，windows为“C:\用户\你的用户名\\.condarc”，Linux为"/home/你的用户名/.condarc"。（若没有这个文件就新建一个，注意文件名为`.condarc`，不要有任何其他后缀）**

- [清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/)

```python
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
ssl_verify: true
```

- [上海交通大学开源镜像站](https://mirrors.sjtug.sjtu.edu.cn/#/)


```python
channels:
  - https://mirrors.sjtug.sjtu.edu.cn/anaconda/pkgs/main/
  - https://mirrors.sjtug.sjtu.edu.cn/anaconda/pkgs/free/
  - https://mirrors.sjtug.sjtu.edu.cn/anaconda/cloud/conda-forge/
ssl_verify: true
```

- [中国科学技术大学 USTC Mirror](https://mirrors.ustc.edu.cn/)

```python
channels:
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/free/
  - https://mirrors.ustc.edu.cn/anaconda/cloud/conda-forge/
ssl_verify: true
```

## 同时建议更换PIP 源，方法见[这里](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/)

![](https://i.loli.net/2019/06/18/5d08d48a7053e18122.png)

![](https://i.loli.net/2019/06/18/5d08d49107a7449366.png)