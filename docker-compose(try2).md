# 0. See:
  - 1. [nextcloud image](https://hub.docker.com/_/nextcloud)
  - 2. [NextCloud 部署尝试一](https://github.com/AaG7xNnrgbzeyqc5woPS/OpenWrt/blob/master/NAS(NextCloud).md)
  - 3. [docker-compose(try1)](https://github.com/AaG7xNnrgbzeyqc5woPS/OpenWrt/blob/master/docker-compose(try1).md)


# 1. nextcloud 部署尝试二
  第二次尝试 部署 nextcloud，这是使用 docker-compose技术，将前面三篇引用文章的内容结合起来
  部署：mariadb,adminer,nextcloud三个容器，这里先做基本尝试，更高级的放到部署尝试三里去做。
  # 1.1 Create the project with name "nextcloud2"
    
      cd ~
      mkdir nextcloud2
      
  # 1.2 Create compose.yaml file
  
```
# Use root/root_pwd as user/password credentials
version: '3.9'

services:
# backend:
    db:
      image: mariadb
      restart: always
      command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
      volumes:
        - db_data:/var/lib/mysql     
      environment:
        MARIADB_ROOT_PASSWORD: root_pwd
        MARIADB_DATABASE: db_nextcloud
        MARIADB_USER: user_nextcloud
        MARIADB_PASSWORD: user_pwd
      networks:
        - back-tier
  
# frontend:
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
        - MYSQL_PASSWORD=user_pwd
        - MYSQL_DATABASE=db_nextcloud
        - MYSQL_USER=user_nextcloud
        - MYSQL_HOST=db   
      depends_on:
        - db
    
    
volumes:
  db_data: {}
  nextcloud: {}

networks:
  front-tier: {}
  back-tier: {}

```

# 1.3 启动 
   
     docker-compose up -d
   
   启动成功！
# 1.4 浏览器访问
  http://ip:8080 可以访问到 nextcloud，
  http://ip:8081 可以访问到 adminer，并且能登录
  

