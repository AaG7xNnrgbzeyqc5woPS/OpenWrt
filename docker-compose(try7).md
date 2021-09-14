
# 0. See:
- [docker-compose(try4)](https://github.com/AaG7xNnrgbzeyqc5woPS/OpenWrt/blob/master/docker-compose(try5).md)

# 1. 还原
 - 使用安全文件的方法传输 秘密数据，一直不成功。先放弃吧
 - 回复到 try4 已经成功的项目，作为基础继续改进

```
#[compose-spec](https://github.com/compose-spec/compose-spec/blob/master/spec.md)

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
        - MARIADB_DATABASE=nextcloud
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
        - MYSQL_PASSWORD=user_pwd
        - MYSQL_DATABASE=nextcloud
        - MYSQL_USER=user_nextcloud
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

```
- ❤️调试再次通过的!可以建立数据库，完成nextcloud初始化，使用adminer可以 看到 mariadb中 nextcloud数据库建立成功，里面有很多表格


# 2. 使用环境变量

