
# 学习教程:
    萌新入门之如何在桌面环境(VMware,Virtualbox)安装尝试软路由系统，桌面环境(VMware,Virtualbox)配置虚拟子网，
    上手尝试OpenWrt路由系统:
    https://www.youtube.com/watch?v=UbbZphew_o4&list=PLQXiKqDZ8qAMlLpOJcBUBmNXCBadPY8tD&index=3
  
# 1. 下载 image文件
      网盘地址:
      https://drive.google.com/drive/folders/1dqNUrMf9n7i3y1aSh68U5Yf44WQ3KCuh
      Lean Code X86_64/eSir SPP 精品小包 一级一级的找,最好找到文件
      openwrt-SPP-IPv4-v12.1-x86-64-combined-squashfs.img.gz  107306268
      下载后大小 107306268 字节
      sha1sum 文件名 
      md5sum 文件名
      同网盘上的校验和进行比较,Ok
      
# 2. 解压gz文件得到img文件,并更名 1201.img     
      
# 3. 转换文件从img格式到vdi格式
     VBoxManage convertfromraw 1201.img 1201.vdi 
     vboxmanage convertfromraw 1201.img 1201.vdi
      Converting from raw image file="1201.img" to file="1201.vdi"...
      Creating dynamic image with size 331874304 bytes (317MB)...
     Ok!

# 4. 创建虚拟机
      虚拟机的设置如下:
      1. 虚拟机名称: openwrt
      2. 操作系统类型: linux / other linux64
      3. 磁盘文件为: 使用已存在的磁盘文件. 然后添加上面的 1201.vdi 做为虚拟磁盘
      4. 创建虚拟,成功后.再点虚拟机配置
      5. cpu 1 core, memory 512M, 
      6. 设置虚拟机网卡, 网卡一为桥接模式,网卡二为 内部网络,网络名称 选择 intnet
      7. 启动虚拟 openwrt
      8. 启动完后,回车进入 系统
      9. 当前进入的是 root用户
      10. 使用passwd 设置root用户的密码
      11. nano /etc/config/network
          内容应该编辑成下面这样,无关的可以删除掉.减少干扰
         
        root@OpenWrt:~# cat /etc/config/network
        config interface 'loopback'
            option ifname 'lo'
            option proto 'static'
            option ipaddr '127.0.0.1'
            option netmask '255.0.0.0'

        config interface 'lan'
            option type 'bridge'
            option ifname 'eth1'
            option proto 'static'
            option ipaddr '192.168.5.1'
            option netmask '255.255.255.0'
            option ip6assign '64'

        config interface 'wan'
            option ifname 'eth0'
            option proto 'dhcp'
            option metric '64'
            option delegate '0'

        root@OpenWrt:~#
        
      12. poweroff
      13. 重启 openwrt 虚拟机
      14. 进入 虚拟机. 不用输入密码,不用担心, 这是控制台 
      15. ifconfig, ip address 查看wan口是否有ip地址
      16. 成功拿到ip地址Ok, 如果没有,请回到前面,看哪里有问题.
      
      17. 再建立一个测试虚拟机test, 我们使用 clone的方法
      18. 修改 test 虚拟机的网路配置, 附加到 内部网络,网络名 intnet.这个虚拟机只有一个网卡的.
      19. 启动 test 虚拟机
      20. 浏览器输入: http://192.168.5.1 
      21.  出现一个登录界面,输入 root用户的密码,就是最开始设置的密码(第10步)
      22.  现在已经进入 openwrt的LuCI网页控制台,
      23. 首先, 在 系统/系统/系统属性/语言和界面/主题 修改主题为: Bootstrap
      24. 右下角,点"保存&应用", 然后刷新网页
      25. 现在可以随意浏览网页,也可以访问外网.
      26. 测试成功!
      
      
      


      
      
      
