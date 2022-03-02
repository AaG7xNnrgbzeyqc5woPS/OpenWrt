# 0. See: 
- [SMB / Samba share overview (Windows file sharing)](https://openwrt.org/docs/guide-user/services/nas/samba_configuration)
- [Samba (smb)](https://openwrt.org/docs/guide-user/services/nas/samba)

# 1. OpenWrt配置文件
 - 1. /etc/config/samba 这个保存通用配置参数
 - 2. /etc/samba/smb.conf.template 这个模板文件保存不常用参数
 - 3. samba 服务重启命令： /etc/init.d/samba restart
 - 4. 通过这两个配置文件，可以实现samba的两种访问方式：Share level access / User level access

实验，查看两个配置文件和samba shell 接口文件
```
  ssh openwrt
  cat /etc/config/samba
  cat /etc/samba/smb.conf.template
  cat /etc/init.d/samba
```

# 2. Share level access
 1. 修改 /etc/samba/smb.conf.template 文件，确保
     > security = share
 2. 在/etc/config/samba新增一个共享项目，并且确保：
    > option 'guest_ok' 'yes'
 3. 现在在客户机可以访问这个共享了，可以使用任何用户名和密码：
   > \\ip-adress-openwrt\name-of-share
  
# 3. User level access

