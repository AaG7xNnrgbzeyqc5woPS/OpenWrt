# 0. See:
  - [Failed to install Nextcloud with docker-compose](https://help.nextcloud.com/t/failed-to-install-nextcloud-with-docker-compose/83681)
  - [vishnubob/wait-for-it](https://github.com/vishnubob/wait-for-it)
  - [How to Self-Host a Collaborative Cloud with Nextcloud and Docker](https://www.cloudsavvyit.com/12476/how-to-self-host-a-collaborative-cloud-with-nextcloud-and-docker/)
  -  ❤️[Support for docker secrets on first initialization broken ](https://gitmemory.com/issue/nextcloud/docker/1148/860636969)
  -  [Linuxserver.io Nextcloud docker-compoe is all i need?](https://www.reddit.com/r/docker/comments/njnvth/linuxserverio_nextcloud_dockercompoe_is_all_i_need/)
  -  [Nextcloud Docker with Traefik Reverse Proxy for Beginners](https://www.smarthomebeginner.com/traefik-docker-nextcloud/)


# 1. test 1:
- Question: 在使用 docker secrets 时候，无法链接数据库! 初始化出现问题，之前使用环境变量做链接参数没有问题的！
- 通过上面的文章，发现几种解决方案
# 1.1  
[Support for docker secrets on first initialization broken ](https://gitmemory.com/issue/nextcloud/docker/1148/860636969)方案中
```
Actually, everything works as intended (if I got the things correctly):

https://github.com/nextcloud/docker/blob/05026b029d37fc5cd488d4a4a2a79480e39841ba/21.0/apache/entrypoint.sh#L121

MYSQL_PASSWORD_FILE will be read and loaded only if NEXTCLOUD_ADMIN_USER and NEXTCLOUD_ADMIN_PASSWORD are provided.

This happens because in this case Nextcloud is being installed via occ maintenance:install command.

Otherwise Nextcloud is not installed, and autoconfig.php is used: https://github.com/nextcloud/docker/blob/05026b029d37fc5cd488d4a4a2a79480e39841ba/21.0/apache/config/autoconfig.php#L9 Autoconfig reads only ordinary MYSQL_PASSWORD etc environment variables, which do not exist.

```
- ❤️ Need NEXTCLOUD_ADMIN_USER and NEXTCLOUD_ADMIN_PASSWORD are provided.

# 1.2

