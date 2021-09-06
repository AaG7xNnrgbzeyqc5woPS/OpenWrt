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


