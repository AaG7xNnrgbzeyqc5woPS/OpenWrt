# 1. 参考： 
- [OpenWrt路由器打造小型NAS服务：外接存储并实现内网共享](https://baijiahao.baidu.com/s?id=1698202288081364222&wfr=spider&for=pc)
- [智能路由器-OpenWRT 系列五 （NAS-SMB家庭共享）](https://www.cnblogs.com/wizju/p/6923625.html)
- [Linux -- Samba之客户端访问（Linux和windows）](https://blog.51cto.com/u_57388/1552978)
- [linux命令行访问samba共享文件夹](https://blog.csdn.net/linmingan/article/details/85245138)



 # 2. 前提

## 2.1  确定你已经连接、并挂载了U盘/移动硬盘。

## 2.2 配置防火墙策略，保证137 、138 、139、445 端口是打开的。
```
vi /etc/config/firewall

增加以下内容

config 'rule'
        option 'src' 'lan'
        option 'proto' 'udp'
        option 'dest_port' '137-138'
        option 'target' 'ACCEPT'

config 'rule'
        option 'src' 'lan'
        option 'proto' 'tcp'
        option 'dest_port' '139'
        option 'target' 'ACCEPT'

config 'rule'
        option 'src' 'lan'
        option 'proto' 'tcp'
        option 'dest_port' '445'
        option 'target' 'ACCEPT'



保存并退出
```


# 3. OpenWRT 使用 SMB共享
  - 菜单路径：LuCI\网络存储\网络共享\Samba
  - 基本设置，新增 共享目录： 共享名称：raid1; 目录：/mnt/sda1，允许用户：root， 只读：不勾选，可浏览：勾选，允许匿名用户：勾选，文件权限：0777，目录权限：0777， 保存并且应用
  - 编辑模板，注释这一行：# invalid users = root

# 4. 客户端
   ## 4.1. linux
     sudo mount -t cifs //ip地址/path/to/share path/to/mount
     测试不成功，总说命令参数错误，也可能配置的时候还有些不对。
     
   ## 4.2 windows
     网络映射：
     net use z: //192.168.2.2/raid1
     已经测试成功，可以顺利的浏览目录，并且读写
    




