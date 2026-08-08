# 局域网卡顿

创建于 2026/08/08；编辑于 2026/08/08

---

使用 deskflow 穿透鼠标指针和键盘，或者使用 ssh 链接局域网内的其他机器，有时候会遇到间歇性卡顿的问题，现象是差不多 500ms 就卡一小下，用起来非常恶心，此问题在多处被报道过：

---

[Deskflow Workarounds - macOS: Laggy cursor](https://github.com/deskflow/deskflow/wiki/Workarounds#macos-laggy-cursor)

这篇文章推荐你使用 `sudo ifconfig awdl0 down` 暂时解决，实测 awdl0 会自动重启，效果不佳，而且可能会影响 Airdrop 等功能的使用。

---

[Wi-Fi Jitter, Erratic Ping, Latency Due To AWDL (AirDrop/AirPlay)](https://forums.macrumors.com/threads/wi-fi-jitter-erratic-ping-latency-due-to-awdl-airdrop-airplay.2373916/)

这篇文章的评论区有人提到可以将 5G Wi-Fi 的信道设置成 149 来解决问题，此方法测试有效，在路由器设置完信道后记得重启。

## 原因

Apple 拥有一个叫做 AWDL（Apple Wireless Direct Link）的私有协议，可以让苹果设备之间绕过路由器进行通讯，这也是使用 AirDrop 等功能的基础。

此功能本质上是在网络芯片上额外建立的网络，并且本网络对于信道有偏好：

- 2.4GHz Wi-Fi：倾向于信道 6
- 5GHz Wi-Fi：倾向于信道 44、149，并且更倾向于 149

因此如果路由器发射信号的信道不是上述提到的信道，就会导致 Mac 的网络芯片不断切换信道造成延迟，这也就是局域网内卡顿的原因。
