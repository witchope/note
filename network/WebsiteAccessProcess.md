# 浏览器访问过程

在浏览器中输入 `www.google.com` 后执行的全部过程

 1. 浏览器获取输入的域名 `www.google.com`
 2. 浏览器向 DNS 请求解析 `www.google.com` 的 IP 地址
 3. 域名系统 DNS 解析出服务器 IP 地址
 4. 浏览器与该服务器建立 TCP 连接（默认端口 80）
 5. 浏览器发出 HTTP 请求， 请求 Google 首页
 6. 服务器通过 HTTP 响应把文件传输回浏览器
 7. TCP 连接释放
 8. 浏览器对 HTTP 页面进行解析渲染

涉及的协议：

- 应用层：HTTP（WWW 访问协议），DNS（域名解析服务）
- 传输层：TCP（为 HTTP 提供可靠的数据传输），UDP （DNS使用UDP传输）
- 网络层：IP（IP 数据包传输和路由的选择），ICMP（提供网络传输过程中的差错检测），ARP（将本地的默认网关 IP 地址映射成无力 MAC 地址）
