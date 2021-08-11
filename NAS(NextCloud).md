# 0. See:
 - [nextcloud](https://hub.docker.com/_/nextcloud)
 - [Docker DOC (Use volumes)](https://docs.docker.com/storage/volumes/)
 - [openwrt下 docker使用](https://koolshare.cn/thread-180474-1-1.html)
  
 - [Nextcloud -wikipedia](https://en.wikipedia.org/wiki/Nextcloud)
 - [nextcloud main page - office web](https://nextcloud.com/)
 - [nextcloud install - office web](https://nextcloud.com/install/)

# 1. 安装NextCloud
## 1.1 新增NextCloud模板
 - 在浏览器中，登录 http://192.168.2.2:9000  进入 PorTainer 控制界面。
 - 在左侧菜单栏点击 App Templates，添加一个 NextCloud的新条目
 - 新增条目表单中请填写以下项目：
 - Title， Description，Platform，Categories，Registry，Image， Network，Port mapping，Volume mapping，Restart policy 
 - 特别是：
 - TiTle： nextcloud
 - Registry： DocerHub; 
 - Image：nextcloud:latest，
 - Port mapping：host 8080---> container 80 /tcp
 - Restart policy: unless stopped
 - 最后点 Create the Template 按钮，建立完成

## 1.2.修改模板 
- 在 App Templates 菜单，搜索 nextcloud
- 点击 搜索到的 nextcloud 模板
- 点击右边的update 键，开始修改，
- 完成后点 update template  提交。


## 1.3 根据模板启动 nextcloud容器
  - 点击主菜单 App Templates 菜单 
  - 搜索到的 nextcloud 模板
  - 点击 nextcloud 
  - 点击 Deploy template 部署模板
  - 稍等片刻提示容器生成
  - 点击 主菜单 containers 可以看到 nextcloud 容器已经生成
  - 在浏览器中输入 http://192.168.2.2:8080可以看到nextcloud的控制界面，
  - nextcloud 容器安装完成

# 2. 配置和使用 nextcloud
  - 第一次启动需要 配置 nextcloud, 首先是 输入 管理员名称与密码
  - 进入主界面后，可以配置 界面语言，还有地区
  - 新建用户，
  - 添加分组，缺省两个分组，admin(管理员) 和 所有人（everybody）
  - 浏览主要功能：dashboard,files, photos, activity

# 3. 配置 nextcloud 在windows上的客户端
  - 配置好浏览器翻墙
  - 在 windows 上登录  http://192.168.2.2:8080，（用普通用户登录）
  - 在菜单 files/setting /webDAV, 点最下面的链接，可以进入页面[Accessing Nextcloud files using WebDAV](https://docs.nextcloud.com/server/22/user_manual/en/files/access_webdav.html)
  - 根据这个帮助可以找到pc desktop 下载页面
  - The recommended way to synchronize a desktop PC with a Nextcloud server is by using Nextcloud/ownCloud sync clients.
  - 在 nextcloud 下载对应 客户端： https://nextcloud.com/install/#install-clients
  - 下载 windows 安装程序，安装
  - 启动 客户端 Nextcloud/ownCloud sync clients
  - 需要到 网页上进行确认，然后同步客户端就启动成功了
  - 同步客户端有配置的，我们使用虚拟文件夹，这样最节省本地硬盘
  - 经过测试,同步成功!

# 4. 配置 nextcloud 在 linux 上的客户端
   -  参考 上一条,配置windows客户端
   -  这里需要下载 linux客户端 appimage
   -  appimage 设置为 可执行属性,点击可以启动客户端
   -  其它同window类似,
   -  经过测试,同步成功!

# 5. 下一步任务:
  1. 整个nextcloud文件系统迁移到 大硬盘（raid1镜像，实际容量4T）上,当前在16G的SD卡上
  2. 整个甚至容器系统都迁移到 大硬盘上,images和容器都迁移过去,容量和可靠性大大提高，只是速度慢一点。
  3. nextcloud其它的内在功能的使用

# 6. 迁移 openwrt中的docker根目录
  1. docker 版本 20.10.7
  2. 原来的根目录：  /opt/docker
  3. 现在预备的根目录：/mnt/sda1/opt/docker
  4. 关闭 docker服务器 
  5. ssh命令行下使用命令：cp -r /opt/docker/* /mnt/sda1/opt/docker
  6. 更改原来的 /opt/docker目录为，/opt/docker_bak
  7. 设置新的docker根目录：/mnt/sda1/opt/docker
  8. 启动 docker服务器
  9. docker info 查看docker根目录，设置正确
  10. luCI界面查看docker 影像，容器都在
  11. 启动容器 portainer, nextcloud
  12. 登录 portainer 正常，操作也正常
  13. 登录 nextcloud 出现错误提示，放弃。删除nextcloud容器
  14. rm -r /opt/docker_bak 删除原来的根目录成功
  15. reboot 重启openwrt服务器
  16. luCI 正常
  17. portainer 也正常。
  18. 至此，迁移docker根目录基本成功

# 7. 再次部署 nextcloud 容器
  1. 多次部署不成功
  2. ❤️ **似乎打开 LuCI/配置/DockerMan settings/访问控制/允许访问的容器，增加 容器名“NextCloud“就可以啦**
  3. 这个选项下有注释：“	桥接网络下哪些容器可以访问，即使是不允许从接口访问，也要填写容器 ID 或名称 ”，不太理解
  4. nextcloud 内嵌的app下载也可以啦，连着下了两个都成功！
  5. 重建本地文件同步连接：需要先删除原先已经有的帐号，再建立一个。本地目录已经有了，就换个名字。
  6. **NextCloud 安装 内嵌app bookmarks,firefox也装对应插件，可以同步电脑和nextcloud上的 bookmarks

# 8. 下一步任务
   还有很多任务，才能将 nextcloud运行和维护好：
  1. Using an external database
  2. Running this image with docker-compose
  3. Make your Nextcloud available from the internet
  4. HTTPS - SSL encryption
  5. see: [nextcloud - official image](https://hub.docker.com/_/nextcloud)
  6. see: [Using the occ command](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/occ_command.html)

# 9. portainer/portainer-ce
  1. [portainer/portainer-ce image](https://hub.docker.com/r/portainer/portainer-ce)
  2. portainer/portainer ---This Repo is now deprecated, use portainer/portainer-ce instead
  3. 删除 portainer 容器, 删除所有的容器和images,volumes
  4. [portainer-ce install docs] https://documentation.portainer.io/v2.0/deploy/ceinstalldocker/
  5. Portainer is comprised of two elements, the Portainer Server, and the Portainer Agent. Both elements run as lightweight Docker containers on a Docker engine.
  6. By default, Portainer will expose the UI over port 9000 and expose a TCP tunnel server over port 8000. The latter is optional and is only required if you plan to use the Edge compute features with Edge agents.
  7. **Agent Versions**: Always match the agent version to Portainer Server version. i.e., while installing or upgrading to Portainer 2.6 make sure all the agents are also version 2.6.
  8. Portainer Server Deployment
     ``` 
      docker volume create portainer_data
      
      docker run \
            -d \
            -p 8000:8000 \
            -p 9000:9000 \
            --name=portainer \
            --restart=always \
            -v /var/run/docker.sock:/var/run/docker.sock \
            -v portainer_data:/data \
            portainer/portainer-ce
         
      docker run \
             -d \
             -p 9001:9001 \
             --name portainer_agent \
             --restart=always \
             -v /var/run/docker.sock:/var/run/docker.sock \
             -v /mnt/sda1/opt/docker/volumes:/var/lib/docker/volumes \
             portainer/agent
     ```
  9. 部署好了后，可以在浏览器上输入：  192.168.2.2:9000 登录 portainer 管理界面，设置管理员密码，进入系统后，有两种方式连接 到 portainer容器，一种是 local,一种是agent代理,部署这两个容器后，都支持的。都测试过的   
  10. 连接模式由 Endpoints 控制，删掉这个信息后，下次再登录，需要再次选择 Endpoints，这个试过了
  11. 通过 agent 可以在一个界面管理多个主机，也就是 Endpoints，local模式只能管理portainer容器所在主机
  12. 要管理其它的 Endpoints，可以在 Endpoints 界面增加新的 Endpoints
 
# 10. portainer-ce -- Hiding specific containers
  ## 10.1 例子
   portainer-ce 容器在容器管理器中可见，容易被误删除，一直有这个担忧，如果隐藏起来或者设置为只读，无法删除就好了，刚好看到这个功能，不知道行不行，试试看
  ```
Portainer allows you to hide containers with a specific label by using the -l flag.

For example, take a container started with the label owner=acme (note that this is an example label, you can define your own labels):

docker run -d --label owner=acme nginx

To hide this container, simply add the -l owner=acme option on the CLI when starting Portainer:

docker run -d -p 9000:9000 -p 8000:8000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer-ce -l owner=acme

Note that the -l flag can be repeated multiple times to specify multiple labels:

docker run -d -p 9000:9000 -p 8000:8000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer-ce -l owner=acme -l service=secret



  ``` 
  ## 10.2 修改后的语句
  加上标签后的语句如下：
   ``` 
      docker volume create portainer_data
      
      docker run \
            -d \
            -p 8000:8000 \
            -p 9000:9000 \
            --name=portainer \
            --restart=always \
            -v /var/run/docker.sock:/var/run/docker.sock \
            -v portainer_data:/data \
            portainer/portainer-ce \
            -l portainer
         
      docker run \
             -d \
             -p 9001:9001 \
             --name portainer_agent \
             --restart=always \
             -v /var/run/docker.sock:/var/run/docker.sock \
             -v /mnt/sda1/opt/docker/volumes:/var/lib/docker/volumes \
             portainer/agent \
             -l portainer
     ```
    
    
    
  ## 10.3. 第一次尝试
    尝试下，没有出现预期的效果。再看看例子，原来是理解错误，以为在容器启动命令后面直接加个标签就能隐藏。
    实际情况是，在别的容器A启动时候加标签，然后在 portainer 容器启动的时候，指明这个标签，容器A在 portainer 控制界面被隐藏。这是我现在的理解，试试看
    **尝试的时候，发现一个严重的问题** 必须首先建立 portainer 容器，地址为 172.16.0.2，才可以从笔记本电脑访问它（192.168.2.2:9000）,如果先建立 两个mariaDB 容器，导致 portainer ip为 172.16.0.4,就不能访问了。试了几次，都这样。看来是网络没有配置好，也不会配置，以后还得研究下！

    

     
        
            
            
            


