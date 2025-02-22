# Arch Linux 安装指南

创建于 2025/02/03；编辑于 2025/02/22

---

## 系统安装

1. 执行 `systemctl stop reflector.service` 停用 reflector 服务，它会尝试使用最快的源，但在中国网络环境下它并不能起作用，还会把原有源（的注释）全部删除，到时候再想添加源就只能手打了。
2. 连接网络，使用 `iwctl` 命令，结合 `device list`（列出设备），`station <DEVICE NAME> scan`（扫描网络），`station <DEVICE NAME> get-networks`（显示扫描到的网络）和 `station <DEVICE NAME> connect <NETWORK SSID>`  这些命令进行连接即可。连接完成后等待一段时间，可以使用 `ping www.gnu.org` 判断是否连接成功，需要手动 Ctrl + C 停止 ping 命令。
3. 同步系统时间：`timedatectl set-ntp true`，再执行 `timedatectl status` 判断是否同步成功。
4. 修改国内镜像源：`nano /etc/pacman.d/mirrorlist`，在里面可以找到 `## China` 的注释，从里面挑一个，Alt + A 选择，Ctrl + K 剪切，Ctrl + U 粘贴到最前面就行。
5. 磁盘分区，使用 `lsblk` 查看磁盘信息，再使用 fdisk 工具进行分区，比如 `fdisk /dev/nvme0n1`。根据工具提示使用即可，需要注意的是可以用 +20G 等代表结束区块（G 就是 Windows 里面的 GB），不用拿计算器算；分区分出 EFI 系统分区，根目录，家目录和 Swap 即可，EFI 系统分区标签选第一个，根目录和家目录使用 Linux FileSystem 的标签，按下 w 写入磁盘并退出 fdisk 分区程序。
6. 格式化分区，先使用 `mkfs.vfat -F 32 /dev/nvme0n1p1` 将 EFI 系统分区格式化为 FAT32 格式，再使用 `mkfs.ext4 /dev/nvme0n1p2` 等命令格式化根目录和家目录。
7. 挂载分区：先挂载根目录所在分区到 /mnt，即 `mount /dev/nvme0n1p2 /mnt`，再挂载 EFI 分区到 /mnt/boot/efi，再挂载家目录所在分区到 /mnt/home，如果挂载文件夹不存在，可以在 mount 的时候使用 --mkdir 参数。
8. 安装系统（同时记得安装一个文本编辑器，此处使用 nano）：`pacstrap /mnt base base-devel linux linux-firmware nano`
9. 生成 fstab：`genfstab -U /mnt >> /mnt/etc/fstab`，之后 cat 看一下所有要使用的分区是否都已经包含。
10. 使用 `arch-chroot /mnt` 进入新安装的系统。
11. 配置主机名：`nano /etc/hostname`，输入一行主机名保存即可。
12. 配置 hosts：`nano /etc/hosts`，输入以下内容：
    ```
    127.0.0.1           localhost
    ::1                     localhost
    127.0.1.1           <HOSTNAME>.localdomain		<HOSTNAME>
    ```
13. 设置时区，创建一个链接即可：`ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime`
14. 设置硬件时间：`hwclock --systohc`
15. 配置语言环境，`nano /etc/locale.gen` 之后，把 en_US.UTF-8 UTF-8 和 zh_CN.UTF-8 UTF-8 之前的注释去掉，保存，执行 `locale-gen` 进行更新。
16. 设置 LANG 变量，`nano /etc/locale.conf`，之后向其中写入：`LANG=en_US.UTF-8`
17. 设置root密码和创建普通用户
    1. 设置 root 密码：`passwd root`
    2. 创建普通用户：`useradd -m -G wheel -s /bin/bash <USERNAME>` （不能包含大写英文字母）
    3. 设置普通用户的密码：`passwd <USERNAME>`
    4. 配置 sudo，先 `pacman -S sudo` 安装 sudo，随后 `EDITOR=nano visudo`，将 `%wheel ALL=(ALL:ALL)ALL` 前面的注释去掉。
18. 编辑 /etc/pacman.conf，将 ParallelDownloads 取消注释，开启多线程下载；再将 [multilib] 两行取消注释，启用 32 位程序包，随后进行 `pacman -Syyu`
19. 安装 CPU 微码：`pacman -S intel-ucode` 或者 `amd-ucode`
20. 安装显卡驱动，可参照 [这里](https://archlinuxstudio.github.io/ArchLinuxTutorial/#/rookie/graphic_driver)，这里为 AMD R7 5800H 安装核显驱动：`sudo pacman -S mesa lib32-mesa xf86-video-amdgpu vulkan-radeon lib32-vulkan-radeon libva-mesa-driver lib32-libva-mesa-driver mesa-vdpau lib32-mesa-vdpau`
21. 安装桌面环境
    1. 选择 GNOME 桌面环境：
       1. 安装：`sudo pacman -S gnome`
       2. 启用登陆管理器：`sudo systemctl enable gdm`
    2. 选择 KDE Plasma 桌面环境：
       1. 安装：`sudo pacman -S plasma dolphin konsole ark kwrite sddm`
       2. 启用登陆管理器：`sudo systemctl enable sddm`

    无论选择上述哪种桌面环境，都需要安装网络管理组件并启用：sudo pacman -S networkmanager && sudo systemctl enable NetworkManager
22. 安装网络相关组件，以在 tty 环境中也可以联网：`sudo pacman -S iwd dhcpcd`
23. 安装字体：`sudo pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji`
24. 安装 grub 启动引导器：`pacman -S grub efibootmgr`，随后执行 `grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=<NAME IN BIOS>`（名称不要带空格，没试过，想折腾可以试试），再生成 grub.cfg：`grub-mkconfig -o /boot/grub/grub.cfg` 即可。
25. 执行 `exit` 退出 chroot 环境，`umount -R /mnt` 取消所有挂载，`reboot` 重启系统，即可进入所配置的桌面环境。

## 后期配置

1. 使用 ArchLinuxCN 源并使用 yay：
    参照 [这里](https://mirrors.bfsu.edu.cn/help/archlinuxcn/)，按步骤添加 ArchLinuxCN 源，并 `sudo pacman -S yay` 安装 yay
2. 安装 Chrome：`yay -S google-chrome`

## KDE Plasma 桌面环境配置

1. 设置中文：打开系统设置，找到语言和区域，直接把语言改成简体中文并重启即可。
2. 为 SDDM 启动管理器配置中文：`sudo nano /usr/lib/systemd/system/sddm.service`，在 [Service] 标签下，添加 `Environment=LANG=zh_CN.UTF-8`，重启即可。
3. 强制使用 24 小时制：`yay -S qt5-base-24h`，使其替换掉自带的 qt5-base（KDE 6 时代可能不同）
4. 如果没有用户文件夹（家目录中下载、图片、视频、音乐那些），可以手动创建：`pacman -S xdg-user-dirs && xdg-user-dirs-update`，默认会创建英文（因为 LANG 参数是英文），需要指定语言可查阅 Arch Wiki
