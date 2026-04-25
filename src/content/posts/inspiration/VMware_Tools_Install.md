---
title: VMware Tools 安装详细教程（Ubuntu 虚拟机）
published: 2026-04-13
description: '本教程适用于 Ubuntu 18.04/20.04/22.04 及以上版本，分为 自动安装（open-vm-tools） 和 手动安装（官方 VMware Tools） 两种方式。'
image: ''
tags: [VMware]
category: '生活灵感'
draft: false 
lang: 'zh_CN'
---
### 一、推荐方式：open-vm-tools（自动安装，简单高效）

1. **打开终端，更新系统**

```bash
sudo apt update
sudo apt upgrade -y
```

1. **安装 open-vm-tools 及桌面集成功能**

```bash
sudo apt install open-vm-tools open-vm-tools-desktop -y
```

1. **重启虚拟机**

```bash
sudo reboot
```

1. **测试 VMware Tools 功能**

>拖拽文件：支持从宿主机拖拽文件至虚拟机桌面。  
>复制粘贴：宿主机与虚拟机之间可以互相复制粘贴文本。  
>分辨率调整：窗口大小拖拽自动调整分辨率。  


### 二、官方 VMware Tools（手动安装方式，适合特殊需求）

1. 在 VMware 菜单中选择：

> 虚拟机 -> 安装 VMware Tools
> 
2. 打开终端，进入 VMware Tools 挂载目录

```bash
cd /media/$USER/VMware\ Tools
```

查看挂载内容：

```bash
ls
```

示例输出：

```bash
VMwareTools-10.x.x.tar.gz
manifest.txt
```

3. 解压 VMware Tools 压缩包

```bash
cp VMwareTools-*.tar.gz /tmp/
cd /tmp
tar -zxvf VMwareTools-*.tar.gz
```

4. 执行安装脚本

```bash
cd vmware-tools-distrib
sudo ./vmware-install.pl
```

提示一路回车，采用默认选项。

1. 安装完成后重启

```bash
sudo reboot
```

### 三、常见问题与解决

| 问题 | 解决方案 |
| --- | --- |
| 分辨率无法自动调整 | 确认 open-vm-tools-desktop 是否正确安装，并重启虚拟机。 |
| 文件拖拽/复制粘贴失效 | 检查 VMware 的虚拟机设置，确认启用了拖放和共享剪贴板功能。 |
| VMware Tools 挂载失败 | 在 VMware 菜单手动点击 安装 VMware Tools。 |
