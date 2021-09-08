# 0. See:
  - [Compose and WordPress](https://github.com/AaG7xNnrgbzeyqc5woPS/OpenWrt/blob/master/docker-compose(learn%204).md)
  - [MariaDB(docker_compose)](https://github.com/AaG7xNnrgbzeyqc5woPS/OpenWrt/blob/master/MariaDB(docker_compose).md)
  - [adminer image](https://hub.docker.com/_/adminer)
  - [mariadb image](https://hub.docker.com/_/mariadb?tab=description&page=1&ordering=last_updated)
  - [adminer official web](https://www.adminer.org/)
  - [mariadb official web](https://mariadb.com/)
  - [Host Name Component](https://mariadb.com/kb/en/create-user/#host-name-component)

# 1. Define the project
  通过前面几个例子的学习，现在来做几个尝试，改进下 MariaDB 和 adminer 容器链接。
```
# Use root/root_password as user/password credentials
version: '3.9'

services:

  db:
    image: mariadb
    restart: always
    environment:
      MARIADB_ROOT_PASSWORD: root_password
      MARIADB_DATABASE: db_test
      MARIADB_USER: user_test
      MARIADB_PASSWORD: user_passoword

  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
    depends_on: 
      - db
```
  
  
# 10. MariaDB Environment Variables 
  [MariaDB Environment Variables](https://hub.docker.com/_/mariadb?tab=description&page=1&ordering=last_updated)

## 10.1 Environment Variables
When you start the mariadb image, you can adjust the initialization of the MariaDB instance by passing one or more environment variables on the docker run command line. Do note that none of the variables below will have any effect if you start the container with a data directory that already contains a database: any pre-existing database will always be left untouched on container startup.

From tags 10.2.38, 10.3.29, 10.4.19, 10.5.10 onwards, and all 10.6 tags, the MARIADB_* equivalent variables are provided. MARIADB_* variants will always be used in preference to MYSQL_* variants.

One of MARIADB_ROOT_PASSWORD, MARIADB_ALLOW_EMPTY_ROOT_PASSWORD, or MARIADB_RANDOM_ROOT_PASSWORD (or equivalents, including *_FILE), is required. The other environment variables are optional.

**MARIADB_ROOT_PASSWORD / MYSQL_ROOT_PASSWORD**

This specifies the password that will be set for the MariaDB root superuser account. In the above example, it was set to my-secret-pw.

**MARIADB_ALLOW_EMPTY_ROOT_PASSWORD / MYSQL_ALLOW_EMPTY_PASSWORD**

Set to a non-empty value, like yes, to allow the container to be started with a blank password for the root user. NOTE: Setting this variable to yes is not recommended unless you really know what you are doing, since this will leave your MariaDB instance completely unprotected, allowing anyone to gain complete superuser access.

**MARIADB_RANDOM_ROOT_PASSWORD / MYSQL_RANDOM_ROOT_PASSWORD**

Set to a non-empty value, like yes, to generate a random initial password for the root user. The generated root password will be printed to stdout (GENERATED ROOT PASSWORD: .....).

**MARIADB_ROOT_HOST / MYSQL_ROOT_HOST**

This is the hostname part of the root user created. By default this is %, however it can be set to any default MariaDB allowed hostname component.

**MARIADB_DATABASE / MYSQL_DATABASE**

This variable allows you to specify the name of a database to be created on image startup.

**MARIADB_USER / MYSQL_USER, MARIADB_PASSWORD / MYSQL_PASSWORD**

These are used in conjunction to create a new user and to set that user's password. Both user and password variables are required for a user to be created. This user will be granted all access (corresponding to GRANT ALL) to the MARIADB_DATABASE database.

Do note that there is no need to use this mechanism to create the root superuser, that user gets created by default with the password specified by the MARIADB_ROOT_PASSWORD / MYSQL_ROOT_PASSWORD variable.

**MARIADB_INITDB_SKIP_TZINFO / MYSQL_INITDB_SKIP_TZINFO**

By default, the entrypoint script automatically loads the timezone data needed for the CONVERT_TZ() function. If it is not needed, any non-empty value disables timezone loading.

## 10.2 Docker Secrets

As an alternative to passing sensitive information via environment variables, _FILE may be appended to the previously listed environment variables, causing the initialization script to load the values for those variables from files present in the container. In particular, this can be used to load passwords from Docker secrets stored in /run/secrets/<secret_name> files. For example:

$ docker run --name some-mysql -e MARIADB_ROOT_PASSWORD_FILE=/run/secrets/mysql-root -d mariadb:tag

Currently, this is only supported for MARIADB_ROOT_PASSWORD, MARIADB_ROOT_HOST, MARIADB_DATABASE, MARIADB_USER, and MARIADB_PASSWORD (and MYSQL_* equivalents of these).

## 10.3 总结
 **MARIADB_ROOT_PASSWORD**
 - 这环境变量 指定 root 用户的口令，用户创建 root 用户
 - root必须创建，所以相关的root口令参数必须有。

 **MARIADB_DATABASE**
 - MariaDB 启动时，创建该数据库

 **MARIADB_USER, MARIADB_PASSWORD**
 -  这两个环境变量用于创建一个用户，并且用户拥有**MARIADB_DATABASE**环境变量指定的数据库的全部权限
 -  注意该用户不是root用户，创建root不需要改指令，只用指定**MARIADB_ROOT_PASSWORD**环境变量就可以


  
