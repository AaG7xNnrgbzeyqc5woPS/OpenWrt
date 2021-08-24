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
 
```
在空间bridge中建立 名字为br的桥接设备
上线 br,两个网卡
➡️ 将两个网卡连接到br上，做为br的两个接口,这两个接口不需要配置ip地址， br是链路层设备，只需要mac地址就可以工作
brctr show 显示桥接设备, 可以看到桥接设备的名字和拥有的端口


# 
