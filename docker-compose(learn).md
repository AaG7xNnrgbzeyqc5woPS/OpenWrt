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

## 1.2尝试2


