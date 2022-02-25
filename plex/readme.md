# see:
- [Install Plex Media Server as a Docker Container on OpenWrt](https://wiki.seeedstudio.com/OpenWrt-Plex-Media-Server-on-Docker/)
- [Official Docker container for Plex Media Server](https://github.com/plexinc/pms-docker)
- [Plex (company) wikipedia](https://en.wikipedia.org/wiki/Plex_(company))
- [plex main page](https://www.plex.tv/)

# Using docker-compose on ARM devices

The provided docker-compose templates use the plexinc/pms-docker image which is the amd64 build and won't work on ARM devices.

To use docker-compose with ARM devices, you must first build one of the ARM images locally.

docker build -t plexinc/pms-docker:latest -f Dockerfile.armv7 . # or arm64

Then you can docker-compose up.

---------------------------
## 下载 源码 
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
11. 

