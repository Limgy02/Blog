# 迁移 GPG 密钥

创建于 2025/02/22；编辑于 2025/02/22

---

可用于在不同操作系统之前迁移 GPG 密钥，或者用于备份密钥

起因：需要为 git 启用 GPG 签名；将 `.gnupg` 文件夹进行复制的方法突然不能用了，从 Windows 复制到 Linux 中一直提示找不到私钥，运行 `gpg --list-secret-keys` 无输出。于是使用导出再导入的方法解决问题。

## 查询 GPG 密钥

执行 `gpg --list-secret-keys --keyid-format LONG`：

```bash
/home/USERNAME/.gnupg/pubring.kbx
------------------------------
sec   rsa4096/XXXXXXXXXXXXXXX 2025-01-08 [SC]
      YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY
uid                 [ unknown] NAME (Personal key) <EMAIL_ADDRESS>
ssb   rsa4096/ZZZZZZZZZZZZZZZ 2025-01-08 [E]
```

## 导出公钥

执行 `gpg -a -o public-file.key --export YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY`

其中 `public-file.key` 是导出密钥的文件名，`YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY` 同查询时的输出。

## 导出私钥

执行 `gpg -a -o private-file.key --export-secret-keys YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY`

各部分意义同导出公钥，注意：
- 私钥和公钥导出都是 `YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY` 而**不是** `XXXXXXXXXXXXXXX` 或者 `ZZZZZZZZZZZZZZZ`
- 导出的私钥包括公钥信息
- 导出的私钥一定要妥善加密保管

## 在另一个操作系统上导入

执行 `gpg --import <FILENAME.key>` 即可。

---

参考

[导出并保存 GPG 公密钥](https://guzhongren.github.io/2021/04/%E5%AF%BC%E5%87%BA%E5%B9%B6%E4%BF%9D%E5%AD%98gpg%E5%85%AC%E5%AF%86%E9%92%A5/)