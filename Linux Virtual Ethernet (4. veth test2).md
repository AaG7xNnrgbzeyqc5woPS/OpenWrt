# 0. See：
 [详解：Linux网络虚拟化技术](https://www.cnblogs.com/wxiaote/articles/10879327.html)
 
# 1. 序言
  继续学习，还是以 开头的文章为基础，再次学习linux的虚拟化，并且多做练习。这对理解linux网络虚拟化大有帮助
 
# 2. Clear 网络手工配置 
  ssh openwrt
  ip netns ls
  ...
  reboot
  重启 树梅派，清除上次手工做的网络实验，有一个干净的环境
  ssh openwrt
  ip netns ls
  清除完毕
  
# 3. 实验目标
  目标： 建立两个 network namespace, 并且链接起来，能互相ping 通，使用 veth pair链接网络空间

# 4. create net0,net1 and up lo
  
  ```
  ip netns ls
  ip netns add net0
  ip netns add net1
  ip netns ls
  
  ip netns exec net0 ip addr 
  ip netns exec net1 ip addr 
  
  ip netns exec net0    ip link set lo up
  ip netns exec net1    ip link set lo up
  
  ```
  
 # 5. create veth pair (virtual ethernet pair) 
 ```
   ip link add type veth
   ip addr | grep veth
   ip addr
   
 ```
 
 # 6. move veth0 to net0, veth1 to net1
 ```
 ip link set veth0 netns net0
 ip link set veth1 netns net1
 
 ip netns exec net0   ip addr
 ip netns exec net1   ip addr
 
 ip netns exec net0    ip link set veth0 up 
 ip netns exec net1    ip link set veth1 up
 
 ip netns exec net0   ip addr
 ip netns exec net1   ip addr
 
 ip netns exec net0   ip address add 10.0.1.1/24 dev veth0
 ip netns exec net1   ip address add 10.0.1.2/24 dev veth1
 
 ip netns exec net0   ip addr
 ip netns exec net1   ip addr
 ```
 移动veth0到net0,veth1到net1
 并且启动这两个网卡，网卡已经添加了ipv4地址，自动分配了ipv6地址，启动都成功！
 
 # 7. 测试net0和net1联通性
 ```
ip netns exec net0  ping -c 3 10.0.1.2
PING 10.0.1.2 (10.0.1.2): 56 data bytes
64 bytes from 10.0.1.2: seq=0 ttl=64 time=0.141 ms
64 bytes from 10.0.1.2: seq=1 ttl=64 time=0.223 ms
64 bytes from 10.0.1.2: seq=2 ttl=64 time=0.205 ms

--- 10.0.1.2 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.141/0.189/0.223 ms

```
 
```
$ ip netns exec net1  ping -c 3 10.0.1.1
PING 10.0.1.1 (10.0.1.1): 56 data bytes
64 bytes from 10.0.1.1: seq=0 ttl=64 time=0.107 ms
64 bytes from 10.0.1.1: seq=1 ttl=64 time=0.216 ms
64 bytes from 10.0.1.1: seq=2 ttl=64 time=0.242 ms

--- 10.0.1.1 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.107/0.188/0.242 ms
 
 ```
 可见IPv4双向都是通的
 💔ipv6反复实验，都没有通，可能不支持ip6吧
 
 # 8. veth pair测试完毕！





