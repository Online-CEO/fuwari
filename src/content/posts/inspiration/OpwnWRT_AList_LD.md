---
title: 将 AList 挂载为 OpenWrt 的本地磁盘
published: 2026-04-13
description: 'AList 本身只是一个 WebDAV 服务，为了让路由器能把它当成本地硬盘，我们需要用到 Rclone 配合 Fuse 来挂载。'
image: ''
tags: [OpenWRT,AList]
category: '生活灵感'
draft: false 
lang: ''
---
### AList 挂载为 OpenWrt 的本地磁盘

AList 本身只是一个 WebDAV 服务，为了让路由器能把它当成本地硬盘，我们需要用到 **Rclone** 配合 **Fuse** 来挂载。

### **安装 Rclone 及相关依赖**

在 SSH 终端中运行以下命令：

```bash
opkg update
opkg install rclone kmod-fuse fuse-utils
```

### **配置 Rclone 连接 AList**

输入 **rclone config** 进入交互配置界面，按以下步骤进行：

- 输入 n (新建连接)
- 命名为 alist (随便起，自己记住即可)
- 选择存储类型：找到 WebDAV 并输入对应数字（一般是 40 多，具体看列表）
- URL 输入：http://127.0.0.1:5244/dav (本机 AList 的 WebDAV 地址)
- Vendor 选择：Other (输入对应的数字，通常是 5)
- User 输入：你 AList 的登录账号（默认 admin）
- Password 选择 y (输入自己的密码)，然后输入两次 AList 的登录密码
- Bearer token：直接回车跳过
- 其他全部回车默认，最后按 y 确认并按 q 退出。

### **3. 执行挂载命令**

创建一个本地文件夹用来映射网盘：

```bash
mkdir -p /mnt/alist
```

执行挂载命令（直接复制以下整段并运行）：

```bash
rclone mount alist: /mnt/alist \
  --copy-links \--no-gzip-encoding \--no-check-certificate \--allow-other \--allow-non-empty \--umask 000 \--vfs-cache-mode minimal \--daemon
```

*说明：--daemon 表示后台运行。*

此时，你可以输入 df -h 看看是不是多了一个 /mnt/alist，如果有，说明网盘已经成功挂载到路由器了！

### Fuse的作用

**Fuse 是在后台（隐式）发挥作用的。**

具体来说，当你执行最后一步 **rclone mount ...**（挂载命令）的时候，Rclone 就是通过调用 Fuse 来完成工作的。你虽然没有直接敲击带有 fuse 字眼的命令，但如果没有它，挂载会直接报错失败。

为了让你更清楚它的作用，我们可以把原理拆解一下：

### 1. 为什么需要 Fuse？（Fuse 是什么）

Fuse 的全称是 **FUSE (Filesystem in Userspace)**，即“用户空间文件系统”。

**常规硬盘：** 你的路由器插上一个 U 盘（格式可能是 ext4、FAT32、NTFS），Linux 内核原生就认识这些格式，可以直接挂载。

**网盘/AList：** AList 提供的是 WebDAV 协议（一种网络接口），Linux 内核**根本不认识**这种东西，它没办法直接把一个网页接口当成本地硬盘来用。

**Fuse 就是它们之间的“翻译官”或“桥梁”。** 它可以让普通的软件（比如 Rclone）“伪装”成一个本地硬盘驱动器，欺骗 Linux 系统。

### 2. Fuse 在哪一步发挥了作用？

当你执行 rclone mount alist: /mnt/alist 时，真实的流程是这样的：

Rclone 程序启动，并向系统里的 **Fuse** 模块申请：“我要虚拟一个硬盘，挂载到 /mnt/alist 目录下。”

**Fuse 模块**对 Linux 操作系统说：“我这里现在有一个真实的硬盘了，位置在 /mnt/alist，你可以正常读写它。”

此时，你的 Samba 共享或者你用命令去查看 /mnt/alist/电影.mp4 时。

Linux 系统以为它在读本地硬盘，就把读取请求发给 **Fuse**。

**Fuse** 瞬间把请求转交给 **Rclone**。

**Rclone** 再通过 WebDAV 向 **AList** 请求数据。

### 3. 我们安装的包具体是干嘛的？

在第一步中，你执行了 opkg install kmod-fuse fuse-utils：

**kmod-fuse**：这是 OpenWrt 系统的底层内核驱动（kernel module）。没有它，路由器内核不支持上述的“伪装”功能。

**fuse-utils**：这是配套的工具包，让 Rclone 这种外部软件拥有调用 kmod-fuse 的权限和能力。

### 总结

你之所以感觉“没用到它”，是因为 **Rclone 替你跟 Fuse 打了交道**。

如果没有安装 Fuse，Rclone 依然可以工作，但它只能像普通的下载软件一样做 rclone copy（把网盘文件下载到本地）或者 rclone sync（同步文件）。**只有在借助 Fuse 的情况下，Rclone 才能使用 mount 命令，把网盘无缝“变成”路由器上的一个本地文件夹。**