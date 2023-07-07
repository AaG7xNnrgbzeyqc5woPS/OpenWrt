# See:
- [Dropbear key-based authentication](https://openwrt.org/docs/guide-user/security/dropbear.public-key.auth)
- [dropbearkey](https://manpages.ubuntu.com/manpages/xenial/en/man1/dropbearkey.1.html)
- [dropbearkey (1) - Linux Manuals](https://www.systutorials.com/docs/linux/man/1-dropbearkey/)

# 使用 gropbear 生成私钥文件
- gropbear 是不同于openssh的 密钥对生成文件软件，用法同openssh基本类似
```
dropbearkey -f ~/.ssh/id_dropbear -t ed25519
```

- 查看对应的**公钥**
```
dropbearkey -f ~/.ssh/id_dropbear -y
```

# 编辑 目标ssh服务器上的authorized_keys文件
使用下述命令编辑 authorized_keys 文件，增加 dropbearkey 公钥
```
nano ~/.ssh/authorized_keys
```

# 使用 ssh root@ip 登陆
现在不需要输入密码，直接登陆
