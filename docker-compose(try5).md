# 0. See:
- [mariadb office image - Docker Secrets](https://hub.docker.com/_/mariadb)
- [Docker Secrets](https://hub.docker.com/_/nextcloud)

# 1. 使用普通用户名
  
# 1.1. Define the project "nc4"
- nc = nextcloud's abbreviation
- 4 for 4th try


## 1.2 Create a empty directory with name "nc4"

```
 # root @ OpenWrt in ~/nc4 [11:18:52] 
$ cat compose.yaml
#[compose-spec](https://github.com/compose-spec/compose-spec/blob/master/spec.md)
# Use root/root_pwd as user/password credentials

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
        - MYSQL_PASSWORD=user_pwd
        - MYSQL_DATABASE=db_nextcloud
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


# root @ OpenWrt in ~/nc4 [11:19:03] 
$ 
  
```

# 1.3. 实验一 
- 改进后，nextcloude 用普通用户也能登录，初始化。原先不能初始化的时候，担心权限不够用root用户，这是不必要的。
- 改进，nextcloud 使用普通用户 user_nextcloud/user_pwd 登录：
```
nextcloud:
      image: nextcloud
environment:
        - MYSQL_PASSWORD=user_pwd
        - MYSQL_DATABASE=db_nextcloud
        - MYSQL_USER=user_nextcloud
        - MYSQL_HOST=db  
```

# 2.  实验二 Docker Secrets 
## 3.1 Docker Secrets

As an alternative to passing sensitive information via environment variables, _FILE may be appended to the previously listed environment variables, causing the initialization script to load the values for those variables from files present in the container. In particular, this can be used to load passwords from Docker secrets stored in /run/secrets/<secret_name> files. For example:
```
$ docker run --name some-mysql -e MARIADB_ROOT_PASSWORD_FILE=/run/secrets/mysql-root -d mariadb:tag
```
Currently, this is only supported for MARIADB_ROOT_PASSWORD, MARIADB_ROOT_HOST, MARIADB_DATABASE, MARIADB_USER, and MARIADB_PASSWORD (and MYSQL_* equivalents of these).

## 3.2 compose.yaml
```
  
```


