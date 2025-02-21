# 在 Debian 系统驱动 Realtek 8822CE 无线网卡

创建于 2021/09/20；编辑于 2025/02/21

---

因为想折腾，于是打算把 Ubuntu 换掉，装个 Debian 玩玩，又因为想装 Gnome 40，就打算从命令行的 Debian 自己安装图形界面了。结果发现 Realtek 8822CE 这张无线网卡驱动并不能被成功加载。

## 解决过程记录

### 系统安装

会提醒「需要 rtw88/rtw8822c_fw.bin 和 rtw8822c_wow_fw.bin」，尝试将这两个文件放在移动硬盘和安装 U 盘里，均无法加载，尝试失败（或许有第二个 U 盘就能成功？），于是在无网络环境下安装 Debian.

### 安装网卡驱动

从网络上下载此软件包：[firmware-realtek](https://pkgs.org/download/firmware-realtek)

将 `firmware-realtek_xxxxxxxx.deb` 复制到 U 盘，回到 Debian.

使用 root 权限，执行 `mkdir /mnt/usb && mount /dev/sdb1 /mnt/usb` 挂载 U 盘（sdb1 自行调整）。

执行 `dpkg -i "firmware-realtek_xxxxxxxx.deb 所在位置"`，安装驱动。

此时出现错误：'ldconfig' not found in PATH or not executable

执行 `vi /root/.bashrc` 修改其中内容，添加 `export PATH=/sbin:/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin` 后保存，再执行 `source /root/.bashrc` 即可解决问题。再次执行 `dpkg -i xxx.deb` 命令安装，驱动安装成功。

### 连接网络

从网络上下载如下软件包：

[libpolkit-gobject-1-0](https://pkgs.org/download/libpolkit-gobject-1-0) [libpolkit-agent-1-0](https://pkgs.org/download/libpolkit-agent-1-0) [policykit-1](https://pkgs.org/download/policykit-1) [libteamdctl0](https://pkgs.org/download/libteamdctl0) [libnm0](https://pkgs.org/download/libnm0) [libndp0](https://pkgs.org/download/libndp0) [libmm-glib0](https://pkgs.org/download/libmm-glib0) [libbluetooth3](https://pkgs.org/download/libbluetooth3) [network-manager](https://pkgs.org/download/network-manager)

使用上一步的方法，按照顺序全部安装。

> 如果缺失依赖，可自行从上方所述网站中搜索依赖名称，选择对应的版本进行下载。

使用 root 权限，执行以下命令连接 WiFi ：  

```shell
nmcli dev
nmcli r wifi on
nmcli dev wifi
nmcli dev wifi connect "WiFi 名称" password "密码"
```

### 检测网络连接状态

执行命令 `nmcli dev`，如果网络连接成功，可以看到绿色 connected 样提示。
