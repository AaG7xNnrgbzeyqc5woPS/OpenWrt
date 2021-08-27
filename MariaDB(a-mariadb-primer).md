# 0. See:
## 0.1 官方网站
- [mariadb.org(Official web)](https://mariadb.org/)
- [MariaDB-server(Source) ](https://github.com/MariaDB/server)
- [mariadb (official image)](https://hub.docker.com/_/mariadb)
- [mariadb-docker(Source)](https://github.com/MariaDB/mariadb-docker)

## 0.2 官方文档
- [mariadb doc](https://mariadb.org/documentation/)
- [Beginner MariaDB Articles](https://mariadb.com/kb/en/beginner-mariadb-articles/)
- [a-mariadb-primer](https://mariadb.com/kb/en/a-mariadb-primer/)
- [Connecting to MariaDB](https://mariadb.com/kb/en/connecting-to-mariadb/)
- [MariaDB Basics](https://mariadb.com/kb/en/mariadb-basics/)
- [MariaDB Training & Tutorials](https://mariadb.com/kb/en/training-tutorials/)

## 0.3 教学网站
[www.tutorialspoint.com/mariadb](https://www.tutorialspoint.com/mariadb/index.htm)


# 1. 使用mysql登录 mariadb

```

# ssh openwrt
# docker ps | grep mariadb
# d8c94f052af2   mariadb  ....  mariadbtest
d8c94f052af2   mariadb   "docker-entrypoint.s…"   14 hours ago   Up 14 hours   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp  mariadbtest

# docker exec -ti mariadbtest bash 
root@d8c94f052af2:/# 
root@d8c94f052af2:/# mysql
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
root@d8c94f052af2:/# mysql -p    
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 13
Server version: 10.6.4-MariaDB-1:10.6.4+maria~focal mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 


```

登录 openwrt服务器，进入 docker container mariadbtest, 使用 mysql -p链接 mariadb服务
最后 出现提示符 MariaDB [(none)]>， 表示已经链接成功！

# 2. 尝试一些命令
## 2.1 帮助命令，简单的命令
```
help
?
\h
\?

status
system ls 

use
exit
quit

```
- **小窍门，如果一行命令输入错了，要取消，使用 \c 两个字符，再回车，可以取消刚才输入的字符，如果使用 ctrl+c，会导致mysql客户端整个退出。
- 

## 2.2 server side help
  For server side help, type 'help contents'
```
  
help contents
help Administration
SHOW DATABASES
SHOW TABLES
```
## 2.3 learn  [a-mariadb-primer](https://mariadb.com/kb/en/a-mariadb-primer/)
  根据教程做一遍，关键的命令，我在前面已经讲过了！






