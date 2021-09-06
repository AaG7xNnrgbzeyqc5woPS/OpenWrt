# 0. see:
  [Quickstart: Compose and Django](https://docs.docker.com/samples/django/)

# 1. try1
   - 按照上面教程的指导，建立一个django项目，
   - 尽量手工输入，这样学的更多
   - 长的不好记忆的，为了避免错误，还是拷贝吧，现在我们还不会调试，要是错误太多，就麻烦啦
   - 一切顺利，
   - 项目开始 启动      
``` 
       docker-compose up
```
   - 出现一个错误，说 数据库的超级用户root要有口令，不能为空
   - 查看 docker-compose.yml 文件
   - 发现环境变量定义处： POSTGRES_PASSWORD=postgres 这里有 笔误，PASSSWORD多了一个S，改正
   - 保存 docker-compose.yml

# 2. try2
   ## 2.1 清除 上次项目的遗留垃圾
     直接启动，报错。只能删除上次生成的垃圾。
     进入项目顶层目录
 ```
   rm *.py
   rm -r composeexample
   rm -r data
   
 ```
  
  ## 2.2 Create a Django project
```
   docker-compose run web django-admin startproject composeexample .
```
   项目目录会生成一些东西，注意查看
  
  ## 2.3 再次编辑composeexample/settings.py文件

```
    # settings.py
   
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'postgres',
        'USER': 'postgres',
        'PASSWORD': 'postgres',
        'HOST': 'db',
        'PORT': 5432,
    }
}
```

  ## 2.4 docker-compose up
    启动 django 项目
```
    docker-compose up
```
  输出提示信息，启动成功！   
  ❤️ Good job!
  
  ## 2.5 客户端浏览
  ```
     http://ip:8000
  ```
  提示出错，要求 设置 配置文件 composeexample/settings.py
```
  ALLOWED_HOSTS = ['*']
```

  
   



