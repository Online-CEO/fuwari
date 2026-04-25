---
title: iStoreOS 完整备份指南（NanoPi R4S 适用）
published: 2026-04-08
description: '备份分为 「轻量配置备份」 和 「全盘镜像备份」 两种方案，建议根据需求选择'
image: ''
tags: [OpenWRT]
category: '生活灵感'
draft: false 
lang: 'zh_CN'
---

## **📦 方案一：系统配置备份（推荐日常使用）**

适合：更换设备、升级固件前、快速恢复设置

### **方法①：Web 界面备份（最简单）**

1. 登录 iStoreOS 后台 → **「系统」** → **「备份/升级」**
2. 点击 **「生成备份」** 或 **「下载备份」**

保存 `backup-xxx.tar.gz` 到本地电脑

✅ 备份内容：

- 网络配置、防火墙规则
- 已安装插件列表（不含插件数据）
- 系统参数、用户设置

❌ 不包含：

- Docker 容器数据
- 挂载硬盘中的文件
- 自定义脚本（`/root` 等目录）

### **方法②：SSH 命令行备份（更灵活）**

```bash
# 备份配置到 /tmp
sysupgrade -b /tmp/istoreos_backup.tar.gz

# 下载到本地（在电脑执行）
scp root@192.168.100.1:/tmp/istoreos_backup.tar.gz ./

# 恢复时上传后执行
sysupgrade -r /tmp/istoreos_backup.tar.gz
```

## **💾 方案二：全盘镜像备份（灾难恢复级）**

适合：系统彻底重装前、重要环境存档、整机克隆

### **使用 `dd` 命令克隆整个系统盘**

### **步骤 1：安装进度条插件（可选但推荐）**

```bash
# SSH 登录后执行
opkg update
opkg install coreutils-dd
```

### **步骤 2：查看磁盘分区**

```bash
fdisk -l
# 或
parted /dev/mmcblk0 print
```

📌 NanoPi R4S eMMC 通常是 `/dev/mmcblk0`，TF 卡可能是 `/dev/mmcblk1`

### **步骤 3：执行全盘备份**

```bash
# 备份整个 eMMC 到外接存储（如挂载的 U 盘 /mnt/sda1/）
dd if=/dev/mmcblk1 of=/mnt/T3/img_backup/r4s_full_backup_$(date +%YY%mm%dd).img bs=4M status=progress
```

```bash
# 可选：备份同时压缩（节省空间）
dd if=/dev/mmcblk1 bs=4M status=progress | gzip > /mnt/T3/img_backup/r4s_full_backup_$(date +%Y%m%d).img.gz
```

⏱️ 耗时参考：32GB eMMC 约需 10~20 分钟

### 步骤 4: 压缩

```bash
gzip -k r4s_full_bk_20260405.img
```

### **排除空闲空间（高级，大幅减小体积）**

```bash
# 先清零空闲空间（让压缩更高效）
dd if=/dev/zero of=/tmp/zero.fill bs=1M || rm /tmp/zero.fill

# 再执行备份压缩
dd if=/dev/mmcblk0 bs=4M | gzip > backup.img.gz
```

### **步骤 5：验证备份文件**

### 🎯 快速验证命令（一行搞定）

```bash
# 一行命令验证所有关键点
ls -lh r4s_full_bk_20260405.img* && gzip -t r4s_full_bk_20260405.img.gz && echo "✅ 压缩成功" || echo "❌ 压缩失败"
```

### **🔍 方法一：检查文件是否存在（最快速）**

```bash
# 压缩命令
gzip -k r4s_full_backup_20260329.img

# 查看文件
ls -lh r4s_full_backup_20260329.img*

# 预期输出：
# -rw-r--r-- 1 root root 32G Mar 30 00:00 r4s_full_backup_20260329.img      ← 原文件（保留）
# -rw-r--r-- 1 root root 4.2G Mar 30 00:10 r4s_full_backup_20260329.img.gz  ← 压缩文件（新生成）
```

---

### **🔍 方法二：检查文件大小（确认压缩率）**

```bash
# 查看详细信息
ls -lh r4s_full_backup_20260329.img*

# 或使用 du 查看磁盘占用
du -sh r4s_full_backup_20260329.img*
```

---

### 🔍 方法三：测试压缩文件完整性（推荐）

```bash
# 测试压缩文件是否损坏（不解压）
gzip -t r4s_full_backup_20260329.img.gz

# 如果无输出，表示文件完整
# 如果有错误，会显示：gzip: xxx: invalid compressed data--format violated

# 查看测试结果
if gzip -t r4s_full_backup_20260329.img.gz 2>/dev/null; then
    echo "✅ 压缩文件完整"
else
    echo "❌ 压缩文件损坏"
fi
```

✅ **成功标志**：命令无输出（返回码 0）

---

### **🔍 方法四：查看压缩文件信息**

```bash
# 查看压缩文件的详细信息
gzip -l r4s_full_backup_20260329.img.gz

# 输出示例：
#         compressed        uncompressed  ratio uncompressed_name
#        4404234567       34359738368  87.2% r4s_full_backup_20260329.img
```

---

### **🔁 恢复镜像方法**

1. 将 `.img` 文件用 **Rufus / BalenaEtcher / dd** 写入新 TF 卡/eMMC
2. 或 SSH 中反向操作（需 Live 环境）：

```bash
gunzip -c backup.img.gz | dd of=/dev/mmcblk0 bs=4M status=progress
```

---

### **🐳 额外：Docker 数据备份（重要！）**

iStoreOS 的 Docker 数据默认在 `/overlay/volumes` 或 `/docker`，**配置备份不包含这些数据**

```bash
# 备份 Docker 数据
tar czf /mnt/sda1/docker_backup_$(date +%Y%m%d).tar.gz /overlay/volumes/

# 或按容器单独备份
docker run --rm -v 容器名:/data -v /mnt/sda1:/backup alpine \
  tar czf /backup/容器名_$(date +%Y%m%d).tar.gz /data
```

---

### **📋 备份方案对比**

| **方案** | **备份内容** | **恢复速度** | **适用场景** | **存储占用** |
| --- | --- | --- | --- | --- |
| Web 配置备份 | 系统设置+插件列表 | ⚡ 秒级 | 日常升级/换机 | ~1-5MB |
| sysupgrade | 同左+部分自定义文件 | ⚡ 分钟级 | 固件升级前 | ~5-20MB |
| OpenBackRestore | 插件+配置+部分数据 | ⚡ 分钟级 | 插件环境迁移 | ~10-100MB |
| dd 全盘镜像 | 🔥 整个系统盘（含所有数据） | 🐢 10-30 分钟 | 灾难恢复/整机克隆 | ≈ 磁盘容量 |

---

### **💡 最佳实践建议**

**日常**：每月用 Web 界面备份一次配置

1. **大改前**：升级固件/安装高风险插件前，用 `sysupgrade -b` 备份
2. **重要环境**：配置稳定后，用 `dd` 做一次全盘镜像存档
3. **Docker 用户**：单独备份 `/overlay/volumes` 或使用 `docker commit`

> 🔐
> 
> 
> **安全提示**
>
