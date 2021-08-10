# See:
 - [nextcloud](https://hub.docker.com/_/nextcloud)
 - [Docker DOC (Use volumes)](https://docs.docker.com/storage/volumes/)
 - [openwrt下 docker使用](https://koolshare.cn/thread-180474-1-1.html)
  
 - [Nextcloud -wikipedia](https://en.wikipedia.org/wiki/Nextcloud)
 - [nextcloud main page - office web](https://nextcloud.com/)
 - [nextcloud install - office web](https://nextcloud.com/install/)

# 安装NextCloud
## 新增模板
 - 在浏览器中，登录 http://192.168.2.2:9000  进入 PorTainer 控制界面。
 - 在左侧菜单栏点击 App Templates，添加一个 NextCloud的新条目
 - 新增条目表单中请填写以下项目：
 - Title， Description，Platform，Categories，Registry，Image， Network，Port mapping，Volume mapping，Restart policy 
 - 特别是：
 - TiTle： nextcloud
 - Registry： DocerHub; 
 - Image：nextcloud:latest，
 - Port mapping：host 8080---> container 80 /tcp
 - Restart policy: unless stopped
 - 最后点 Create the Template 按钮，建立完成

# 修改模板 
- 在 App Templates 菜单，搜索 nextcloud
- 点击 搜索到的 nextcloud 模板
- 点击右边的update 键，开始修改，
- 完成后点 update template  提交。


# 根据模板启动 nextcloud容器
  - 点击主菜单 App Templates 菜单 
  - 搜索到的 nextcloud 模板
  - 点击 nextcloud 
  - 点击 Deploy template 部署模板
  - 稍等片刻提示容器生成
  - 点击 主菜单 containers 可以看到 nextcloud 容器已经生成
  - 在浏览器中输入 http://192.168.2.2:8080可以看到nextcloud的控制界面，
  - nextcloud 容器安装完成

# 
