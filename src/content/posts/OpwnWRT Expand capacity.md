---
title: OpenWRT 扩充系统盘
published: 2026-04-13
description: 'NanoPi R4S 刷入 [iStoreOS](https://zhida.zhihu.com/search?content_id=272173889&content_type=Article&match_order=1&q=iStoreOS&zhida_source=entity) 后，默认根分区只有 2GB 左右，需要通过以下步骤扩容到完整容量'
image: ''
tags: [OpenWRT,Disk]
category: 'Examples'
draft: false 
lang: ''
---
### 📋 扩容前的准备

NanoPi R4S 刷入 [iStoreOS](https://zhida.zhihu.com/search?content_id=272173889&content_type=Article&match_order=1&q=iStoreOS&zhida_source=entity) 后，默认根分区只有 2GB 左右，需要通过以下步骤扩容到完整容量

测试设备与版本号:**NanoPi R4S iStoreOS 24.10.5**

## 🔧 扩容步骤

### **第一步：[SSH](https://zhida.zhihu.com/search?content_id=272173889&content_type=Article&match_order=1&q=SSH&zhida_source=entity) 登录系统**

通过 SSH 连接到您的 iStoreOS：

```bash
ssh root@192.168.100.1
```

[](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### **第二步：查看当前分区情况**

```bash
parted
(parted) print
```

查看输出信息，确认：

- 磁盘设备名（如 `/dev/mmcblk0` 或 `/dev/mmcblk2`）
- 磁盘总容量
- 需要扩容的分区号（通常是第 3 个分区）

示例输出：

```bash
Model: MMC HBG4a (sd/mmc)
Disk /dev/mmcblk0: 31.0GB
Partition Table: gpt

Number  Start   End     Size    File system  Flags
1       17.4kB  262kB   245kB              bios_grub
2       262kB   134MB   134MB   fat16        legacy_boot
3       403MB   10.0GB  9597MB  ext4
```

[](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### **第三步：调整分区大小**

```bash
(parted) resizepart
Partition number? 3  # 输入要扩容的分区号
Warning: Partition /dev/mmcblk0p3 is being used. Are you sure you want to continue?
Yes/No? yes
End? [10.0GB]? 31GB  # 输入新的结束位置（可使用磁盘总容量）
(parted) quit
```

[](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**注意**：

- 回答 `yes` 确认在线调整
- End 值可以设置为磁盘总容量（如 31GB 表示预留部分空间）

### **第四步：扩展文件系统**（关键步骤）

这是最重要的一步！只调整分区大小是不够的，还必须扩展文件系统

```bash
resize2fs -p /dev/mmcblk0p3
```

[](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**说明**：

- `/dev/mmcblk0p3` 表示第 3 个分区（根据您的实际情况调整）
- `p` 参数显示进度信息
- 此命令可以在线执行，无需卸载分区

### **第五步：验证扩容结果**

```bash
df -h
```

[](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

查看根分区（`/` 或 `/overlay`）的容量是否已更新。

### ⚠️ 注意事项

1. **设备名称**：
- NanoPi R4S 使用 eMMC 时通常是 `/dev/mmcblk0` 或 `/dev/mmcblk2`
- 分区号为 3（`/dev/mmcblk0p3`）
1. **无需重启**：整个过程可以在线完成，不需要重启系统[http://blog.zhheo.com](https://link.zhihu.com/?target=http%3A//blog.zhheo.com)
2. **备份重要数据**：虽然可以在线扩容，但建议操作前备份重要配置