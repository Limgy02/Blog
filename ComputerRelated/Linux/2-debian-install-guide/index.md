# Debian Linux 安装指南

创建于 2025/02/03；编辑于 2025/02/22

---

## 下载 Debian Daily ISO 映像文件

对于个人计算机上安装桌面环境的使用情景，需要比较新的软件包，适配新硬件来达到比较好的使用体验，因此选择 Debian Daily ISO 映像文件进行安装。

可以在 [这里](https://cdimage.debian.org/cdimage/daily-builds/daily/arch-latest/amd64/iso-cd/) 下载到它，名为 `debian-testing-amd64-netinst.iso`.

## 创建 USB 启动盘

需要将 U 盘格式化成 FAT32 格式，如果 U 盘大于 32GB，由于 Windows 系统的限制，可能无法在格式化窗口中选择 FAT32 格式，可以尝试执行下列命令：

```cmd
diskpart
[diskpart] list disk
[diskpart] select disk X
[diskpart] clean
[diskpart] create partition primary size=32768
[diskpart] format quick fs=fat32
[diskpart] assign letter=E
```

> 注意不要选错磁盘避免数据丢失，注意备份 U 盘数据，注意分配可用盘符。

## 从 USB 设备启动

因设备不同而异，如果发现无法启动，可以查看主板 BIOS 中是否有类似「Enable Microsoft 3rd party CA」的相关选项，尝试将其打开再试。

## 系统安装

大部分场合下根据安装向导安装即可，有以下几点需要注意：
- 不要设置 root 密码，否则不会自动安装 sudo
- 在分区界面，磁盘大小的容量换算是按照 1000 换算的，一块 1TB 的磁盘总容量为 1024GB（在 Windows 中只有 950+GB）
- 不要设置 Swap 分区，推荐在后期配置 Swap 文件，使用 Swap 文件的方法调整大小更容易
- [对于中国用户] 当选择 apt 源的时候，最好配置代理，否则可能会有几个软件包下载速度非常慢；一个可行的方法是使用手机上的代理软件在同一 Wi-Fi 环境下共享代理，假设共享的 IP 地址是 192.168.31.33，端口为 1082，那应当在安装程序中输入的代理服务器地址就是 http://192.168.31.33:1082，若提示无网络，请检查手机的代理是否已经开启。

## 配置桌面环境（KDE Plasma）

### 设置 Swap 文件

- `sudo fallocate -l 32G /swapfile` 大小按需分配即可，此处使用了与电脑内存大小相同的 32GB，此处的 32GB 在 KDE 资源监视器中显示的大小就是标准的 32GB。
- `sudo chmod 0600 /swapfile`
- `sudo mkswap /swapfile`
- `sudo nano /etc/fstab`，新建一行，写入 `/swapfile swap swap defaults 0 0`，没有必要和之前的对齐。
- `swapon /swapfile`

### 卸载用不上的软件

使用 `sudo apt purge` 命令，按需卸载如下软件：

- Libre Office: `*libreoffice*`
- Firefox Browser: `*firefox*`
- KDE Connect: `*kdeconnect*`
- Gimp: `*gimp*`

### 安装 Google Chrome 浏览器

执行 `wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb` 下载安装包，随后执行 `sudo apt install ./<FILENAME>` 来安装。

安装完成后不要直接打开，请重新启动操作系统后再打开。

### 设置系统代理

在系统设置中选择手动配置系统代理，并且选中「Use one proxy server for all protocol」，Chrome 浏览器即可识别到代理并正常上网。
