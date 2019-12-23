
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
      
# 2. 转换文件从img格式到vdi格式
     VBoxManage convertfromraw 1201.img 1201.vdi 
     vboxmanage convertfromraw 1201.img 1201.vdi
      Converting from raw image file="1201.img" to file="1201.vdi"...
      Creating dynamic image with size 331874304 bytes (317MB)...
     Ok!

# 
