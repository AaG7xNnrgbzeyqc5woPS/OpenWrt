# See:
- [Syncthing Main page](https://syncthing.net/)
- [Syncthing Getting Started](https://docs.syncthing.net/intro/getting-started.html)
- [ syncthing/syncthing](https://github.com/syncthing/syncthing)

# OpenWrt Syncthing
启动 Syncthing
[网络存储/存储同步](http://192.168.2.2/cgi-bin/luci/admin/nas/syncthing)
syncthing同步工具
选择 启动 
保存&应用
 
firefox
打开 192.168.2.2:8384 
进入 syncthing 的网页配置界面
首先配置 
1. 设置Gui密码
2. Actions\Settings\Use HTTPS for GUI
3. 根据 [Syncthing Getting Started](https://docs.syncthing.net/intro/getting-started.html) 进行其他的配置




# nixos Syncthing
  
  - [Search syncthing in NixOS Search](https://search.nixos.org/packages?channel=21.11&from=0&size=50&sort=relevance&type=packages&query=syncthing)
  
  Add syncthing package
  
  ```
  sudo -i
  passowrd ...
  
  cd nixos/etc/nixos/
  nano configuration.nix
  add package "syncthing"
  nixos-rebuild build --upgrade
  nixos-rebuild switch --upgarde
  reboot
  
  cmd 
  syncthing
  
  firefox 
  http://127.0.0.1:8384
  
 ```
 Actions\Settings\Use HTTPS for GUI
 
 # 配置 Sencond.Brain 文件夹同步，在OpenWrt上进行备份
 
