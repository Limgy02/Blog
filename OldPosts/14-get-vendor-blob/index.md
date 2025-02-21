# 手动生成一个 proprietary(vendor) blob

创建于 2022/06/13；编辑于 2024/04/15

---

一直很好奇一个问题，Device Tree 里面的 proprietary-files.txt、setup-makefiles.sh、extract-files.sh 是怎么来的，怎么用的，毕竟删掉它们也不影响编译

直接给出结论吧，这三个文件都是为了生成 vendor 服务的，而 vendor 也就是所谓的 proprietary(vendor) blob，如果一直从诸如 Pixel Experience 的 [vendor blobs](https://gitlab.pixelexperience.org/android/vendor-blobs) 中获取 vendor 树，则很容易不清楚这些文件的作用，也不清楚 vendor 中的内容是如何生成的。

恰巧，最近 alioth 多个设备树配套的 vendor 都无法开机，因此就来自己生成一个 vendor blob 吧

## 执行逻辑

还是要先说一下基本的执行流程

以为 alioth 编译为例，在设备树 device_xiaomi_alioth 下执行 extract-files.sh 文件。

extract-files.sh 会寻找设备树目录下的 proprietary-files.txt 文件，并根据此文件内一行一行列出的文件，从 adb 连接的设备（或指定的文件夹）中提取它们，进行整合，最终把提取出的文件放入 `vendor/xiaomi/alioth` 中，整个过程是全自动的，执行后即可生成 vendor 树。

而 setup-makefiles.sh 则是 extract-files.sh 执行过程中引用的一个「模块」。

## 准备材料

vendor blob 一般是直接从手机中，或从 MIUI 官方发布的刷机包中提取。很明显，把主力机用来开发的我不可能每次提取都刷一遍最新的 MIUI（这会清除数据的啊喂），所以在此处选择从 MIUI 官方提供的线刷包中提取 vendor blob.

那需要准备的材料就是一个线刷包，下载完成后把里面的 super.img 拉出来。

## 解包和挂载

如果没有，先安装工具 `sudo apt install simg2img`

执行 `simg2img super.img super.img_raw` 将 Android sparse image 转换为 raw image

你需要编译过一次 Android 源码，在 `out/host/linux-x86/bin` 中找到 `lpunpack`，在此目录执行 `./lpunpack super.img_raw ~/super` 将 super.img 的 raw 格式解包到 ~/super 目录，应该能在该目录中发现 system.img product.img 等文件（A/B 或 VAB 分区为 system_a.img product_a.img 等）。

以 VAB 分区为例，先挂载 system，选一个空文件夹，比如挂载到 ~/super/system/，在 ~/super/ 目录下执行： `sudo mount -o ro system_a.img system/` 完成挂载后应该能在 system 目录下看到 Android system 分区内的文件。

以此类推，执行 `sudo mount -o ro vendor_a.img system/vendor/` 等依次挂载所有分区直至完成。

## 开始生成

cd 到设备树目录，执行 `./extract-files.sh ~/super/system/` 即可从挂载的系统目录中提取 vendor blob 了。

过程中会输出日志，很可能出现红色的找不到文件信息，这时可自行判断此文件是否真的需要，如果不需要就将其从 proprietary-files.txt 删除重新生成；需要的话就从其他地方找到这个文件自行补充到 vendor blobs 的对应位置中，不能缺失而不管，否则无法通过编译。

> 如果找不到的文件太多，就要怀疑用的刷机包版本啊，proprietary-files 是否合适一类的  
> 总之这不是很正常的现象

## 尚存在的问题

还是没有弄清楚 proprietary-files.txt 是如何生成的，难道是人一行一行手写的吗？

还是不清楚为什么「别人制作的 vendor blob 」在 Pixel Experience 中不开机，而其它系统却可以开机。

---
参考

[Android super.img 镜像解包](https://www.jianshu.com/p/aa567960156c)  
[Android super.img 镜像解包（你怎么和上面那个一模一样）](https://blog.csdn.net/u012045061/article/details/119383397)  
[Working with proprietary blobs](https://wiki.lineageos.org/proprietary_blobs)  
[Extracting proprietary blobs from LineageOS zip files](https://wiki.lineageos.org/extracting_blobs_from_zips)
