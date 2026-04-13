---
title: 如何查看、删除和禁用 Windows 运行历史记录
published: 2026-04-13
description: ''
image: ''
tags: [Windows]
category: '生活灵感'
draft: false 
lang: ''
---
从 Windows 95 时代开始，「[运行]」对话框就是 Windows 系统中一个极其实用的功能。通过它，我们能快速启动各种 Windows 工具、打开文件和文件夹、启动应用，甚至访问网络资源。更方便的是，直接敲入一些 [CMD] 命令或者修改[注册表]，它都能一步执行到位。

默认情况下，Windows 会自动保存你执行过的命令，这样下次输入时就能自动补全，让重复操作更加便捷。
![](https://i-blog.csdnimg.cn/img_convert/9c2bf6e830a4830f0824be098edcff6a.jpeg)
![CSDN图标](https://i-blog.csdnimg.cn/img_convert/9c2bf6e830a4830f0824be098edcff6a.jpeg "CSDN图标")

<!-- ![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==) -->

在使用「运行」对话框时：

- 可以通过鼠标点击或键盘上、下箭头来切换选择条目，找到需要的命令后，直接「回车」即可重复执行。
- 如果你觉得列表太长，或者出于隐私考虑。可以手动关闭自动记录功能，让它不再记录；或者把历史记录清零，以便重新开始。

## 如何删除 Windows「运行」历史记录

### 方法 1：通过「[文件夹选项]」清除「运行」历史

要完全清空「运行」对话框中的历史记录，请按以下步骤操作：

1使用`Window + R`快捷键打开「运行」命令框，执行`control folders`打开「文件夹选项」。

2在「常规」选项卡的「隐私」区域中点击「清除」按钮。

![](https://i-blog.csdnimg.cn/img_convert/1aca2c8ffe1c9ba2e89071cd856dfada.jpeg)

[](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

编辑

点击「清除」按钮

这样不仅能清空文件资源管理器的浏览历史，连「运行」历史记录也会一并清除干净。

相关阅读：[如何清除「资源管理器」搜索历史记录]

### 方法 2：通过注册表删除「运行」历史记录

如果你只想删除某些特定的记录，而不是一键清空所有，可以通过编辑注册表来实现，具体步骤是：

1使用`Window + R`快捷键打开「运行」对话框，执行`regedit`打开[注册表编辑器]。

2导航到：

**复制**复制**复制**复制HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU

在右侧窗格中，会以字符串值形式，显示所有记录。

- 右键删除`RunMRU`键，就可以清除所有记录。

![](https://i-blog.csdnimg.cn/img_convert/5a5d03f0574be0e63d19389943e0c8c0.jpeg)

[](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

编辑

- 如果只想删除特定条目，可以在右侧窗格中逐一删除。（每条记录对应一个字母如 a, b, c…）

![](https://i-blog.csdnimg.cn/img_convert/f0409b259e98b226592ac5209916710d.jpeg)

[](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

编辑

完成删除操作后，下次再打开「运行」窗口时，之前删除或清空的记录就没有了。

## 如何禁用「运行」对话框历史记录

要彻底禁用 Windows「运行」对话框的历史记录功能，可以使用以下两种方法。

### 方法 1：通过「设置」禁用「运行」历史记录

1使用`Windows + I`快捷键打开「设置」，在左侧面板中选择「隐私和安全性」，然后点击右侧的「常规」选项。

![](https://i-blog.csdnimg.cn/img_convert/37c1b00da00ac5e0c19c9d36098278ae.jpeg)

[](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

编辑

2关掉「允许 Windows 跟踪应用启动」开关，这样「运行」的历史记录功能就会被禁用。

![](https://i-blog.csdnimg.cn/img_convert/d996a6c835630a28e1de5c84bb5da7ad.jpeg)

[](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

编辑

使用「设置」禁用 Windows「运行」历史记录

### 方法 2：通过注册表，启用或禁用「运行」历史记录

1使用`Window + R`快捷键调出「运行」对话框，执行`regedit`打开注册表编辑器。

2导航到：

**复制**复制**复制HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced

3在右侧窗格中找到并双击[`Start_TrackProgs`]，将其十六进制值设置为：

- `0` 禁用「运行」历史记录
- `1` 启用「运行」历史记录

![](https://i-blog.csdnimg.cn/img_convert/83825cffd70e5b974a22993d276cb1a2.jpeg)

[](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

编辑

使用注册表禁用 Windows「运行」历史记录

更改以上注册表项无需重启系统即可生效。

---

本文介绍了在 Windows 系统中管理「运行」命令历史记录的几种方法。你可以根据需要清除历史记录、删除特定记录，或者完全禁用「运行」历史记录功能。