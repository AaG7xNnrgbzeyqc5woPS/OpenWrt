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
  
# 2. 

