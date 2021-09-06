# See:
- [Quickstart: Compose and Rails](https://docs.docker.com/samples/rails/)

# Define the project
依照上面的教程，创建下面的文件
```
root@localhost:~/compose# mkdir rails
root@localhost:~/compose# ls
composetest  django  rails
root@localhost:~/compose# cd rails
root@localhost:~/compose/rails# ls
root@localhost:~/compose/rails# nano Dockerfile
root@localhost:~/compose/rails# nano Gemfile
root@localhost:~/compose/rails# touch Gemfile.lock
root@localhost:~/compose/rails# nano entrypoint.sh
root@localhost:~/compose/rails# nano docker-compose.yml

```

# Build the project
With those files in place, you can now generate the Rails skeleton app using docker-compose run:
```
docker-compose run --no-deps web rails new . --force --database=postgresql
```
长长的输出屏......    

生成了一堆东西，如下：  

```
root@localhost:~/compose/rails# docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
rails_web    latest    d2dd432801e9   6 minutes ago   960MB
django_web   latest    7bf0d83eb1fd   3 hours ago     965MB
python       3         a31efa943659   2 days ago      911MB
postgres     latest    346c7820a8fb   2 days ago      315MB
ruby         2.5       27d049ce98db   2 months ago    843MB
root@localhost:~/compose/rails# docker ps 
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
root@localhost:~/compose/rails# docker ps -a
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS                     PORTS     NAMES
e81901a24879   rails_web   "entrypoint.sh rails…"   6 minutes ago   Exited (0) 5 minutes ago             rails_web_run_6a905f215af7
root@localhost:~/compose/rails# docker ps 
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
root@localhost:~/compose/rails# ls -l
total 84
drwxr-xr-x 10 root root 4096 Sep  6 19:52 app
drwxr-xr-x  2 root root 4096 Sep  6 19:53 bin
drwxr-xr-x  5 root root 4096 Sep  6 19:52 config
-rw-r--r--  1 root root  130 Sep  6 19:52 config.ru
drwxr-xr-x  2 root root 4096 Sep  6 19:52 db
-rw-r--r--  1 root root  358 Sep  6 19:46 docker-compose.yml
-rw-r--r--  1 root root  484 Sep  6 19:41 Dockerfile
-rw-r--r--  1 root root  202 Sep  6 19:44 entrypoint.sh
-rw-r--r--  1 root root 2214 Sep  6 19:52 Gemfile
-rw-r--r--  1 root root 5236 Sep  6 19:53 Gemfile.lock
drwxr-xr-x  4 root root 4096 Sep  6 19:52 lib
drwxr-xr-x  2 root root 4096 Sep  6 19:52 log
-rw-r--r--  1 root root   63 Sep  6 19:52 package.json
drwxr-xr-x  2 root root 4096 Sep  6 19:52 public
-rw-r--r--  1 root root  227 Sep  6 19:52 Rakefile
-rw-r--r--  1 root root  374 Sep  6 19:52 README.md
drwxr-xr-x  2 root root 4096 Sep  6 19:52 storage
drwxr-xr-x  9 root root 4096 Sep  6 19:52 test
drwxr-xr-x  5 root root 4096 Sep  6 19:52 tmp
drwxr-xr-x  2 root root 4096 Sep  6 19:52 vendor
root@localhost:~/compose/rails# 


```

# Connect the database


