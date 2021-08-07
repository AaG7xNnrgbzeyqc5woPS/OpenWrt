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
