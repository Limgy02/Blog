# 使用 Debian 11 构建 Pixel Experience 12

创建于 2022/04/20；编辑于 2024/04/15

---

Google 官方推荐使用 Ubuntu 构建 AOSP，但出于 Snap、Xorg 和 wayland 两座屎山、~~WSL 的 Ubuntu 图标太丑~~等各种原因我想逃离 Ubuntu，此文章针对在 WSL2 中使用 Debian 11（bullseye）编译 Pixel Experience 12 的关键过程进行总结。

## 安装所需依赖

根据 [搭建构建环境 | Android 开源项目](https://source.android.com/setup/build/initializing?hl=zh-cn) 的步骤，安装以下软件包：

`git-core gnupg flex bison build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 libncurses5 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig`

---

repo init 时可能出现找不到 python 的问题，因此需要安装 `python-is-python2`，如果后期 repo 要求使用 python3，可以安装 `python-is-python3`

后期编译可能提示找不到 openssl/?.h，因此需要安装 `libssl-dev`

后期编译可能提示找不到命令 rsync，因此需要安装 `rsync`

## 安装 Repo

在主目录执行 `curl https://storage.googleapis.com/git-repo-downloads/repo > ~/repo` 下载 repo，  
执行 `mv ~/repo ~/.bin/repo` 将 repo 移动到合适的目录，  
`nano ~/.bashrc` 在最后添加 `export PATH=~/.bin:$PATH` 将 ~/.bin 添加到环境变量，再执行 `source ~/.bashrc` 使更改生效，  
执行 `sudo chmod +x ~/.bin/repo` 赋予 repo 可执行权限。

可能需要重启终端，repo 就可以使用了。

## 修复一个编译 bug

后期编译可能遇到诸如 AssertionError: Failed to execute: out/soong/host/linux-x86/bin/resize2fs 的错误，只需要执行 `sudo ln -s /proc/self/mounts /etc/mtab` 创建一个链接即可。

## 分离工作区

不推荐直接在 WSL 的虚拟磁盘中编译，可以使用单独的虚拟磁盘。

> 不能直接在 Windows 所在磁盘分出单独的 ext4 格式分区，这在 WSL2 中无法挂载。

使用 Windows 自带的磁盘管理工具，创建一个符合需求大小（比如 300GB）的 vhdx 格式虚拟磁盘，创建完成后在磁盘管理中分离此虚拟磁盘（否则在挂载时会提示已占用）。

使用管理员执行 `wsl --mount --vhd "VHDX 文件绝对路径" --bare` 附加虚拟磁盘而不装载它。

在 Linux 系统中，使用 `lsblk` 参照磁盘大小和顺序确定虚拟磁盘位置，使用 `sudo mkfs -t ext4 /dev/sdx` 将虚拟磁盘格式化为 ext4 格式；随后将格式化后的磁盘挂载（在此不赘述），使用 `sudo chown 用户名 挂载点` 更改所有者（否则操作文件会有权限问题），挂载点文件夹即可当作普通文件夹使用了。

---

最后即可参照 [如何自己编译自定义 Android ROM](https://www.akr-developers.com/d/107) 进行编译。

参考
---

https://githubhot.com/repo/remote-android/redroid-doc/issues/111  
https://www.akr-developers.com/d/107  
https://source.android.com/setup/build/initializing?hl=zh-cn  
https://docs.microsoft.com/zh-cn/windows/wsl/wsl2-mount-disk
