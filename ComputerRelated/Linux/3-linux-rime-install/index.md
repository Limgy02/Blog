# Linux 下 rime 输入法安装与配置

创建于 2025/02/03；编辑于 2025/02/03

---

## 安装

### Debian 系

fcitx5 平台：`sudo apt install fcitx5-rime rime-data-double-pinyin`

ibus 平台：`sudo apt install ibus-rime rime-data-double-pinyin`

### Arch 系

fcitx5 平台：`sudo pacman -S fcitx5-im fcitx5-rime rime-double-pinyin`

~~ibus 平台：暂时还没试过~~

## 启用输入法

### KDE Plasma 桌面环境 + fcitx5 平台

安装完成后注销重新登录，任务栏右下角托盘处会出现输入法图标，右键，取消选中 Only Show Current Language，搜索 Rime，将其添加到左侧列表，完成输入法添加。

### GNOME 桌面环境 + ibus 平台

安装完成后注销重新登录，打开系统设置→键盘→添加输入源→汉语，找到安装的 rime，将其添加即可。

## 配置

1. 首次部署完成后，在 scheme 中添加自然码双拼方案：
   1. fcitx5 平台：`nano ~/.local/share/fcitx5/rime/default.custom.yaml`
   2. ibus 平台：`nano ~/.config/ibus/rime/default.custom.yaml`

    将下列内容添加到上述文件
    ```
    patch:
      schema_list:
        - schema: double_pinyin
    ```
2. 设置主题（仅 fcitx5 平台）：
    1. 如果是 Debian 系，需要从 [这里](https://gitlab.com/scratch-er/fcitx5-breeze) 下载主题并根据项目 README 进行安装即可，如果出现找不到 inkscape 类似错误，手动 `sudo apt install inkscape` 安装即可。
    2. 如果是 Arch 系，则自带主题，无需安装。

    安装完主题后，在右下角任务栏托盘处右击输入法→Configure→Addons→Classic User Interface 之后选择安装的主题 / KDE Plasma（实验性）即可。
3. 调整简体中文：输入一些文字，之后「按下键盘 F4」→「中/半/汉」→「漢字 → 汉字」即可。
4. 修改后选项个数：
    仍然编辑 `default.custom.yaml`（注意区分是 fcitx5 还是 ibus 平台，路径不同），添加如下所示最后两行：
    ```
    patch:
      schema_list:
        - schema: double_pinyin
    menu:
      page_size: 7
    ```
5. 设置候选项横排（仅 ibus 平台）：`nano ./config/ibus/rime/build/ibus_rime.yaml`，将其中 horizonal 项改为 true 即可。
6. 取消双拼自动展开：
    1. fcitx5 平台：`nano ~/.local/share/fcitx5/rime/double_pinyin.custom.yaml`
    2. ibus 平台：`nano ~/.config/ibus/rime/double_pinyin.custom.yaml`

    向其中添加以下内容：
    ```
    patch:
      translator/preedit_format: []
    ```

配置全部完成后，右键输入法菜单重新部署即可。