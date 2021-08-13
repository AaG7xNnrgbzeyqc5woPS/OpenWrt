# 0. See:
 - 🖐️ [详解：Linux网络虚拟化技术](https://www.cnblogs.com/wxiaote/articles/10879327.html)

# 1. 创建虚拟网络环境(newtwork namespace) net0,net1
- ip netns add net0
- ip netns show

```
# root @ OpenWrt in ~ [22:37:22] 
$ ip netns                 
ns1 (id: 2)
ns0 (id: 1)

# root @ OpenWrt in ~ [22:44:31] 
$ ip netns add net0     

# root @ OpenWrt in ~ [22:44:41] 
$ ip netns add net1

# root @ OpenWrt in ~ [22:44:46] 
$ ip netns show    
net1
net0
ns1 (id: 2)
ns0 (id: 1)

# root @ OpenWrt in ~ [22:44:54] 
$ 

```

# 2. 进入虚拟网络环境(network namespace) net0
使用命令

      ip netns exec net0 `command`
      
我们可以在 net0 虚拟环境中运行任何命令。

```
# root @ OpenWrt in ~ [22:44:54] 
$ ip netns exec net0 bash
bash-5.1# ip addr
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: gre0@NONE: <NOARP> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/gre 0.0.0.0 brd 0.0.0.0
3: gretap0@NONE: <BROADCAST,MULTICAST> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
4: erspan0@NONE: <BROADCAST,MULTICAST> mtu 1464 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
bash-5.1# 
```
这样我们可以在新的网络环境中打开一个shell，可以看到，新的网络环境里面~~只有一个~~lo设备，并且这个lo设备与外面的lo设备是不同的，之间不能互相通讯。

# 3. 查看 net0,net1
```
# root @ OpenWrt in ~ [23:05:26] 
$ ip netns exec net0 ip addr
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: gre0@NONE: <NOARP> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/gre 0.0.0.0 brd 0.0.0.0
3: gretap0@NONE: <BROADCAST,MULTICAST> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
4: erspan0@NONE: <BROADCAST,MULTICAST> mtu 1464 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff

# root @ OpenWrt in ~ [23:05:41] 
$ ip netns exec net0 ip addr | grep lo
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

# root @ OpenWrt in ~ [23:05:49] 
$ ip netns exec net1 ip addr | grep lo
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

# root @ OpenWrt in ~ [23:05:59] 
$ ip netns exec net1 ip addr          
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: gre0@NONE: <NOARP> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/gre 0.0.0.0 brd 0.0.0.0
3: gretap0@NONE: <BROADCAST,MULTICAST> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
4: erspan0@NONE: <BROADCAST,MULTICAST> mtu 1464 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff

# root @ OpenWrt in ~ [23:07:47] 
$ 

```
