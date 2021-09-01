
# 0. see:
- [mariadb Official Image](https://hub.docker.com/_/mariadb)
- 

# 1.

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

