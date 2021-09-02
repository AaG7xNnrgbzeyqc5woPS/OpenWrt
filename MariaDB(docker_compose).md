
# 0. see:
- [mariadb Official Image](https://hub.docker.com/_/mariadb)
- 

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
  
      $ docker run  --restart always -p 127.0.0.1:3306:3306  --name mariadb -e MARIADB_ROOT_PASSWORD=passwd -d mariadb

2.2 启动 adminer
  - See: [admier](https://hub.docker.com/_/adminer)
        docker run --link some_database:db -p 8080:8080 adminer
