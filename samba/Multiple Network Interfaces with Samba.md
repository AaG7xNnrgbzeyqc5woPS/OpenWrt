# See:
- [Multiple Network Interfaces with Samba](https://www.samba.org/~tpot/articles/multiple-interfaces.html)


# samba多接口设置
最后的成功的配置如下，关键是 interface 选项
```
$ cat /etc/config/samba 

config samba
	option name 'OpenWrt'
	option workgroup 'WORKGROUP'
	option description 'OpenWrt'
	option homes '0'
	option interface 'loopback lan zt_sql'
	option autoshare '0'

```

# TEST
```
# netstat -tapn | grep smbd
# netstat -tapn | grep smbd
# smbclient -L //10.1.1.1 -U%

# netstat -apn | grep nmbd

```
