# 1. 创建虚拟网络环境(newtwork namespace) net0,net1
- ip netns add net0
- ip netns show

```
# root @ OpenWrt in ~ [22:37:22] 
$ ip netns                 
ns1 (id: 2)
ns0 (id: 1)

# root @ OpenWrt in ~ [22:44:31] 
$ ip netns add net0     

# root @ OpenWrt in ~ [22:44:41] 
$ ip netns add net1

# root @ OpenWrt in ~ [22:44:46] 
$ ip netns show    
net1
net0
ns1 (id: 2)
ns0 (id: 1)

# root @ OpenWrt in ~ [22:44:54] 
$ 

```

