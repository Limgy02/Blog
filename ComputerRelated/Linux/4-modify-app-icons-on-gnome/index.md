# GNOME 更换软件图标

创建于 2025/02/21；编辑于 2025/02/22

---

## 复制 .desktop 文件

为了防止软件更新后，我们自定义的软件图标失效，需要将软件对应的 `.desktop` 文件从系统目录复制到用户目录；用户目录的同名文件优先级高于系统目录中的文件。

`.desktop` 文件的路径：

- 系统目录中：`/usr/share/applications/`
- 用户目录中：`/.local/share/applications`

`.desktop` 文件的名称根据软件名称自行查找即可。

复制命令（以 QQ 为例）：`sudo cp /usr/share/applications/qq.desktop /.local/share/applications/`

## 修改 .desktop 文件

找到文件中的 `Icon=` 行，如下：

```shell
[Desktop Entry]
...
Icon=/usr/share/icons/hicolor/512x512/apps/qq.png
...
```

将 `Icon=` 后面的路径改为你要使用的图标路径，我这里使用了 WhiteSur 主题，主题放在了 `~/.icons/` 中，格式至少可以是 `.png` 和 `.svg`：

```shell
# 不要使用 ~ 代替 /home/USERNAME
Icon=/home/USERNAME/.icons/WhiteSur/apps/scalable/qq.svg
```

修改完成后，注销重新登录即可生效。
