# 0. see:
[Overview of Docker Compose](https://docs.docker.com/compose/)

# 1. 第一课： 
[Get started with Docker Compose](https://docs.docker.com/compose/gettingstarted/)

## 1.1 尝试1
按照 教程的指导，在 openwrt服务器上建立几个文件，在编译的时候，有文件下载不下来。经过尝试没有搞定。   
将文件打包 

       tar c -f test1.tar  composetest/*   
       
经过测试用这个命令也行： 

       tar c -f test1.tar  composetest  

把文件传到外网服务器上，使用：

    scp openwrt:test1.tar .  
拷贝到本地

    scp test1.tat trojan:.  
再拷贝到外网服务器，其实上面两个命令合成一个：   
~~scp openwrt:test1.tar trojan:.~~   
//测试，这个不行，trojan解析不了，找不到ip地址，如果直接用ip地址就可以，以前试过的

##  1.2 尝试2
 - 拷贝上来后，发现 docker-compose 没有安装
```
apt update
apt upgrade
apt automove
reboot

apt install docker-compose
docker-compose version

```
  - 使用docker-compose编译发现 docker-compose 版本不对
  - 可能docker-compose不是最新的
  - 将教程中 docker-compose.yml文件的最开始的版本 3.9 修改成 3.3
  - 使用 docker-compose up 编译启动成功

## 1.3 尝试3 客户端
  浏览器输入 服务器的http://ip:5000
  一个干扰是,直接把地址换成 https://ip:5000,
  刷行一次，提示不安全警告，通过就是
  上面两个干扰都是 “https Everwhere”插件的作用，不要惊慌
  最后顺利显示期望的结果，刷新还会计数：
  
     Hello World! I have been seen 5 times. 

  Good job!
  
  
# 2. 总结
  实验还算顺利，得到想要结果。
  ❤️ docker images 建立工作还是在外网服务器上建立方便。真有必要再拷贝下来在openwrt上运行！
  
  
# 3. 尾声
  ## 3.1 服务器上的docker-compose 升级一下
  [docker-compose install](https://docs.docker.com/compose/install/)
  使用的是linux安装方案，删除老的 docker-compose文件，docker-compose全部就一个二进制文件。
  
  ```
    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  ```
 > To install a different version of Compose, substitute 1.29.2 with the version of Compose you want to use.

     sudo chmod +x /usr/local/bin/docker-compose
     
 > Note: If the command docker-compose fails after installation, check your path. You can also create a symbolic link to /usr/bin or any other directory in your path.
  
  制作一个软链接：

      sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
     
  老的 docker-compose 藏在 /usr/bin/ 目录下，使用命令: 
  
     find / -name "docker-compose"可以找到。
     
  安装好后，查看版本
      
  > docker-compose version
      
```
root@localhost:~/composetest# docker-compose version
docker-compose version 1.29.2, build 5becea4c
docker-py version: 5.0.0
CPython version: 3.7.10
OpenSSL version: OpenSSL 1.1.0l  10 Sep 2019
```
      
  ## 3.2 删除全部dokcer images and containers
     为了让 docker-compose 重建images和containers, 把不要的容器和images全部删除，减少干扰。我的服务器上没有有用的images和容器，我就全部都删除啦。
     ```
     docker ps
     docker stop container ...
     docker rm contianer ...
     docker images
     docker rmi images_name_or_id ...
     ```
     
  ## 3.3 docker-compose up
    进入 外网服务器 composetest 目录
    ```  
      修改文件： docker-compose.yml 
      version: "3.9"
      版本改成 原教程最新的 3.9
    ```  
    使用命令
    
       docker-compose up
       
    可以见 composetest 开始编译和执行
    回到客户端，浏览器输入 http://ip:5000  可以看到项目正常运行
    
    Good job!
    
    ❤️ Perfect!
    
      
      
 

