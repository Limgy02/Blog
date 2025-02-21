# 原生系统微信后台问题

创建于 2022/05/21；编辑于 2024/04/15

---

最近 [Xiaomi SM8250 development](https://github.com/xiaomi-sm8250-devs) 将底包从 MIUI 12.5.x (Android 11) 升级到了 MIUI 13.x.x (Android 12)，本来以为是顺利的升级过程，但未曾想到升级后重刷却出现了烦人已久的微信后台问题：

* 微信从后台划掉就会被杀，立刻点回去会看到很长的加载界面
* 被杀后可能永远起不来了，然后收不到消息，目前而言，嗯，后果严重

## 分析

介于升级底包前，微信是**有一定几率**能够正常使用的（就是像流氓软件一样一直霸占后台，但至少可以用，不会丢消息），因此怀疑：

* 升级底包后破坏了某种系统功能，该功能会在开机后自动执行某些代码使微信保活。

有一天我打了个微信电话，发现微信通话过程中从后台划掉微信，并不会让微信被杀死，这意味着什么？

* 微信通话是**前台服务**，这个前台服务使微信不会被杀死。

最终怀疑那个被破坏的系统功能实际上是调用了某个微信前台服务，去实现某种功能，顺便将微信保活了（不排除本身目的就是保活）。

## 初步尝试

将手机 root，安装微信后**先不要启动**，尝试通过 adb 启动微信自身的服务，个人印象深刻的 CoreService 自然是第一个选择。

通过 MyAndroidTools 查看服务全称后，在 root 权限下执行 `am start-foreground-service -n com.tencent.mm/com.tencent.mm.booter.CoreService` 启动 CoreService

执行成功后即可在正在运行的服务（或缓存）中看到微信。

此时我启动微信，惊喜的发现划掉后台时微信就不会被杀死了。

## 系统整合

想到 sm8250-common 的设备树中有一个 XiaomiParts 中含有 BootCompletedReceiver，开机后启动微信的 CoreService，这是可行的，以下代码可在 Android 12 中编译并正常运行：

```java
Intent service = new Intent();
service.setClassName("com.tencent.mm", "com.tencent.mm.booter.CoreService");
context.startForegroundService(service);
```

之后就可以常规的向系统设置内添加开关等，成为一项新的系统功能了。
