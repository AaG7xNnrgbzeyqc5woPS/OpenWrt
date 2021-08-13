
# 0. See:
 - [Linux虚拟网络技术](https://www.cnblogs.com/gaoyanbing/p/14006972.html)
 - [详解：Linux网络虚拟化技术](https://www.cnblogs.com/wxiaote/articles/10879327.html)

# 1. 背景
  docker等虚拟化容器技术，都是建立在 linux的虚拟化技术之上的，openwrt也是在管理linux的网络，要用好这些产品，必须深入了解linux的网络技术以及网络虚拟化技术
  这篇文章是基础技术的学习和练习笔记，先掌握虚拟化最基本的技术。要真正的学会，少不了练习和实践
  
# 2. Network Namespace
  Network Namespace 是 Linux 内核提供的功能，是实现网络虚拟化的重要功能，它能创建多个隔离的网络空间，它们有独自网络栈信息。不管是虚拟机还是容器，运行的时候仿佛自己都在独立的网络中。而且不同Network Namespace的资源相互不可见，彼此之间无法通信。
  
## 2.1 ip netns命令
## 2.2 创建Network Namespace
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
  
