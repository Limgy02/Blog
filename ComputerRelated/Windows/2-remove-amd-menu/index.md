# 删除 Windows 11 中右键菜单的 AMD 驱动选项

创建于 2024/10/22；编辑于 2025/02/22

---

> 基本可认为本文转载自 [这里](https://www.iszy.cc/posts/remove-win11-amd-menu/)，我将其按照我习惯的方式复述了一遍，主要目的还是方便找，此外避免原文哪一天突然没了。

打开注册表编辑器，导航到 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Classes\PackagedCom\Package`，寻找包含 AMDRadeonSoftware 类似内容的项，将其打开，再展开 Sever 和 0，找到 SurrogateAppId 并将其值复制到其他文本编辑器中。

随后在注册表编辑器中导航到 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Shell Extensions`，右键新建项名为 `Blocked`，进入 该项，右侧新建 String Value，**命名**为刚才复制的 SurrogateAppId 的值，该条目的值不用管，默认为空即可。
