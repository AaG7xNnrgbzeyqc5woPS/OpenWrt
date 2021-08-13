
# 0. See:
 - [Linux虚拟网络技术](https://www.cnblogs.com/gaoyanbing/p/14006972.html)
 - [详解：Linux网络虚拟化技术](https://www.cnblogs.com/wxiaote/articles/10879327.html)

# 1. 背景
  docker等虚拟化容器技术，都是建立在 linux的虚拟化技术之上的，openwrt也是在管理linux的网络，要用好这些产品，必须深入了解linux的网络技术以及网络虚拟化技术
  这篇文章是基础技术的学习和练习笔记，先掌握虚拟化最基本的技术。要真正的学会，少不了练习和实践
  
# 2. Network Namespace
  Network Namespace 是 Linux 内核提供的功能，是实现网络虚拟化的重要功能，它能创建多个隔离的网络空间，它们有独自网络栈信息。不管是虚拟机还是容器，运行的时候仿佛自己都在独立的网络中。而且不同Network Namespace的资源相互不可见，彼此之间无法通信。
  
## 2.1 ip netns命令
  - 可以借助ip netns命令来完成对 Network Namespace 的各种操作。ip netns命令来自于iproute2安装包，一般系统会默认安装，如果没有的话，请自行安装。
  - 注意：ip netns命令修改网络配置时需要 sudo 权限。
  - 可以通过ip netns命令完成对Network Namespace 的相关操作，可以通过ip netns help查看命令帮助信息
  - 💖 注意：ip 命令建立的 namespace, 网卡，网桥等，重启后会消失，已经验证过了，昨天在openwrt上建立的，刚才ip netns ls 看了下还在， reboot后就没有了。
  - 🖤 如果需要永久保存, 需要写到配置文件里面，具体那个配置文件还不清楚，可能是 /etc/config/network
```
$ ip netns help
Usage:	ip netns list
	ip netns add NAME
	ip netns attach NAME PID
	ip netns set NAME NETNSID
	ip [-all] netns delete [NAME]
	ip netns identify [PID]
	ip netns pids NAME
	ip [-all] netns exec [NAME] cmd ...
	ip netns monitor
	ip netns list-id [target-nsid POSITIVE-INT] [nsid POSITIVE-INT]
NETNSID := auto | POSITIVE-INT
```

默认情况下，Linux系统中是没有任何 Network Namespace的，所以ip netns list命令不会返回任何信息。  
💙 /var/run/netns目录也不存在  
```
# root @ OpenWrt in ~ [16:05:08] C:255
$ ip netns list

# root @ OpenWrt in ~ [16:07:11] 
$ ls /var/run/netns     
ls: /var/run/netns: No such file or directory

```
  
## 2.2 创建Network Namespace
现在先创建 ns0,ns1,ns2,ns3,然后在删掉，展示下几个命令
```
 root @ OpenWrt in ~ [16:05:08] C:255
$ ip netns list

# root @ OpenWrt in ~ [16:07:11] 
$ ls /var/run/netns     
ls: /var/run/netns: No such file or directory

# root @ OpenWrt in ~ [16:07:21] C:1
$ ip netns list

# root @ OpenWrt in ~ [16:09:27] 
$ ip netns add ns0 ns1

# root @ OpenWrt in ~ [16:12:03] 
$ ip netns list       
ns0

# root @ OpenWrt in ~ [16:12:11] 
$ ip netns add ns1    

# root @ OpenWrt in ~ [16:12:25] 
$ ip netns ls     
ns1
ns0

# root @ OpenWrt in ~ [16:12:35] 
$ ip netns add ns3

# root @ OpenWrt in ~ [16:12:42] 
$ ip netns add ns4

# root @ OpenWrt in ~ [16:12:45] 
$ ip netns ls     
ns4
ns3
ns1
ns0

# root @ OpenWrt in ~ [16:12:56] 
$ ip netns delete ns4

# root @ OpenWrt in ~ [16:13:33] 
$ ip netns ls        
ns3
ns1
ns0

# root @ OpenWrt in ~ [16:13:38] 
$ ip -all netns delete

# root @ OpenWrt in ~ [16:13:58] 
$ ip netns list       

# root @ OpenWrt in ~ [16:14:05] 
$ ip netns ls  

# root @ OpenWrt in ~ [16:14:09] 
$ 

```


## 2.3 操作Network Namespace

## 2.4 在Network Namespace之间转移设备

# 3. veth pair
## 3.1 创建veth pair
## 3.2 实现Network Namespace间通信
## 3.3 veth查看对端

# 4. 网桥
## 4.1 网桥的工作原理
## 4.2 网桥的实现
## 4.3 brctl

# 5. iptables/netfilter

# 6. 总结
  
