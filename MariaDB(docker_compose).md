
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

# 5. docker run --link 说明
The following Linux command will deploy a second and this time a parent docker container named sandbox2. We will also use a --link flag which will create a so called parent-child relationship with previously deployed container sandbox1. Furthermore, the --link flag will enable the parent container to access any services running on sandbox1 container via its corresponding ports numbers without the child container's need to expose any ports to outside world.

```
docker run -it --rm --name sandbox2 -h sandbox2 --link sandbox1:sandbox1  -e MARIADB_ALLOW_EMPTY_ROOT_PASSWORD mariadb /bin/bash

```

The above docker command used the --link flag which expects two arguments separated by a colon. The first argument is expected to be a container ID or as in our case a supplied sandbox1 container name we would like to link to. The second argument, also sandbox1, is an internal alias used by sandbox2 to resolve sandbox1's network configuration using the /etc/hosts configuration file:

```
oot@sandbox2:/# cat /etc/hosts          
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.4	sandbox1 sandbox1
172.17.0.5	sandbox2
root@sandbox2:/# grep sandbox1 /etc/hosts
172.17.0.4	sandbox1 sandbox1

```

Depending on your child container's ports configuration in use you can also extract sandbox1's configuration from system environment variables. For example: 
```
root@sandbox2:/# env
HOSTNAME=sandbox2
PWD=/
HOME=/root
SANDBOX1_ENV_GOSU_VERSION=1.13
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
MARIADB_VERSION=1:10.6.4+maria~focal
SANDBOX1_PORT_3306_TCP=tcp://172.17.0.4:3306
SANDBOX1_ENV_MARIADB_VERSION=1:10.6.4+maria~focal
SANDBOX1_NAME=/sandbox2/sandbox1
GOSU_VERSION=1.13
SANDBOX1_PORT_3306_TCP_PORT=3306
SANDBOX1_ENV_MARIADB_ROOT_PASSWORD=my_passwd_88
SANDBOX1_PORT_3306_TCP_ADDR=172.17.0.4
TERM=xterm
MARIADB_MAJOR=10.6
SHLVL=1
SANDBOX1_ENV_MARIADB_MAJOR=10.6
SANDBOX1_PORT=tcp://172.17.0.4:3306
SANDBOX1_PORT_3306_TCP_PROTO=tcp
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
_=/usr/bin/env
root@sandbox2:/# 

```

# 6. 还是用docker-compose 方法启动
- 前面的方法一直不能调试通，再换回第一种方法，如下文：
- [via docker stack deploy or docker-compose](https://hub.docker.com/_/mariadb?tab=description&page=1&ordering=last_updated)
- 折腾几次怎么也链接不上，原来能链接上的，现在怎么不行呢？后来重新启动服务器和笔记本电脑，就可以链接上了。
- 这次还有新收获，就是能用 adminer 登录 mariadb 服务器,管理 mariadb
- ❤️ 登录凭证： 服务器：db, 用户：root，密码： example，
- ❤️ 请和stack.yml文件对照，就知道其中的奥秘了，以前就是登录凭证不对导致无法登录。
- 

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

