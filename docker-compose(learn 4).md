# 0. See:
  - [Quickstart: Compose and WordPress](https://docs.docker.com/samples/wordpress/)

# 1. Define the project
- Create an empty project directory
- Create file "docker-compose.yml" at the project directory:
```
version: "3.9"
    
services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - wordpress_data:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
  wordpress_data: {}

```
# 2. Build the project
```
  docker-compose up -d
  docker-compose ps  
```
- 出现 几个错误，都是空格，对齐问题，一一改正就可以
- 特别要注意，yaml语言是缩进语法的


# 3. Bring up WordPress in a web browser
```
  http://ip:3000
```
- 进入 wordpress的管理界面，设置初始参数，进入后台管理界面。发表一篇文章，退出。再刷新 http://ip:3000
可以看到刷新的文章
- 管理界面使用htt://ip:3000/admin可以进入，需要用户名和口令
- ❤️ 一切正常， ❤️ Good job！

# 4. Shutdown and cleanup

The command docker-compose down removes the containers and default network, but preserves your WordPress database.

The command docker-compose down --volumes removes the containers, default network, and the WordPress database.
```
root@localhost:~/compose/wordpress# docker-compose down --rmi all --volumes
Stopping wordpress_wordpress_1 ... done
Stopping wordpress_db_1        ... done
Removing wordpress_wordpress_1 ... done
Removing wordpress_db_1        ... done
Removing network wordpress_default
Removing volume wordpress_db_data
Removing volume wordpress_wordpress_data
Removing image mysql:5.7
Removing image wordpress:latest
root@localhost:~/compose/wordpress# 

```

