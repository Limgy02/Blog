# 在 Windows OOBE 阶段使用代理服务器

创建于 2024/04/20；编辑于 2025/02/21

---

中国移动的大内网安装 Windows 简直是折磨，账号登不上，驱动不下载，甚至收不到 Windows 更新，无法在微软商店下载应用，受不了一点。

## 配置服务器

首先需要一个能在局域网内提供代理服务器功能的软件，v2RayN、Quantumult X 之类的都可以，以 Quantumult X 为例：

点击右下角小风车 → 划到最下面点击其他设置 → 开启「HTTP 代理服务器」功能；此功能右侧的说明可以看到服务器地址，比如 192.168.1.102:8899（下面均依此举例，请自行替换）。

如果使用其他软件，需要先想办法把 IP 查出来，可以从路由器界面看，加上代理软件内设定的端口即可。

建议开启全局代理模式。

## 在 OOBE 阶段启用代理

进入 OOBE 的网络连接界面后，先不要连接网络，按下 Shift + F10 打开命令提示符，输入如下 3 条命令执行

```powershell
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings" /v ProxyEnable /t REG_DWORD /d 1 /f
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings" /v ProxyServer /d "192.168.0.102:8899" /f
netsh winhttp set proxy 192.168.0.102:8899 ""
```

随后在电脑上连接网络，可以同时在代理软件上查看日志，判断是否成功连接。

### 简化的配置方法

后来我让 ChatGPT 帮我写了一段脚本用以自动执行这堆东西（毕竟太长还没处复制太容易打错了），存为 `.bat` 文件即可。

```cmd
@echo off

:: Notify user input IP address and port
set /p ip=Please input IP address:
set /p port=Please input port:

:: Combine
set proxy=%ip%:%port%

:: Enable Proxy
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings" /v ProxyEnable /t REG_DWORD /d 1 /f
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings" /v ProxyServer /d "%proxy%" /f
netsh winhttp set proxy %proxy% ""

:: Notify success
echo Proxy successfully set to %proxy%
```

## 后记

注册表的设置可以维持到 OOBE 结束前，进入桌面后需要重新设置；winhttp 的设置会始终生效，如果后期不使用代理或者地址发生变化，需要重新设置，否则部分应用会无法联网。

取消 winhttp 代理：`netsh winhttp reset proxy`

---
参考

[CMD设置代理 注册表设置IE代理](https://blog.csdn.net/ilqgffvramusm2864/article/details/108105129)  
[怎么设置win10自动更新走SS？](https://github.com/shadowsocks/shadowsocks-windows/issues/1741#issuecomment-374812288)
