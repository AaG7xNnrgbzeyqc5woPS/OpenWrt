
# 0. see:
- [mariadb Official Image](https://hub.docker.com/_/mariadb)
- [How To Communicate Between Docker Containers ](https://www.tutorialworks.com/container-networking/)
- [Basic networking example on how to connect docker containers](https://linuxconfig.org/basic-example-on-how-to-link-docker-containers)


# 1. docker-compose start-up mariadb and adminer

via docker stack deploy or docker-compose

Example stack.yml for mariadb:

```
# Use root/example as user/password credentials
version: '3.1'

services:

  db:
    image: mariadb
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example

  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
```

Run docker stack deploy -c stack.yml mariadb (or docker-compose -f stack.yml up), wait for it to initialize completely, and visit http://swarm-ip:8080, http://localhost:8080, or http://host-ip:8080 (as appropriate).

- 启动成功，但是这两个容器的网络是另外一个，而且从adminer 上无法连接 mariadb容器。无法进一步调试。
- 还是用手工创建两个容器，定制更多的参数，便于调试，调试好了后再写 stack 文件，使用dock-compose

# 2. 手工启动 MariaDB 和 adminer容器
2.1 启动 MariaDB
  - See: [mariadb](https://hub.docker.com/_/mariadb)
  ```
      $ docker run  --restart always -p 127.0.0.1:3306:3306  --name mariadb -e MARIADB_ROOT_PASSWORD=passwd -d mariadb
  ```
2.2 启动 adminer
  - See: [adminer](https://hub.docker.com/_/adminer)  
```      
      $ docker run --link some_database:db -p 8080:8080 adminer
      
       docker run --restart always -d -p 8080:8080 adminer
```


Usage with external server

You can specify the default host with the ADMINER_DEFAULT_SERVER environment variable. This is useful if you are connecting to an external server or a docker container named something other than the default db.

```
docker run -p 8080:8080 -e ADMINER_DEFAULT_SERVER=mysql adminer
```

# 3. 数据库命令行外部链接
## 3.1  不暴露数据库的端口 
```
 $ docker run  --restart always  --name my_mariadb -e MARIADB_ROOT_PASSWORD=pass99 -d mariadb
 # 启动数据库，不暴露端口，3306端口在内网上，使用 172.17.0.4（mariadb容器的内网地址）直接链接
 $ docker run  -it --rm mariadb /bin/bash 
  mysql --host 172.17.0.4 -p
 // 链接成功！
  mysql --host 192.168.2.2 -p
 // 链接不成功！
 
 
```

```
root@5e4393baa3c2:/# mysql --host 172.17.0.4 -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 7
Server version: 10.6.4-MariaDB-1:10.6.4+maria~focal mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> exit
Bye
root@5e4393baa3c2:/# mysql --host 192.168.2.2 -p
Enter password: 
ERROR 2002 (HY000): Can't connect to server on '192.168.2.2' (115)
root@5e4393baa3c2:/# 

```

## 3.2 暴露数据库的端口
```
$ docker run  --restart always  -p 3306:3306 --name my_mariadb -e MARIADB_ROOT_PASSWORD=my_passwd_88   -d mariadb
$ docker run  -it --rm mariadb /bin/bash 

```
- 使用下面的两种方式都可以链接成功！
```
root@36c7ce01527d:/# mysql --host 172.17.0.4 -p
root@36c7ce01527d:/# mysql --host 192.168.2.2 -p
```
- ❤️ 重要知识：容器暴露端口给主机，有两种方式访问，直接访问 容器的IP地址和访问主机的IP地址都是可以的，通过实验已经证明啦！
-    换句话说：暴露端口给主机，不会关闭原来容器IP地址的访问
-    再换句话：同一个网络之间的容器之间总是可以访问的，不管这个容器是否包括端口给容器所在的主机

```
root@36c7ce01527d:/# mysql --host 172.17.0.4 -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 7
Server version: 10.6.4-MariaDB-1:10.6.4+maria~focal mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> exit
Bye
root@36c7ce01527d:/# mysql --host 192.168.2.2 -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 8
Server version: 10.6.4-MariaDB-1:10.6.4+maria~focal mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

Maria
```

# 4.Basic networking example on how to connect docker containers

- [Basic networking example on how to connect docker containers](https://linuxconfig.org/basic-example-on-how-to-link-docker-containers)

One of the many desirable features built directly into Docker is networking. Docker's networking feature can be accessed by using a --link flag which allows to connect any number of Docker containers without the need to expose a container's internal ports to the outside world.

In this guide, you will learn how to network two or more Docker containers together on a Linux system through command line instructions. This will work on any Linux distribution. Check out the step by step instructions below to find out how.

- 😧 注意：原来的容器image 格式太过时，我稍微修改下，仿照其中的原理，做个实验
```
docker run --name sandbox1 -h sandbox1 -e MARIADB_ROOT_PASSWORD=my_passwd_88 -d mariadb 
docker run -it --rm --name sandbox2 -h sandbox2 --link sandbox1:sandbox1  -e MARIADB_ALLOW_EMPTY_ROOT_PASSWORD mariadb /bin/bash
```
是过尝试，上面的命令终于可以使用了。

- ❤️ mysql --host sandbox1 -p
- 可以使用主机名，不用直接使用容器的ip地址
```
root@sandbox2:/# mysql --host sandbox1 -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 5
Server version: 10.6.4-MariaDB-1:10.6.4+maria~focal mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 

```

