# See:
- [linux重启定时任务crontab命令](https://www.cnblogs.com/7qin/p/13584646.html)
- [Cron 和 crontab](https://openwrt.org/zh/docs/guide-user/base-system/cron)
- [The quick and simple editor for cron schedule expressions by Cronitor](https://crontab.guru)

# 计划任务 
在最后增加一行
```
15 * * * * rsync -azP -i /root/.ssh/id_ed2519 /mnt/sda1/opt/ydh_share3/DB_BACKUP/AH_YDH_2023_0610/ hwt@10.41.51.49:AH_YDH_BACKUP_2023_7_7
```
