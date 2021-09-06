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
Replace the contents of config/database.yml with the following:
```
default: &default
  adapter: postgresql
  encoding: unicode
  host: db
  username: postgres
  password: password
  pool: 5

development:
  <<: *default
  database: myapp_development


test:
  <<: *default
  database: myapp_test

```

You can now boot the app with docker-compose up. If all is well, you should see some PostgreSQL output:
```
 docker-compose up
```

```
root@localhost:~/compose/rails# docker-compose up
Creating rails_db_1 ... done
Creating rails_web_1 ... done
Attaching to rails_db_1, rails_web_1
db_1   | The files belonging to this database system will be owned by user "postgres".
db_1   | This user must also own the server process.
db_1   | 
db_1   | The database cluster will be initialized with locale "en_US.utf8".
db_1   | The default database encoding has accordingly been set to "UTF8".
db_1   | The default text search configuration will be set to "english".
db_1   | 
db_1   | Data page checksums are disabled.
db_1   | 
db_1   | fixing permissions on existing directory /var/lib/postgresql/data ... ok
db_1   | creating subdirectories ... ok
db_1   | selecting dynamic shared memory implementation ... posix
db_1   | selecting default max_connections ... 100
db_1   | selecting default shared_buffers ... 128MB
db_1   | selecting default time zone ... Etc/UTC
db_1   | creating configuration files ... ok
db_1   | running bootstrap script ... ok
web_1  | bundler: failed to load command: rails (/usr/local/bundle/bin/rails)
web_1  | Bundler::GemNotFound: Could not find public_suffix-4.0.6 in any of the sources
web_1  |   /usr/local/lib/ruby/site_ruby/2.5.0/bundler/spec_set.rb:91:in `block in materialize'
web_1  |   /usr/local/lib/ruby/site_ruby/2.5.0/bundler/spec_set.rb:85:in `map!'
web_1  |   /usr/local/lib/ruby/site_ruby/2.5.0/bundler/spec_set.rb:85:in `materialize'
web_1  |   /usr/local/lib/ruby/site_ruby/2.5.0/bundler/definition.rb:170:in `specs'
web_1  |   /usr/local/lib/ruby/site_ruby/2.5.0/bundler/definition.rb:237:in `specs_for'
web_1  |   /usr/local/lib/ruby/site_ruby/2.5.0/bundler/definition.rb:226:in `requested_specs'
web_1  |   /usr/local/lib/ruby/site_ruby/2.5.0/bundler/runtime.rb:108:in `block in definition_method'
web_1  |   /usr/local/lib/ruby/site_ruby/2.5.0/bundler/runtime.rb:20:in `setup'
web_1  |   /usr/local/lib/ruby/site_ruby/2.5.0/bundler.rb:107:in `setup'
web_1  |   /usr/local/lib/ruby/site_ruby/2.5.0/bundler/setup.rb:20:in `<top (required)>'
web_1  |   /usr/local/lib/ruby/site_ruby/2.5.0/rubygems/core_ext/kernel_require.rb:54:in `require'
web_1  |   /usr/local/lib/ruby/site_ruby/2.5.0/rubygems/core_ext/kernel_require.rb:54:in `require'
rails_web_1 exited with code 1
db_1   | performing post-bootstrap initialization ... ok
db_1   | syncing data to disk ... ok

```


💔 错误。。。。。。
```
root@localhost:~/compose/rails# docker-compose run web rake db:create
Creating rails_web_run ... done
Could not find public_suffix-4.0.6 in any of the sources
Run `bundle install` to install missing gems.
ERROR: 7
```


