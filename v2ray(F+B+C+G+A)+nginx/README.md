介绍：

v2ray 或 Xray 前置（监听 443 端口），利用 trojan+tcp+tls 或 trojan+tcp+xtls 强大的回落/分流 WebSocket（WS）特性与 nginx 为 gRPC 提供反向代理，实现除 v2ray 或 Xray 的 KCP 应用外共用 443 端口。其应用如下：

1、F=trojan+tcp+tls/xtls（回落/分流配置，TLS/XTLS由自己提供及处理。）

2、B=vless+ws+tls（TLS由trojan+tcp+tls/xtls提供及处理，不需配置。另可改、可增其它WS类应用，参考对应的服务端单一应用配置示例。）

3、C=shadowsocks+xray-plugin/v2ray-plugin+tls（TLS由trojan+tcp+tls/xtls提供及处理，不需配置。）

4、G=vless+grpc+tls（TLS由trojan+tcp+tls/xtls提供及处理，不需配置。另可改、可增其它gRPC类应用，参考对应的服务端单一应用配置示例。）

5、A=vless+kcp+seed（可改成vmess+kcp+seed，或添加它。）

注意：

1、Xray 版本不小于 v1.5.1 才完美支持 TLS 模式下 trojan 协议。

2、v2ray 版本不小于 v4.43.0 才完美支持 trojan 协议。

3、nginx 支持 H2C server 及 gRPC proxy，需要 nginx 包含 http_v2_module 模块。

4、nginx 支持 HTTP 功能块接收 PROXY protocol，需要 nginx 包含 http_realip_module 模块。

5、nginx 支持 H2C server，但不支持 HTTP/1.1 server 与 H2C server 共用一个端口或一个进程（Unix Domain Socket 应用）；故回落配置就必须分成 http/1.1 回落与 h2 回落两部分，以便分别对应 nginx 的 HTTP/1.1 server 与 H2C server。

6、不要使用 ACME 客户端在当前服务器上以 HTTP 验证或 TLS-ALPN 验证方式申请与更新证书及密钥，因 HTTP 验证或 TLS-ALPN 验证方式申请与更新证书及密钥需监听 80 端口或 443 端口，从而与当前应用端口冲突。

7、配置1：采用端口回落/分流、端口转发。配置2：采用进程回落/分流、进程转发。配置3：采用进程回落/分流、进程转发，且启用了 PROXY protocol。
