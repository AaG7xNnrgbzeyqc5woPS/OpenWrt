# 0. See:
- [Docker网络详解——原理篇](https://blog.csdn.net/meltsnow/article/details/94490994)
- [Docker系列04—Docker的网络模式详解](https://www.cnblogs.com/along21/p/10233746.html)
- [Docker的四种网络模式的详解](https://blog.csdn.net/ningyuxuan123/article/details/81840517)
- [Docker四种网络模式](https://www.jianshu.com/p/22a7032bb7bd)

# 1. 准备
-  先要学习下 Linux 的网络虚拟化技术，学习笔记如下：
- [Linux Virtual Ethernet (1. Virtual Ethernet Pair)](https://github.com/AaG7xNnrgbzeyqc5woPS/OpenWrt/blob/master/Linux%20Virtual%20Ethernet%20(1.%20Virtual%20Ethernet%20Pair).md)
- [Linux Virtual Ethernet (2. Bridge).md](https://github.com/AaG7xNnrgbzeyqc5woPS/OpenWrt/blob/master/Linux%20Virtual%20Ethernet%20(2.%20Bridge).md)
- [Linux Virtual Ethernet (3. veth test).md](https://github.com/AaG7xNnrgbzeyqc5woPS/OpenWrt/blob/master/Linux%20Virtual%20Ethernet%20(3.%20veth%20test).md)
- [Linux Virtual Ethernet (4. veth test2).md](https://github.com/AaG7xNnrgbzeyqc5woPS/OpenWrt/blob/master/Linux%20Virtual%20Ethernet%20(4.%20veth%20test2).md)
- [Linux Virtual Ethernet (5. bridge test).md](https://github.com/AaG7xNnrgbzeyqc5woPS/OpenWrt/blob/master/Linux%20Virtual%20Ethernet%20(5.%20bridge%20test).md)

# 2. Docker bridge 网络实验
  docker network ls
  ```
$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
b648fc8c2463   bridge    bridge    local
638da73ab9d4   host      host      local
e8be9fad5f7c   none      null      local
```

  bridge模式： 使用 --net=bridge 指定，默认设置。
  
## 2.1 

