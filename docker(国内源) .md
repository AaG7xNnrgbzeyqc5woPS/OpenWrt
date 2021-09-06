# 0. see:
  [docker 国内镜像源](https://www.jianshu.com/p/3b062c177508)
  
# 1. 创建或修改 /etc/docker/daemon.json 文件
```
# vi /etc/docker/daemon.json
{
    "registry-mirrors": ["http://hub-mirror.c.163.com", "https://docker.mirrors.ustc.edu.cn"]
}
systemctl restart docker.service

```

```
Docker中国区官方镜像
https://registry.docker-cn.com

网易
http://hub-mirror.c.163.com

ustc 
https://docker.mirrors.ustc.edu.cn

中国科技大学
https://docker.mirrors.ustc.edu.cn
```
