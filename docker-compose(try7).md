
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
  - 使用环境变量，环境变量的定义文件文件是 .env
  - ❤️第一次错误，环境变量的使用格式错误：正确的是，${variable}
  - 🖤nextcloud初始化成功，并且 使用adminer和事先设定的密码可以登录成功，并且能看到数据库，两条都满足才算成功！
  - 💙修改了.env文件的环境变量，再次重新建立容器和数据库，重复上面的验证步骤，再次成功！

**compose.yaml 和 .env文件内容如下：**
```
# root @ OpenWrt in ~/nc7 [21:42:00] 
$ cat compose.yaml 
#[compose-spec](https://github.com/compose-spec/compose-spec/blob/master/spec.md)
version: "3.9"

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
        - MARIADB_ROOT_PASSWORD=${MARIADB_ROOT_PASSWORD}
        - MARIADB_DATABASE=${MARIADB_DATABASE}
        - MARIADB_USER=${MARIADB_USER}
        - MARIADB_PASSWORD=${MARIADB_PASSWORD}
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
        - MYSQL_PASSWORD=${MARIADB_PASSWORD}
        - MYSQL_DATABASE=${MARIADB_DATABASE}
        - MYSQL_USER=${MARIADB_USER}
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

#env_file: .env

# root @ OpenWrt in ~/nc7 [21:42:04] 
$ cat .env        
MARIADB_ROOT_PASSWORD=pwd_8934FE#tygf
MARIADB_DATABASE=nextcloud
MARIADB_USER=user_name
MARIADB_PASSWORD=user_pwd_899er!dKPd

```
  
  

