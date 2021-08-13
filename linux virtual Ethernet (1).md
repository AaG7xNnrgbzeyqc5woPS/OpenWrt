
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
 ### 2.2.1 现在先创建 ns0,ns1,ns2,ns3,然后再删掉
 - ip netns list         //list 可以简写为 ls, l, li,lis
 - ls /var/run/netns
 - ip netns add ns0
 - ip netns delete ns4
 - ip -all netns delete //删除所有的 Network Namespace
 
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
### 2.2.2 正式创建 ns0
```
 $ ip netns add ns0    

# root @ OpenWrt in ~ [16:19:17] 
$ ip netns l      
ns0

# root @ OpenWrt in ~ [16:19:24] 
$ ip netns add ns0
Cannot create namespace file "/var/run/netns/ns0": File exists

# root @ OpenWrt in ~ [16:19:42] C:1
$ ls /var/run/netns 
ns0

# root @ OpenWrt in ~ [16:19:56] 
$ 
```
新创建的 Network Namespace 会出现在/var/run/netns/目录下。如果相同名字的 namespace 已经存在，命令会报Cannot create namespace file "/var/run/netns/ns0": File exists的错误。  

对于每个 Network Namespace 来说，它会有自己独立的网卡、路由表、ARP 表、iptables 等和网络相关的资源。

## 2.3 操作Network Namespace
  - ip命令提供了ip netns exec子命令可以在对应的 Network Namespace 中执行命令。

### 2.3.1 查看新创建 Network Namespace 的网卡信息
  - ❤️  **ip netns exec ns0 ip addr**
  - 💝 一个技巧：ip netns exec ns0 bash, 进入bash，这是在ns0下，可以再输入 ip addr,大大减少输入量，
  - 经过实验对比，的确所有的命令在ns0下执行。
```
  # root @ OpenWrt in ~ [16:48:35] 
$ ip netns exec ns0 bash
bash-5.1# ip addr
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: gre0@NONE: <NOARP> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/gre 0.0.0.0 brd 0.0.0.0
3: gretap0@NONE: <BROADCAST,MULTICAST> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
4: erspan0@NONE: <BROADCAST,MULTICAST> mtu 1464 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
bash-5.1# ip netns ls
ns0
bash-5.1# 

```
  
```
root @ OpenWrt in ~ [16:32:14] 
$ ip netns ls           
ns0

# root @ OpenWrt in ~ [16:32:20] 
$ ip netns exec ns0 ip addr
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: gre0@NONE: <NOARP> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/gre 0.0.0.0 brd 0.0.0.0
3: gretap0@NONE: <BROADCAST,MULTICAST> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
4: erspan0@NONE: <BROADCAST,MULTICAST> mtu 1464 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff

# root @ OpenWrt in ~ [16:32:30] 
$ 
```

💙我的openwrt是软路由，虚拟网卡较多，现在也不懂。当前是**的确**在ns0中，如果在host中，使用ip addr可以看到**更多**网卡和虚拟网卡信息
可以看到，新创建的Network Namespace中会默认创建一个lo回环网卡，此时网卡处于关闭状态。   
此时，尝试去 ping 该lo回环网卡，会提示Network is unreachable  
```
# root @ OpenWrt in ~ [16:54:13] 
$ ip netns exec ns0 bash
bash-5.1#  ip addr | grep lo
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
bash-5.1# ping 127.0.0.1
PING 127.0.0.1 (127.0.0.1): 56 data bytes
ping: sendto: Network unreachable
bash-5.1# exit
exit

# root @ OpenWrt in ~ [16:54:40] C:1
$ 


```

通过下面的命令启用lo回环网卡：   
**ip link set lo up**  
然后再次尝试去 ping 该lo回环网卡,可见已经可以ping 通了  
```
bash-5.1# ip addr | grep lo
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
bash-5.1# ip link set lo up
bash-5.1# ip addr | grep lo
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
bash-5.1# ping -c 4 127.0.0.1
PING 127.0.0.1 (127.0.0.1): 56 data bytes
64 bytes from 127.0.0.1: seq=0 ttl=64 time=0.162 ms
64 bytes from 127.0.0.1: seq=1 ttl=64 time=0.222 ms
64 bytes from 127.0.0.1: seq=2 ttl=64 time=0.197 ms
64 bytes from 127.0.0.1: seq=3 ttl=64 time=0.217 ms

--- 127.0.0.1 ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max = 0.162/0.199/0.222 ms
bash-5.1# 

```


  
## 2.4 在Network Namespace之间转移设备
我们可以在不同的 Network Namespace 之间转移设备（如veth）。由于一个设备只能属于一个 Network Namespace ，所以转移后在这个 Network Namespace 内就看不到这个设备了。
其中，veth设备属于可转移设备，而很多其它设备（如lo、vxlan、ppp、bridge等）是不可以转移的。

# 3. veth pair
veth pair 全称是 Virtual Ethernet Pair，是一个成对的端口，所有从这对端口一 端进入的数据包都将从另一端出来，反之也是一样。
引入veth pair是为了在不同的 Network Namespace 直接进行通信，利用它可以直接将两个 Network Namespace 连接起来。
整个veth的实现非常简单，有兴趣的读者可以参考源代码drivers/net/veth.c的实现。

## 3.1 创建veth pair



```
 root @ OpenWrt in ~ [17:36:54] C:1
$ ip addr | grep veth 
18: vethc44d271@if17: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 

# root @ OpenWrt in ~ [17:37:11] 
$ ip link add type veth

# root @ OpenWrt in ~ [17:40:46] 
$ ip addr | grep veth  
18: vethc44d271@if17: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
19: veth0@veth1: <BROADCAST,MULTICAST,M-DOWN> mtu 1500 qdisc noop state DOWN group default qlen 1000
20: veth1@veth0: <BROADCAST,MULTICAST,M-DOWN> mtu 1500 qdisc noop state DOWN group default qlen 1000

# root @ OpenWrt in ~ [17:41:06] 
$ 

```
可以看到，此时系统中新增了一对veth pair，将veth0和veth1两个虚拟网卡连接了起来，此时这对 veth pair 处于”未启用“状态（state DOWN）。
再看它们的名字：veth0@veth1， veth1@veth0，说明它们是一对   
如果我们想指定 veth pair 两个端点的名称，可以使用下面的命令：  
```
# root @ OpenWrt in ~ [17:41:06] 
$ ip link add vethfoo type veth peer name vethbar

# root @ OpenWrt in ~ [17:45:34] 
$ ip addr | grep veth                            
18: vethc44d271@if17: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
19: veth0@veth1: <BROADCAST,MULTICAST,M-DOWN> mtu 1500 qdisc noop state DOWN group default qlen 1000
20: veth1@veth0: <BROADCAST,MULTICAST,M-DOWN> mtu 1500 qdisc noop state DOWN group default qlen 1000
21: vethbar@vethfoo: <BROADCAST,MULTICAST,M-DOWN> mtu 1500 qdisc noop state DOWN group default qlen 1000
22: vethfoo@vethbar: <BROADCAST,MULTICAST,M-DOWN> mtu 1500 qdisc noop state DOWN group default qlen 1000

# root @ OpenWrt in ~ [17:45:37] 
$
```

## 3.2 实现Network Namespace间通信
  ### 3.3 建立 ns1
- 下面我们利用veth pair实现两个不同的 Network Namespace 之间的通信。刚才我们已经创建了一个名为ns0的 Network Namespace，下面再创建一个信息Network Namespace，命名为ns1
- 启动lo网卡，再显示可以见到回环ip地址:127.0.0.1, lo网卡的状态由 state DOWN 变成 state UNKNOWN

```
# root @ OpenWrt in ~ [17:50:05] C:130
$ ip netns add ns1

# root @ OpenWrt in ~ [17:54:12] C:1
$ ip netns list   
ns1
ns0

# root @ OpenWrt in ~ [17:55:08] C:1
$ ip netns exec ns1  ip addr | grep lo
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

# root @ OpenWrt in ~ [17:55:36] 
$ ip netns exec ns1  ip link set lo up

# root @ OpenWrt in ~ [17:56:13] 
$ ip netns exec ns1  ip addr | grep lo
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo

# root @ OpenWrt in ~ [17:56:33] 
$ 


```



## 3.3 veth查看对端

# 4. 网桥
## 4.1 网桥的工作原理
## 4.2 网桥的实现
## 4.3 brctl

# 5. iptables/netfilter

# 6. 总结
  
