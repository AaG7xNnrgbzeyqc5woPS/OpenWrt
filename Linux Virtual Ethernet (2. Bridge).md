# 1. 网桥

veth pair打破了 Network Namespace 的限制，实现了不同 Network Namespace 之间的通信。但veth pair有一个明显的缺陷，就是只能实现两个网络接口之间的通信。
如果我们想实现多个网络接口之间的通信，就可以使用下面介绍的网桥（Bridge）技术。
简单来说，网桥就是把一台机器上的若干个网络接口“连接”起来。其结果是，其中一个网口收到的报文会被复制给其他网口并发送出去。以使得网口之间的报文能够互相转发。

## 1.1 网桥的工作原理

网桥对报文的转发基于MAC地址。网桥能够解析收发的报文，读取目标MAC地址的信息，和自己记录的MAC表结合，来决策报文的转发目标网口。

为了实现这些功能，网桥会学习源MAC地址，在转发报文时，网桥只需要向特定的网口进行转发，从而避免不必要的网络交互。

如果它遇到一个自己从未学习到的地址，就无法知道这个报文应该向哪个网口转发，就将报文广播给所有的网口（报文来源的网口除外）。

## 1.2 网桥的实现

Linux内核是通过一个虚拟的网桥设备（Net Device）来实现桥接的。这个虚拟设备可以绑定若干个以太网接口设备，从而将它们桥接起来。如下图所示：

## 1.3 网桥的位置

如上图所示，网桥设备 br0 绑定了 eth0 和 eth1。

对于网络协议栈的上层来说，只看得到 br0，上层协议栈需要发送的报文被送到 br0，网桥设备的处理代码判断报文该被转发到 eth0 还是 eth1，或者两者皆转发；反过来，从eth0 或 eth1 接收到的报文被提交给网桥的处理代码，在这里会判断报文应该被转发、丢弃还是提交到协议栈上层。

而有时eth0、eth1 也可能会作为报文的源地址或目的地址，直接参与报文的发送与接收，从而绕过网桥。

# 2. brctl

和网桥有关的操作可以使用命令 brctl，这个命令来自 bridge-utils 这个包。

# 2.1 创建网桥
brctl addbr br0 
```

# root @ OpenWrt in ~ [21:18:46] 
$ brctl show     
bridge name	bridge id		STP enabled	interfaces
docker0		8000.0242045fd3c6	no		vethc44d271
br-lan		7fff.dca632575555	no		eth0

# root @ OpenWrt in ~ [21:19:06] 
$ brctl addbr br0

# root @ OpenWrt in ~ [21:19:29] 
$ brctl show     
bridge name	bridge id		STP enabled	interfaces
br0		8000.000000000000	no
docker0		8000.0242045fd3c6	no		vethc44d271
br-lan		7fff.dca632575555	no		eth0


```

# 2.2 删除网桥
brctl delbr br0
```
# root @ OpenWrt in ~ [21:19:29] 
$ brctl show     
bridge name	bridge id		STP enabled	interfaces
br0		8000.000000000000	no
docker0		8000.0242045fd3c6	no		vethc44d271
br-lan		7fff.dca632575555	no		eth0

# root @ OpenWrt in ~ [21:19:40] 
$ brctl delbr br0

# root @ OpenWrt in ~ [21:20:07] 
$ brctl show     
bridge name	bridge id		STP enabled	interfaces
docker0		8000.0242045fd3c6	no		vethc44d271
br-lan		7fff.dca632575555	no		eth0

# root @ OpenWrt in ~ [21:20:11] 
$ 

```

# 2.3 绑定网口
    建立一个逻辑网段之后，我们还需要为这个网段分配特定的端口。在Linux 中，一个端口实际上就是一个物理或虚拟网卡。而每个网卡的名称则分别为eth0 ，eth1 ，eth2 。我们需要把每个网卡一一和br0 这个网段联系起来，作为br0 中的一个端口。

# 让eth0 成为br0 的一个端口
brctl addif br0 eth0  
# 让eth1 成为br0 的一个端口
brctl addif br0 eth1
# 让eth2 成为br0 的一个端口
brctl addif br0 eth2

# 2.4 绑定虚拟网口veth0
💝  eth0网卡绑定不成功，于是到ns0 network namespace中做测试，绑定br0和veth0,成功！
- ip netns exec ns0 bash              //进入ns0.bash 中，
- ip addr                            //显示地址 veth0
- brctl addbr br0                    //在ns0 中建立bridge br0
- brctl addif br0 veth0              //绑定 br0和veth0
- brctl show                         //可以看到绑定成功


```
$ ip netns exec ns0 bash              
bash-5.1# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: gre0@NONE: <NOARP> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/gre 0.0.0.0 brd 0.0.0.0
3: gretap0@NONE: <BROADCAST,MULTICAST> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
4: erspan0@NONE: <BROADCAST,MULTICAST> mtu 1464 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
19: veth0@if20: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 46:8b:99:3c:e5:cc brd ff:ff:ff:ff:ff:ff link-netns ns1
    inet 10.0.1.1/24 scope global veth0
       valid_lft forever preferred_lft forever
    inet6 fe80::448b:99ff:fe3c:e5cc/64 scope link 
       valid_lft forever preferred_lft forever

bash-5.1# brctl addbr br0
bash-5.1# brctl show
bridge name	bridge id		STP enabled	interfaces
br0		8000.000000000000	no

bash-5.1# brctl addif br0 veth0
bash-5.1# brctl show
bridge name	bridge id		STP enabled	interfaces
br0		8000.468b993ce5cc	no		veth0

bash-5.1# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: gre0@NONE: <NOARP> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/gre 0.0.0.0 brd 0.0.0.0
3: gretap0@NONE: <BROADCAST,MULTICAST> mtu 1476 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
4: erspan0@NONE: <BROADCAST,MULTICAST> mtu 1464 qdisc noop state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
5: br0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 46:8b:99:3c:e5:cc brd ff:ff:ff:ff:ff:ff
19: veth0@if20: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br0 state UP group default qlen 1000
    link/ether 46:8b:99:3c:e5:cc brd ff:ff:ff:ff:ff:ff link-netns ns1
    inet 10.0.1.1/24 scope global veth0
       valid_lft forever preferred_lft forever
    inet6 fe80::448b:99ff:fe3c:e5cc/64 scope link 
       valid_lft forever preferred_lft forever
bash-5.1# 

```

❤️ brctl show   //可以看到已经有  bridge id 和 interfaces（veth0），可以绑定成功！
```
bash-5.1# brctl show
bridge name	   bridge id		    STP enabled  	  interfaces
br0		       8000.468b993ce5cc	no	   	          veth0

```
