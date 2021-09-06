# 0. see:
  [Quickstart: Compose and Django](https://docs.docker.com/samples/django/)

# 1. try1
   - 按照上面教程的指导，建立一个django项目，
   - 尽量手工输入，这样学的更多
   - 长的不好记忆的，为了避免错误，还是拷贝吧，现在我们还不会调试，要是错误太多，就麻烦啦
   - 一切顺利，
   - 项目开始 启动  
     
       docker-compose up
   - 出现一个错误，说 数据库的超级用户root要有口令，不能为空
   - 查看 docker-compose.yml 文件
   - 发现环境变量定义处： POSTGRES_PASSWORD=postgres 这里有 笔误，PASSSWORD多了一个S，改正
   - 保存 docker-compose.yml

# 2. try2




