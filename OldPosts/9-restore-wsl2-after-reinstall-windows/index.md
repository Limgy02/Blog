# 重装 Windows 后恢复 WSL2 数据

创建于 2021/12/11；编辑于 2024/04/15

---

## 为新的 Windows 系统安装 WSL2

既然都需要恢复了，安装一个新的自然不费力。

在 Windows 功能中安装「虚拟机平台」，在商店安装「Windows Sub System for Linux Preview」和「需要恢复的 Linux 发行版」。

启动新安装的 Linux 发行版并进行配置即可。

> 最好让 Windows 计算机名与重装前一致，毕竟重装系统也没有必要换计算机名，如果一直用重装系统默认分配的那个还是换掉吧。

> 最好在配置 Linux 系统时，使用和之前 Linux 系统相同的用户名。

## 恢复数据

先执行 `wsl --shutdown` 关闭正在运行的 WSL 实例。

根据所安装的 Linux 发行版，导航到虚拟存盘的存放位置，比如 Ubuntu 的存放位置是 `C:\Users\%UserName%\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState\`

将里面的 `ext4.vhdx` 用先前安装的 WSL 的虚拟磁盘文件替换。

再次启动 WSL，可以发现数据都回来了，enjoy!
