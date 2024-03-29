# See:
- [Multiple Network Interfaces with Samba](https://www.samba.org/~tpot/articles/multiple-interfaces.html)
- [samba interfaces](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html#INTERFACES)


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

# Other Tricks

The interfaces parameter understands a couple of different ways of specifying network interfaces. You may like to use the IP and mask format way of specifying a network. Instead of of specifying eth1 you can say either 10.1.1.1/8 (bit length format) or 10.1.1.1/255.0.0.0 (netmask format). This may be more readable than just using the interface name.
