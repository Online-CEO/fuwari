---
title: rclone + WebDAV 文件同步指南：安装、挂载与 FreeFileSync 实战
published: 2026-05-16
description: "从 Windows 安装 rclone、配置 PATH，到 WebDAV 挂载、copy、sync 与 FreeFileSync 联动，这篇文章把整套文件同步流程一次讲清楚。"
image: "./rclone-freefilesync-webdav-sync-cover.svg"
tags: [rclone, WebDAV, FreeFileSync, Windows, 同步]
category: "精品软件"
draft: false
lang: "zh_CN"
---

如果已经把 WebDAV 远程配置成了 `ctfile`，接下来最常见的需求就是两种：

- 把网盘挂载成一个盘符，交给 FreeFileSync 这类图形工具使用
- 直接用 `rclone sync` 或 `rclone copy` 做命令行同步

这两条路都能走，但适用场景不完全一样。下面把最容易混淆的几个命令一次讲清楚。

## 先安装 rclone

如果你是在 Windows 上使用 `rclone`，最直接的方式通常不是“安装程序”，而是下载压缩包后手动解压。

例如可以把它解压到：

```text
A:\Program Files\rclone
```

确保这个目录里能看到：

```text
A:\Program Files\rclone\rclone.exe
```

如果只是临时使用，也可以直接用完整路径运行：

```powershell
& "A:\Program Files\rclone\rclone.exe" version
```

但如果后面要长期使用，或者要配合计划任务、FreeFileSync、PowerShell 脚本，就更建议把 `rclone` 的目录加入系统环境变量 `PATH`。

## 把 rclone 目录加入 Windows 的 PATH

把 `rclone.exe` 所在目录加入 `PATH` 之后，后面就可以直接使用：

```powershell
rclone version
```

而不需要每次都写完整路径。

### 图形界面方式

1. 打开 `此电脑`，右键 `属性`
2. 进入 `高级系统设置`
3. 点击 `环境变量`
4. 在“用户变量”或“系统变量”里找到 `Path`
5. 点击 `编辑`
6. 新增一行：

```text
A:\Program Files\rclone
```

7. 一路点确定保存
8. 关闭并重新打开 PowerShell、CMD、Windows Terminal 或 VS Code 终端

重新打开终端后，执行下面这条命令验证：

```powershell
rclone version
```

如果能看到版本号，说明已经生效。

### PowerShell 方式

如果你更喜欢用命令行，也可以把目录追加到当前用户的 `PATH`：

```powershell
$target = "A:\Program Files\rclone"
$userPath = [Environment]::GetEnvironmentVariable("Path", "User")
[Environment]::SetEnvironmentVariable("Path", "$userPath;$target", "User")
```

执行完成后，同样需要重新打开一个新的终端窗口，再运行：

```powershell
rclone version
```

### 一个容易踩到的小坑

如果你已经把路径写进了 `PATH`，但当前窗口里执行 `rclone` 仍然提示“无法识别该命令”，通常不是安装失败，而是：

- 这个终端窗口是在修改 `PATH` 之前打开的
- 当前进程还没有拿到最新的环境变量

这时只要关掉当前终端，重新打开一个新窗口即可。

## 先说结论

如果你的目标是“本地文件夹和网盘做稳定同步”，建议优先这样理解：

- `mount` 适合把网盘挂成盘符给 Windows 软件使用
- `copy` 适合只上传或只下载，不删除目标已有文件
- `sync` 适合让目标变成源的镜像，会删除目标里多出来的文件

对 `FreeFileSync + WebDAV` 这个场景，我更推荐从下面两种方式里选一种：

1. 想继续用 FreeFileSync 的图形界面和双向同步逻辑
   用 `rclone mount` 先把远程挂成 `Z:` 盘，再让 FreeFileSync 同步本地目录和 `Z:`。

2. 想要更直接、更适合脚本和定时任务
   直接用 `rclone sync` 或 `rclone copy`，不要再额外挂载盘符。

## rclone mount 怎么用

如果你想把 `ctfile` 挂载成 `Z:` 盘：

```powershell
rclone mount ctfile: Z: --network-mode --vfs-cache-mode full
```

这条命令适合以下场景：

- 需要在资源管理器里像普通盘一样浏览文件
- 需要给 FreeFileSync、Office 或其他 Windows 软件直接访问
- 希望操作方式尽量接近本地硬盘

### `--network-mode` 的作用

`--network-mode` 的重点不是提速，而是让 Windows 更明确地把这个挂载识别为网络驱动器。这样很多软件兼容性会更稳一些。

优点：

- 对 Windows 和部分第三方软件更友好
- 盘符识别通常更稳定

缺点：

- 有些程序会把它当作网络位置，而不是本地磁盘
- 少数只接受“本地硬盘”的软件可能不认

### `--vfs-cache-mode` 的作用

`--vfs-cache-mode` 决定 rclone 为了模拟“像本地盘一样读写”，要在本地做多少缓存。

常见几档：

- `off`：最省空间，但兼容性最弱
- `writes`：写入更稳，适合简单同步
- `full`：兼容性最好，更适合复杂读写和第三方软件

如果只是普通浏览和复制，可以先从默认值开始；如果要配合 FreeFileSync，我更建议用：

```powershell
rclone mount ctfile: Z: --network-mode --vfs-cache-mode full
```

因为 FreeFileSync 会频繁扫描目录、比较大小和时间戳、覆盖写入文件，`full` 往往更省心。

### 一个更适合长期使用的挂载命令

```powershell
rclone mount ctfile: Z: --network-mode --vfs-cache-mode full --cache-dir "A:/rclone-cache" --dir-cache-time 10m --poll-interval 0 --buffer-size 16M
```

参数说明：

- `--cache-dir "A:/rclone-cache"`：把缓存放到固定目录，方便管理
- `--dir-cache-time 10m`：减少频繁刷新目录，提高稳定性
- `--poll-interval 0`：WebDAV 场景通常不依赖变更通知，关掉更简单
- `--buffer-size 16M`：给读写留一点缓冲，够用且不会太吃内存

## rclone sync 是怎么工作的

`rclone sync` 的核心规则只有一句：

**让目标变成和源一模一样。**

也就是说，如果目标里有文件，但源里已经没有了，`sync` 会把目标那份也删除。

基本语法：

```powershell
rclone sync 源 目标
```

例如把本地目录同步到 `ctfile`：

```powershell
rclone sync "A:\01.Common Files" "ctfile:02.Workspace/01.Common Files"
```

这条命令的含义是：

- 本地有、远程没有：上传
- 两边都有但内容不同：更新远程
- 远程有、但本地没有：删除远程

所以第一次执行前，强烈建议先做预演：

```powershell
rclone sync "A:\01.Common Files" "ctfile:02.Workspace/01.Common Files" --dry-run --progress --create-empty-src-dirs --transfers 4 --checkers 8
```

确认输出没问题，再执行正式同步：

```powershell
rclone sync "A:\01.Common Files" "ctfile:02.Workspace/01.Common Files" --progress --create-empty-src-dirs --transfers 4 --checkers 8
```

这里有两个很关键的小细节：

- 本地路径和远程路径都加引号，因为路径里有空格
- 远程路径更推荐使用 `/`，兼容性通常更好

## rclone copy 和 sync 的区别

如果你不希望删除目标里已有但源中没有的文件，就不要用 `sync`，改用 `copy`：

```powershell
rclone copy "A:\01.Common Files" "ctfile:02.Workspace/01.Common Files" --progress --create-empty-src-dirs --transfers 4 --checkers 8
```

可以这样记：

- `copy`：只复制新增和变更，不删目标多余文件
- `sync`：目标完全跟着源走，目标多余文件会被删

对第一次上手来说，`copy` 更安全；对已经确认目录结构、就是想做镜像同步的场景，`sync` 更准确。

## FreeFileSync 场景下怎么选

如果你主要依赖 FreeFileSync，那最常见的是下面两种策略。

### 方案一：挂载盘符后交给 FreeFileSync

适合以下情况：

- 习惯图形界面
- 需要双向同步
- 想在同步前先人工检查比较结果

推荐命令：

```powershell
rclone mount ctfile: Z: --network-mode --vfs-cache-mode full --cache-dir "A:/rclone-cache" --dir-cache-time 10m --poll-interval 0 --buffer-size 16M
```

然后让 FreeFileSync 同步：

- 左侧：本地目录，例如 `A:\01.Common Files`
- 右侧：远程挂载盘，例如 `Z:\02.Workspace\01.Common Files`

### 方案二：直接用 rclone 做单向同步

适合以下情况：

- 只需要单向上传或单向下载
- 想做脚本、计划任务或无人值守执行
- 不想依赖挂载盘符

上传镜像：

```powershell
rclone sync "A:\01.Common Files" "ctfile:02.Workspace/01.Common Files" --progress --create-empty-src-dirs --transfers 4 --checkers 8
```

更保守一点的上传：

```powershell
rclone copy "A:\01.Common Files" "ctfile:02.Workspace/01.Common Files" --progress --create-empty-src-dirs --transfers 4 --checkers 8
```

## 我更推荐哪一种

如果目录不大，而且你更看重可视化和人工确认，我会优先推荐：

- `rclone mount`
- `FreeFileSync`

如果目录很大、同步次数多，或者后面想做自动化任务，我更推荐：

- 用 `rclone copy` 先建立一套安全流程
- 确认没问题后，再考虑是否切换到 `rclone sync`

## 使用前提醒

在正式同步前，最好先记住下面几条：

- 第一次跑 `sync` 前一定先加 `--dry-run`
- 不确定是否该删目标文件时，先用 `copy`
- 路径里有空格时，记得给源路径和目标路径都加引号
- WebDAV 本质上还是远程存储，超多小文件时速度通常不如本地磁盘

如果你的主要目标是“稳定地把本地目录同步到网盘”，其实最稳的起步方式就是下面两条：

```powershell
rclone copy "A:\01.Common Files" "ctfile:02.Workspace/01.Common Files" --dry-run --progress --create-empty-src-dirs --transfers 4 --checkers 8
```

确认输出无误后：

```powershell
rclone copy "A:\01.Common Files" "ctfile:02.Workspace/01.Common Files" --progress --create-empty-src-dirs --transfers 4 --checkers 8
```

等你完全确认目录关系以后，再把 `copy` 换成 `sync`。
