# 0. See: 
- [SMB / Samba share overview (Windows file sharing)](https://openwrt.org/docs/guide-user/services/nas/samba_configuration)
- [Samba (smb)](https://openwrt.org/docs/guide-user/services/nas/samba)

# 1. OpenWrt配置文件
 -  /etc/config/samba 这个保存通用配置参数
 -  /etc/samba/smb.conf.template 这个模板文件保存不常用参数
 - samba 服务重启命令： /etc/init.d/samba restart
 - 通过这两个配置文件，可以实现samba的两种访问方式：Share level access / User level access
 -  ❤️ It is strongly recommended that you use LuCI to establish the initial configuration and then edit the template file (/etc/samba/smb.conf.template) via LuCI Edit Template tab or from the shell as needed. 

实验，查看两个配置文件和samba shell 接口文件：

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
1. 在openwrt服务器上新增一个用户  
2. 在samba 系统里新增 samba用户和口令：  
  > smbpasswd -a newuser  
      ❤️注意：这里的用户和口令是samba专用的，为了简单可以跟客户端的用户名和口令一致。  
3. 编辑smb.conf.template 接受用户使用空口令  
  > Edit /etc/samba/smb.conf.template and add "null passwords = yes"
4. 新增共享项目   
  > Then add a share to /etc/config/samba. Make sure that guest ok is set to no   
  > option 'guest_ok' 'no'   
5. 现在客户端可以通过 \\ip-adress-openwrt\name-of-share 访问共享，并且需要正确的用户名和密码 

# 4. 实践
1. openwrt 上设置一个 samba 共享，名称为depot
2. 使用 user模式，必须输入密码，用户名king，口令：****
3. root 用户禁用
4. ❤️遇到问题，depot文件夹是root用户建立的，需要开放权限# chmod 777 depot
5. 测试，功能正常。需要正确用户名和密码才能登陆,root不能登陆，用king登陆后，可以建立文件夹。 ssh登陆后，可以看到正确的建立了文件夹，用户属于king。

