# 0. 参考：
  - [Nextcloud Official Image (dockerhub)](https://hub.docker.com/_/nextcloud)
  - [Use volumes (docker)](https://docs.docker.com/storage/volumes/)
  - [openwrt下 docker使用](https://koolshare.cn/thread-180474-1-1.html)
  - Docker 网络
  - [Openwrt 下 Docker 网络食用方法](https://zhuanlan.zhihu.com/p/113664215)
  - [手撕Docker网络（1） —— 从0搭建Linux虚拟网络](https://zhuanlan.zhihu.com/p/199298498)
  - [手撕Docker网络（2） —— bridge模式拆解实例分析](https://zhuanlan.zhihu.com/p/206512720)
  - [用 Portainer.io 来监控和管理 Docker 容器（1） | Linux 中国](https://zhuanlan.zhihu.com/p/256469146)
  - [用 Portainer.io 来监控和管理 Docker 容器（1）](https://linux.cn/article-12634-1.html)
  - [Monitor and Manage Docker Containers with Portainer.io (GUI tool) – Part-2](https://www.linuxtechi.com/monitor-manage-docker-containers-portainer-io-part-2/)

# 1. Docker 命令回顾
  参考：
   - [Docker run reference](https://docs.docker.com/engine/reference/run/)
   - [docker run](https://docs.docker.com/engine/reference/commandline/run/)
  
  - 首先使用 ssh 登录  openwrt
  - docker
  - docker help
  - docker --version
  - docker version
  - docker info
  - docker images
  - docker ps
  - docker volume ls
  - docker volume create my-vol 
  - docker volume help
  - docker volume inspect my-vol
  - docker run hello-world
  - docker pull hello-world
  - docker pull ubuntu
  - docker run -d -it  ubuntu
  - docker run --help
  - docker run -d -it --name=my-ubuntu ubuntu
  - docker run -d -it --name my-linux ubuntu
  - docker ps
  - docker run -d -it --name
  - docker run -d -it --restart allway  --name my-linux3 ubuntu

# 2. luCI/Docker
 教程： [openwrt下 docker使用](https://koolshare.cn/thread-180474-1-1.html)   
 - LuCI中的Docker管理插件叫做：DockerMan
 - luCI/Docker/配置中，可以更改docker根目录
 - docker根目录缺省在： /opt/docker/
 - docker 守护程序默认在此路径监听客户端连接： unix:///var/run/docker.sock
 - docker 默认桥接网络：172.17.0.1/16，docker容器分配的地址依次为： 172.17.0.n/16， n=2,3,4,5...255
 - docker 默认桥接网络的网关ip地址为：172.17.0.1 

**更改docker根目录方法二：
```
     root@OpenWrt:~# vim /etc/docker/daemon.json
    {
        "data-root": "/volume/docker/",       #改成我们挂载的其他分区
        "log-level": "warn"
    }
    
    改完之后，记得重启服务。
/etc/init.d/dockerd restart
或者通过菜单-系统-启动项-dockerd来重启
```    
## 安装容器 portainer.ion
这里教大家安装一个更好用的docker管理前端:portainer
1. 拉取 portainer/portainer 镜像文件
在openwrt菜单->docker->images下输入portainer/portainer，然后pull拉取镜像

2. 新建容器 
创建 docker 容器 ：
openwrt菜单->docker->容器 下点 “添加”，出来一个配置表格
具体参数很多，懂docker run 命令，就比较简单
这里重要设置的4点:
    容器名字(自定义): container-name-01 
    使用的镜像 portainer/portainer
    映射的目录(docker的sock位置)   /var/run/docker.sock:/var/run/docker.sock
    映射的端口：9000->9000
填写完后点“提交"

3. 运行容器 portainer/portainer
返回到容器列表，在这里就能开启容器了，列表里能看到容器状态和监听的端口

4. 开启防火墙
  如果有需要可以开启防火墙 9000/tcp 端口，我不知道什么原因，不开启居然也可以连通
  
5. portainer 控制界面
  在浏览器中输入， http://openwrt.lan:9000 ，http://openwrt.local:9000, 也可以输入路由器的IP地址，可以打开控制界面
 首次进来会让我们设置管理员账号密码，docker的管理路径，我们选择local
  进来之后就能使用docker相关的各种功能，我们以后就在这里管理和部署应用了
    

# 3. Portainer.io
教程： [openwrt下 docker使用](https://koolshare.cn/thread-180474-1-1.html)  
进入 Portainer， 最方便的就是模板功能 APP Templates， 就是 docker run 命令相关的参数，都可以模板化，快速建立容易应用
Stacks 是  docker-compose 的应用，可以快速部署多个容器应用

    
