# See:
- [Syncthing Main page](https://syncthing.net/)
- [Syncthing Getting Started](https://docs.syncthing.net/intro/getting-started.html)
- [ syncthing/syncthing](https://github.com/syncthing/syncthing)

# OpenWrt Syncthing


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
 
