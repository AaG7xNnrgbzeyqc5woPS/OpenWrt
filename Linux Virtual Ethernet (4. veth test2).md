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
   
 ```





