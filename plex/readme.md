# 0. see:
- [Install Plex Media Server as a Docker Container on OpenWrt](https://wiki.seeedstudio.com/OpenWrt-Plex-Media-Server-on-Docker/)
- [Official Docker container for Plex Media Server](https://github.com/plexinc/pms-docker)
- [Plex (company) wikipedia](https://en.wikipedia.org/wiki/Plex_(company))
- [plex main page](https://www.plex.tv/)
- [使用Plex & Docker搭建自己的媒体服务器](https://blog.hinatarin.com/2021/04/21/set-up-your-own-media-server-with-plex-and-docker/)

# 1. Using docker-compose on ARM devices

The provided docker-compose templates use the plexinc/pms-docker image which is the amd64 build and won't work on ARM devices.

To use docker-compose with ARM devices, you must first build one of the ARM images locally.

docker build -t plexinc/pms-docker:latest -f Dockerfile.armv7 . # or arm64

Then you can docker-compose up.

---------------------------
## 3. 下载 源码 编译安装
https://github.com/plexinc/pms-docker
https://github.com/plexinc/pms-docker
1. 使用  git clone 失败，最后直接下载 zip文件 pms-docker-master.zip
2. 然后使用 scp pms-docker-master.zip openwrt:. 拷贝到 openwrt服务器上
3. unzip pms-docker-master.zip 解压
4. cd pms-docker-master 进入pms-docker-master 目录
5. 使用 docker build -t plexinc/pms-docker:latest -f Dockerfile.arm64 . 开始建立 docker image文件
6. 建立成功，使用 docker images 查看，应该有名字为 plexinc/pms-docker 的images
7. 启动 一下 docker-compose up
8. 提示没有配置文件 
9. cp docker-compose-bridge.yml.template compose.yml
10. nano compose.yml 修改配置文件
11. 启动 docker 成功
12. 在客户机上可以访问 http://ip:32400，观看视频
13. 研究半天，无法访问管理页面 http://ip:32400/web, 没有权限
14. 看来是 docker 桥接模式的问题，网络没有配置好。


## 4. How to Mount SMB Share on Linux
  - [How to Mount SMB Share on Linux](https://linoxide.com/howto-mount-smb-filesystem-using-etcfstab/)
  - 使用 SMB 共享，访问plex媒体库

## 5. linuxserver docker 镜像 
 - See：[使用Plex & Docker搭建自己的媒体服务器](https://blog.hinatarin.com/2021/04/21/set-up-your-own-media-server-with-plex-and-docker/)
 - See: [Linux Docker 快速搭建 Plex](https://vamev.com/plex/plex-docker.html)
 - ❤️参考上文，搭建成功，可以访问 plex 的管理页面 http://ip:32400/web
 - 注册 Plex 账号,  plex 和 Jellyfin 不同的地方在于，要通过 Plex 官方账号进行登录，即使是自己搭建的服务端。所以，首先去注册一个账号, Plex 官网：https://www.plex.tv/sign-up
 - 申请 PLEX_CLAIM： https://www.plex.tv/claim/           **This claim code will expire in 4 minutes**

```
  ssh openwrt           #登陆 树梅派4 软路由
  mkdir plex2
  cd plex2
  
  docker pull linuxserver/plex  #  ok
  docker images        # ok
  touch compose.yml
  nano compose.yml
  文件内容如下：
  version: "2.1"
  services:
  plex:
    image: linuxserver/plex
    container_name: plex
    environment:
      - PUID=0
      - PGID=0
      - VERSION=docker
      - PLEX_CLAIM=claim-G48utTfwFtUFsxqv_ndp
    volumes:
      - /mnt/sda1/opt/plex2/config:/config
      - /mnt/sda1/opt/plex2/media:/data
      - /mnt/sda1/opt/plex2/temp:/transcode
    restart: unless-stopped
    mem_limit: 700m
    memswap_limit: 2000m
    ports:
      - 32400:32400

  保存退出 nano 
  docker-compose up -d
  http://ip:32400/web
  
  Ok！
  注意，如果无法链接，请重新启动服务器。有时候openwrt 的 docker 容器网络不能立即生效。
  
```

