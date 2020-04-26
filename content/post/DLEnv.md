---
title: "Ubuntu 下安装Anaconda + 显卡驱动 + CUDA + CUDNN + 离线安装环境"
date: 2019-04-25T11:20:09+08:00
lastmod: 2019-04-25T11:20:09+08:00
draft: false
tags: [Python, DeepLearning]
categories: []
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

> 写来给自己备忘，并不是什么教程- 。-

## 下载安装包

- Anaconda：https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/

- 显卡驱动：https://www.nvidia.cn/Download/index.aspx?lang=cn

- CUDA：https://developer.nvidia.com/cuda-toolkit-archive 

- CUDNN：https://developer.nvidia.com/rdp/cudnn-download

## 安装

### Anaconda

```bash
bash ~/Downloads/Anaconda3-2019.10-Linux-x86_64.sh
# 如果忘记在conda init 那个选项选择yes 则：
# source <path to conda>/bin/activate
# conda init
# 如果不想打开Terminal 就激活base env，则：
# conda config --set auto_activate_base False
```

离线情况下安装环境：

```bash
# 拷贝已有环境文件夹
/envs/myenv
# 完成迁移
conda create -n myenv_2  --clone path/myenv --offline
```

### 显卡驱动

:warning:在安装显卡驱动前，记得关闭主板的`security boot`，这是因为Linux 的自带显卡驱动是`nouveau`，而英伟达的官方驱动这时候成了第三方驱动。`security boot`禁止第三方驱动。

```bash
# 禁用nouveau
sudo vim /etc/modprobe.d/blacklist.conf
# 在文件最后插入下面两行
blacklist nouveau
options nouveau modeset=0
# 更新系统
sudo update-initramfs -u
# 重启系统
# 验证nouveau 是否已禁用，无输出则说明已经禁用
lsmod | grep nouveau
# 按ctrl + alt + F6 进入命令行
# 关闭图形界面
sudo service lightdm stop
# 卸载原有Nvidia 驱动
sudo apt-get remove nvidia-*
# 给安装文件赋予执行权限
sudo chmod  a+x NVIDIA-Linux-x86_64-410.129-diagnostic.run
# 安装
sudo ./NVIDIA-Linux-x86_64-410.129-diagnostic.run -no-x-check -no-nouveau-check -no-opengl-files
# 挂载Nvidia 驱动
modprobe nvidia
# 检查是否安装成功
nvidia-smi
# 重启
reboot
```

### CUDA

```bash
sudo sh cuda_10.0.130_410.48_linux.run
# 安装过程记得不要安装显卡驱动（因为之前已安装了最新版）
# 配置环境变量
sudo gedit ~/.bashrc
# 在末尾添加：
export PATH=/usr/local/cuda-10.0/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-10.0/lib64:$LD_LIBRARY_PATH
# 刷新
source ~/.bashrc
```

### CUDNN

```bash
# 解压并移动文件
tar -zxvf cudnn-10.0-linux-x64-v7.6.4.38.tgz
cd cuda
sudo mv lib64/lib* /usr/local/cuda-10.0/lib64/
sudo mv include/cudnn.h /usr/local/cuda-10.0/include/
# 切换到/usr/local/cuda/lib64/文件夹下
cd /usr/local/cuda/lib64/
# 建立软连接，注意替换自己的版本号
sudo chmod +r libcudnn.so.7.6.4
sudo ln -sf libcudnn.so.7.6.4 libcudnn.so.7
sudo ln -sf libcudnn.so.7 libcudnn.so
sudo ldconfig
```