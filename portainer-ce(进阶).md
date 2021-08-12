# 0. 背景
  👉portainer 已经用了起来，这几天发现几个问题：
  - portainer 已经分成社区版和企业版，原来的发布不再更新，我们选用免费的社区版portainer/portainer-ce，升级成功！
  - 想将 portainer-ce中的数据同容器分开存放，存放在独立的volume中，这样便于维护数据，容器崩溃，删除都不影响数据，不用再配置
  - portainer-ce 容器最好能设置成hide或者readonly,避免误删除
  - portainer-ce 有一个 -l选项，可以将指定的容器 隐藏起来，想试试看，重要的常用容器隐藏起来，更安全，避免误操作，也减少容器列表的长度
  - 希望portainer-ce 可以不作为第一个容器启动，这样更方便管理，可以现在必须作为地一个容器才能正常打开容器web管理界面，可能同网络的配置有关系
  - 这样又牵涉出来docker的网络的配置问题和openwrt的网络管理问题，这个问题另外写一篇文章来讨论研究


# 1. portainer/portainer-ce

    portainer/portainer-ce image
    portainer/portainer ---This Repo is now deprecated, use portainer/portainer-ce instead
    删除 portainer 容器, 删除所有的容器和images,volumes
    [portainer-ce install docs] https://documentation.portainer.io/v2.0/deploy/ceinstalldocker/
    Portainer is comprised of two elements, the Portainer Server, and the Portainer Agent. Both elements run as lightweight Docker containers on a Docker engine.
    By default, Portainer will expose the UI over port 9000 and expose a TCP tunnel server over port 8000. The latter is optional and is only required if you plan to use the Edge compute features with Edge agents.
    Agent Versions: Always match the agent version to Portainer Server version. i.e., while installing or upgrading to Portainer 2.6 make sure all the agents are also version 2.6.
    Portainer Server Deployment

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

    部署好了后，可以在浏览器上输入： 192.168.2.2:9000 登录 portainer 管理界面，设置管理员密码，进入系统后，有两种方式连接 到 portainer容器，一种是 local,一种是agent代理,部署这两个容器后，都支持的。都测试过的
    连接模式由 Endpoints 控制，删掉这个信息后，下次再登录，需要再次选择 Endpoints，这个试过了
    通过 agent 可以在一个界面管理多个主机，也就是 Endpoints，local模式只能管理portainer容器所在主机
    要管理其它的 Endpoints，可以在 Endpoints 界面增加新的 Endpoints

# 2. portainer-ce -- Hiding specific containers
## 2.1 例子

portainer-ce 容器在容器管理器中可见，容易被误删除，一直有这个担忧，如果隐藏起来或者设置为只读，无法删除就好了，刚好看到这个功能，不知道行不行，试试看

Portainer allows you to hide containers with a specific label by using the -l flag.

For example, take a container started with the label owner=acme (note that this is an example label, you can define your own labels):

docker run -d --label owner=acme nginx

To hide this container, simply add the -l owner=acme option on the CLI when starting Portainer:

docker run -d -p 9000:9000 -p 8000:8000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer-ce -l owner=acme

Note that the -l flag can be repeated multiple times to specify multiple labels:

docker run -d -p 9000:9000 -p 8000:8000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer-ce -l owner=acme -l service=secret

## 2.2 修改后的语句

加上标签后的语句如下：

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

## 2.3. 第一次尝试

尝试下，没有出现预期的效果。再看看例子，原来是理解错误，以为在容器启动命令后面直接加个标签就能隐藏。 实际情况是，在别的容器A启动时候加标签，然后在 portainer 容器启动的时候，指明这个标签，容器A在 portainer 控制界面被隐藏。这是我现在的理解，试试看

broken_heart 尝试的时候，发现一个严重的网络问题
必须首先建立 portainer 容器，地址为 172.17.0.2，才可以从笔记本电脑访问它（192.168.2.2:9000）,如果先建立 两个mariaDB 容器，导致 portainer ip为 172.17.0.4,就不能访问了。试了几次，都这样。看来是网络没有配置好，也不会配置，以后还得研究下！
