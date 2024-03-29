# 0. See:
  - [nextcloud image](https://hub.docker.com/_/nextcloud)

# 1. compose.yaml
```
# root @ OpenWrt in ~/nextcloud1 [9:41:32] 
$ cat compose.yaml
version: '2'

volumes:
  nextcloud:
  db:

services:
  db:
    image: mariadb
    restart: always
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    volumes:
      - db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=root_pwd
      - MYSQL_PASSWORD=user_pwd
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  app:
    image: nextcloud
    restart: always
    ports:
      - 8080:80
    links:
      - db
    volumes:
      - nextcloud:/var/www/html
    environment:
      - MYSQL_PASSWORD=user_pwd
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=db

# root @ OpenWrt in ~/nextcloud1 [9:41:40] 
$ 

```
- 使用 nextcloud image文档推荐的 docker-compose 文件也不行。
- 还是同样的错误
- 错误页有帮助提示：[Installation and server configuration](https://docs.nextcloud.com/server/22/admin_manual/installation/index.html)

# 2. 查看文档，调试bug 
- 在这个文档找到 相关的 资料，[Installation wizard Quick start](https://docs.nextcloud.com/server/22/admin_manual/installation/installation_wizard.html#quick-start)，特别是其中的 《Database choice》部分


**Database choice**

SQLite is the default database for Nextcloud Server and it is good only for testing and lightweight single-user setups without client synchronization. Supported databases are MySQL, MariaDB, Oracle 11g, and PostgreSQL, and we recommend MySQL/MariaDB. Your database and PHP connectors must be installed before you run the Installation Wizard. When you install Nextcloud from packages all the necessary dependencies will be satisfied (see Installation on Linux for a detailed listing of required and optional PHP modules). You will need the root database login, or any administrator login , and then enter any name you want for your Nextcloud database. Be careful your administrator login needs to have the permissions to create and modify databases and they needs to have the permissions to grant permissions to other users.

After you enter your root or administrator login for your database, the installer creates a special database user with privileges limited to the Nextcloud database. Then Nextcloud needs only the special Nextcloud database user, and drops the root dB login. This user is named for your Nextcloud admin user, with an oc_ prefix, and then given a random password. The Nextcloud database user and password are written into config.php:
```
'dbuser' => 'oc_molly',
'dbpassword' => 'pX65Ty5DrHQkYPE5HRsDvyFHlZZHcm',
```
Click Finish Setup, and start using your new Nextcloud server.


**出错信息**

>Error
>
>Error while trying to initialise the database: An exception occurred while executing a query: SQLSTATE[HY000]: General error: 4047 InnoDB refuses to write tables with ROW_FORMAT=COMPRESSED or KEY_BLOCK_SIZE. 

# 3. internet 上查找 bug信息
使用这一段bug信息在 startpage 上查找信息
```
SQLSTATE[HY000]: General error: 4047 InnoDB refuses to write tables with ROW_FORMAT=COMPRESSED or KEY_BLOCK_SIZE. 
```
❤️ 找到一堆条目，看来这是典型错误，是程序内在问题，很多人升级都遇到这个问题。就是程序太新啦

- [How to fix Nextcloud 4047 InnoDB refuses to write tables with ROW_FORMAT=COMPRESSED or KEY_BLOCK_SIZE.](https://techoverflow.net/2021/08/17/how-to-fix-nextcloud-4047-innodb-refuses-to-write-tables-with-row_formatcompressed-or-key_block_size/)
- [Error when upgrading](https://www.reddit.com/r/NextCloud/comments/ncz7t6/error_when_upgrading/)
- [ Update to next cloud 21.0.2 has get an error #27085 ](https://github.com/nextcloud/server/issues/27085)
- [Unable to create tables with ROW_FORMAT=COMPRESSED [closed]](https://dba.stackexchange.com/questions/256427/unable-to-create-tables-with-row-format-compressed)
- [Enabling MySQL 4-byte support](https://docs.nextcloud.com/server/21/admin_manual/configuration_database/mysql_4byte_support.html)
- [[SOLVED] Issues on nextcloud 22.0.0 and mariadb 10.6](https://bbs.archlinux.org/viewtopic.php?id=268127)
- [InnoDB COMPRESSED Row Format](https://mariadb.com/kb/en/innodb-compressed-row-format/)
- [InnoDB Row Formats Overview](https://mariadb.com/kb/en/innodb-row-formats-overview/)
- [14.9.1.7 SQL Compression Syntax Warnings and Errors](https://dev.mysql.com/doc/refman/5.7/en/innodb-compression-syntax-warnings.html)

  查看文档后发现关键需要下面的指令：

  
  ❤️ 使用下面的指令成功，经过测试！
  ```
  command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW --innodb-file-per-table=1 --skip-innodb-read-only-compressed
  ```
  
  据说这条指令也可以，未测试： 
  ```
  command: --innodb-read-only-compressed=OFF
  ```
  

# 4. compose.yaml 修改
- 问题： 目前 networks 只能有一个，如果是两个的话，nextcloud无法解析 db的地址
- 在一个网络下，现在可以nextcloud可以初始化，完全可以使用啦。
- ❤️ Good job!

```
# root @ OpenWrt in ~/nextcloud2 [15:06:00] 
$ cat compose.yaml 
# Use root/root_pwd as user/password credentials
version: '3.9'

services:
# backend:
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

# frontend:
  adminer:
    image: adminer
    restart: always
    ports:
      - 8081:8080
    networks:
      - back-tier
#      - front-tier
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
#     - front-tier
    depends_on:
      - db
  
  
volumes:
  db_data: {}
  nextcloud: {}

networks:
#  front-tier: {}
  back-tier: {}


# root @ OpenWrt in ~/nextcloud2 [15:06:05] 
$ 

```
  
