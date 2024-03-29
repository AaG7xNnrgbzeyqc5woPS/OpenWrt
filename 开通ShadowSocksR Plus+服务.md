# 0. See:
  - [SSR Plus + 详细使用手册之完全体，关于科学上网那点事儿，都在这里了！](https://www.youtube.com/watch?v=nglEvMILpvE)
  - [软路由（operwrt）通过ShadowSocksr Plus+ 科学上网教程](https://iyuantiao.com/fenxiangfuli/jiaocheng/operwrt-through-shadowsocksr-plus-science-online-tutorial.html)

# 1. 服务节点(Trojan)
 ## 1.1 服务器节点订阅与管理
   这个主要对多服务器节点的管理，以及有发布服务器节点的机场服务采用，自建服务器的不用管   
   自动更新选项取消
   
 ## 1.2 手动添加服务器
   点下面的“添加”，按钮可以显示 “编辑服务器配置” 页面，输入
   ```
   服务器节点类型： Trojan
   别名： trojan
   服务器地址: www.example.net
   端口： 443
   密码：*****
   TLS： 勾选
   TLS Host： www.example.net
   TCP快速打开: 勾选
   本地端口：1234
   ```
   最后点“保存&应用”，之后在服务器节点列表中可以看见刚才配置的服务器
   看看“链接测试”，“ping延时”，是否都OK,想再测试一次可以点击“重新应用”
   如果不行，可以编辑配置，再次尝试。
   
 ## 1.3 服务端
   - 点开“服务端”页面
   - 全局设定/启动服务端，勾选
   - 服务端配置列表下，点添加按钮
   - 编辑服务器配置 页面下，输入服务器配置
   ```
   启用 勾选
   服务端类型：sock5
   端口：输入你想要的端口，比如 10080
   用户名：***
   密码：***
   ```
   - 注意：服务端，用来在openwrt上开通Sock5服务，将科学上网服务共享给整个局域网使用。也就是为整个局域网转发需要翻墙的流量，
   - 使用该 Sock5服务的客户机，需要在浏览器上安装 代理插件，才能使用本sock5服务
   - 为了安全，要求sock5服务必须要设置用户名和密码，否则无法开通sock5服务器。

# 2. 服务器节点（vless）
   ## 2.1 手动添加服务器
   点下面的“添加”，按钮可以显示 “编辑服务器配置” 页面，输入
   ```
   服务器节点类型： V2Ray/XRay
   别名： XRay
   V2Ray/XRay 协议: VLESS
   服务器地址: www.example.net
   端口： 443
   Vmess/VLESS ID (UUID):  *************
   VLESS 加密: none
   传输方式：TCP
   伪装类型：空
     
   TLS： 勾选
   指纹伪造： 禁用
   TLS Host： www.example.net
   允许不安全连接： 不勾选
   Mux：不勾选
   自签证书: 不勾选
   启用自动切换： 不勾选
   本地端口：1234
   ```
   最后点“保存&应用”，之后在服务器节点列表中可以看见刚才配置的服务器
   看看“链接测试”，“ping延时”，是否都OK,想再测试一次可以点击“重新应用”
   如果不行，可以编辑配置，再次尝试。  
   
 # 3. Sock5 设置
|    服务               | 状态   |
|----------------------| ------ |
|TCP透明代理            | 运行中  |
|游戏模式UDP中继        | 运行中 |
|DNS 防污染服务         | 运行中 |
|SOCKS5 代理服务端（全局）| 运行中 | 
|本机服务端              | 未运行 | 
