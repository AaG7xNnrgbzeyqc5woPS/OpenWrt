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


# 1. 链接进入mysql
```
# ssh openwrt
# docker ps | grep mariadb
# d8c94f052af2   mariadb  ....  mariadbtest
d8c94f052af2   mariadb   "docker-entrypoint.s…"   14 hours ago   Up 14 hours   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp  mariadbtest

# docker exec -ti mariadbtest bash 
root@d8c94f052af2:/# 
```






