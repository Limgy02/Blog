# 重编译 Android 内核应清除缓存

创建于 2021/09/20；编辑于 2024/04/15

---

在编译 lmi 的 Pixel Experience 系统时使用的是 Quantic 内核，这个内核在 2021 年的 7 月左右对 Demon000 OSS based FOD 进行了适配，然而这并不适配我使用的 displayfeature based FOD。

显然，选择退回到更改 FOD 方式之前版本的内核可以解决问题，**然而问题并没有解决**。

## 其实是缓存捣的鬼

因为 FOD 方式是由内核适配的，其中 DTBO 分区非常重要，因此必须清除编译内核以及 DTBO、DTB 分区的缓存，它们均存在于目录 `out/target/product/[product_name]/obj` 中。

分别是 `DTB_OBJ`, `DTBO_OBJ` 和 `KERNEL_OBJ` 文件夹，只要将其删除即可清除缓存。

之后再执行 `lunch aosp_[product_name]-userdebug` `mka bacon -jx`（`x` 为处理器核心数量）编译即可解决问题。
