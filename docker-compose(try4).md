# 0. See:
  - [Compose file Reference and guidelines](https://docs.docker.com/compose/compose-file/)
  - [Compose file versions and upgrading](https://docs.docker.com/compose/compose-file/compose-versioning/)
  - [Compose file version 2 reference](https://docs.docker.com/compose/compose-file/compose-file-v2/#aliases)
  - [Networking in Compose](https://docs.docker.com/compose/networking/)
  - [ compose-spec/spec.md ](https://github.com/compose-spec/compose-spec/blob/master/spec.md)
  - [Control startup and shutdown order in Compose](https://docs.docker.com/compose/startup-order/)
  - [Frequently asked questions](https://docs.docker.com/compose/faq/)
  - 
# 1. Question
  - compose.yaml 文件中使用 多个网络，nextcloud不能正常访问
  - version 问题，用最新格式的
  - depends_on, links, alias, healthcheck, env_file, container_name


# 2. 尝试一，compose.yaml
- ❤️使用最新dockpose.yaml文件版本成功！[ compose-spec/spec.md ](https://github.com/compose-spec/compose-spec/blob/master/spec.md)
- ❤️最新文件版本，yaml文件开头不需要 version 关键字，（老版本需要前置： version "2.x"，version: "3.x"）
- ❤️nextcloud初始化可以正常运行，能在双网络的环境下访问mariadb数据库，测试两次都可以
-  ~~当上线 docker-compose up -d 时候， openwrt服务器中的容器服务不用重新启动~~。这个假象，docker-compose项目再启动就不行。
- ❤️使用 version: '3.9' 也是可以 在双网络的环境下访问mariadb数据库，测试两次都可以


```
# root @ OpenWrt in ~/nextcloud3 [20:01:27] 
$ cat compose.yaml
#[compose-spec](https://github.com/compose-spec/compose-spec/blob/master/spec.md)
# Use root/root_pwd as user/password credentials
# version: '3.9'

services:
  #backend:
    db:
      image: mariadb
      restart: always
      command: 
        - --transaction-isolation=READ-COMMITTED 
        - --binlog-format=ROW 
        - --innodb-file-per-table=1
        - --skip-innodb-read-only-compressed
      volumes:
        - db_data:/var/lib/mysql     
      environment:
        - MARIADB_ROOT_PASSWORD=root_pwd
        - MARIADB_DATABASE=db_nextcloud
        - MARIADB_USER=user_nextcloud
        - MARIADB_PASSWORD=user_pwd
      networks:
        - back-tier

  #frontend:
    adminer:
      image: adminer
      restart: always
      ports:
        - 8081:8080
      networks:
        - back-tier
        - front-tier
      depends_on: 
        - db
      
    nextcloud:
      image: nextcloud
      restart: always
      ports:
        - 8080:80
      volumes:
        - nextcloud:/var/www/html
      environment:
        - MYSQL_PASSWORD=root_pwd
        - MYSQL_DATABASE=db_nextcloud
        - MYSQL_USER=root
        - MYSQL_HOST=db   
      networks:
        - back-tier
        - front-tier
      depends_on:
        - db
  
  
volumes:
  db_data: {}
  nextcloud: {}

networks:
  front-tier: {}
  back-tier: {}


# root @ OpenWrt in ~/nextcloud3 [20:01:35] 
$ 


```
