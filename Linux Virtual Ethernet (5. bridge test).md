Linux Virtual Ethernet (5. bridge test).md

# 0. See: 
  [https://www.cnblogs.com/wxiaote/articles/10879327.html](https://www.cnblogs.com/wxiaote/articles/10879327.html)
  
# 1. 创建三个网络空间 net0,net1,bridge，并连接
```
  reboot openwrt
  ssh openwrt
  
  ip netns ls
  ip netns add net0
  ip netns add net1
  ip netns add bridge
  ip netns ls
  
  ip link add type veth
  ip link set dev veth0 name net0-bridge netns net0
  ip link set dev veth1 name bridge-net0 netns bridge
  
  ip link add type veth
  ip link set dev veth0 name net1-bridge netns net1
  ip link set dev veth1 name bridge-net1 netns bridge
  
  ip netns exec net0   ip addr
  ip netns exec net1   ip addr
  ip netns exec bridge   ip addr
  
  ```
  经过检查，可以见连接已经建立好，net0-bridge-net1
  特别是 ip netns exec bridge   ip addr， 可以看到到另外两个空间的链接
  ip地址未配置，设备未上线
  
# 2. 在bridge空间中建立并设置br设备

```
 ip netns exec bridge bash
 bash-5.1# 
 brctl show
 brctl addbr br
 brctl show
 
 ip link set dev br up
 ip link set dev bridge-net0 up
 ip link set dev bridge-net1 up
 
 brctl addif br bridge-net0
 brctl addif br bridge-net1
 brctl show
 brctl showmacs br
 
```
在空间bridge中建立 名字为br的桥接设备
上线 br,两个网卡
➡️ 将两个网卡连接到br上，做为br的两个接口,这两个接口不需要配置ip地址， br是链路层设备，只需要mac地址就可以工作

brctr show 显示桥接设备, 可以看到桥接设备的名字和拥有的端口
brctl showmacs br 显示br桥接端口的mac地址

## 2.2 ☑️ 桥接bridge总结：
- 桥接bridge就是链路层的设备，在链路层工作，端口只有mac地址，没有ip地址。
- 桥接就逻辑上同switch一样的，交换一个链路层的网络数据，
- 但是区别是，桥接，网桥（bridge）可以跨越不同的通讯介质，和链路层的通讯协议，比如可以连接有线网络和无线网络(wifi).


# 3. 配置net0,net1中的两个网卡
```
  ip netns exec net0 ip link set dev net0-bridge up
  ip netns exec net1 ip link set dev net1-bridge up
  
  ip netns exec net0 ip address add 10.0.1.1/24 dev net0-bridge
  ip netns exec net1 ip address add 10.0.1.2/24 dev net1-bridge
  
  ip netns exec net0 ip address
  ip netns exec net1 ip address
  
```
启动 net0,net1中的两个网卡，并且添加ip地址

# 4. 测试 net0 和 net1的联通性
```
  ip netns exec net0 ping -c 3 10.0.1.2
  ip netns exec net1 ping -c 3 10.0.1.1
  
```
```
  
# root @ OpenWrt in ~ [11:41:52] 
$ ip netns exec net0 ping -c 3 10.0.1.2
PING 10.0.1.2 (10.0.1.2): 56 data bytes
64 bytes from 10.0.1.2: seq=0 ttl=64 time=0.174 ms
64 bytes from 10.0.1.2: seq=1 ttl=64 time=0.180 ms
64 bytes from 10.0.1.2: seq=2 ttl=64 time=0.272 ms

--- 10.0.1.2 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.174/0.208/0.272 ms

# root @ OpenWrt in ~ [11:44:35] 
$ ip netns exec net1 ping -c 3 10.0.1.1
PING 10.0.1.1 (10.0.1.1): 56 data bytes
64 bytes from 10.0.1.1: seq=0 ttl=64 time=0.122 ms
64 bytes from 10.0.1.1: seq=1 ttl=64 time=0.254 ms
64 bytes from 10.0.1.1: seq=2 ttl=64 time=0.255 ms

--- 10.0.1.1 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.122/0.210/0.255 ms

# root @ OpenWrt in ~ [11:44:50] 
$ 


```
🍒 测试完美成功！

```
# root @ OpenWrt in ~ [11:44:50] 
$ ip netns exec net0 ping -c 3 fe80::10ed:1eff:fe43:6298            
PING fe80::10ed:1eff:fe43:6298 (fe80::10ed:1eff:fe43:6298): 56 data bytes
64 bytes from fe80::10ed:1eff:fe43:6298: seq=0 ttl=64 time=0.229 ms
64 bytes from fe80::10ed:1eff:fe43:6298: seq=1 ttl=64 time=0.319 ms
64 bytes from fe80::10ed:1eff:fe43:6298: seq=2 ttl=64 time=0.313 ms

--- fe80::10ed:1eff:fe43:6298 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.229/0.287/0.319 ms

# root @ OpenWrt in ~ [11:46:46] 
$ ip netns exec net1 ping -c 3 fe80::18b1:8fff:fea2:14a2           
PING fe80::18b1:8fff:fea2:14a2 (fe80::18b1:8fff:fea2:14a2): 56 data bytes
64 bytes from fe80::18b1:8fff:fea2:14a2: seq=0 ttl=64 time=0.165 ms
64 bytes from fe80::18b1:8fff:fea2:14a2: seq=1 ttl=64 time=0.309 ms
64 bytes from fe80::18b1:8fff:fea2:14a2: seq=2 ttl=64 time=0.324 ms

--- fe80::18b1:8fff:fea2:14a2 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.165/0.266/0.324 ms

# root @ OpenWrt in ~ [11:47:52] 
$ 
```
🍒 ipv6测试下，也完全成功！看来是支持ipv6的

# 5. 自己再玩玩

 # 5.1 任务
   接着上面的例子，删除bridge空间的连线，再加两个桥，br2，br3，这样就有三个桥，把这三个桥连接起来，看看net0, net1能否通
 
 # 5.2 bridge 操作
 ```
 ip netns exec bridge bash
 bash-5.1# 
 brctl show
 ip addr
 
 
 
 ```

