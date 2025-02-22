# bug 合集

创建于 2025/02/22；编辑于 2025/02/22

---

## Chrome 和 electron 内核应用下 Text Selector 鼠标指针变白导致不可见

创建 DWORD (32-bit) 值：`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Dwm\OverlayTestMode`，设为 5 即可解决。

解决方案来自于 [这里](https://answers.microsoft.com/en-us/msoffice/forum/all/my-cursor-keeps-turning-white-in-microsoft-word/fea524dc-9a20-489a-a04e-8c32d4f44800?page=8)
