# 常见问题

## 升级后 WebUI 白屏/黑屏/卡无限数据加载

清除浏览器缓存。

## 启动报错 Failed to bind to port

同时启动了两个 PeerBanHelper 或者端口被占用导致端口冲突。

[尝试更改 WebUI 端口](./network/http-server.md#更改-webui-端口)

## 127.0.0.1 或者 localhost 连不上下载器

出现这个问题多半是因为用了 Docker 容器部署导致的。在容器里使用 `127.0.0.1` 或者 `localhost` 指向的是容器内部，当然是连不上的。

群晖用户：Container Manager -> 网络 -> Bridge，使用显示的网关地址连接。  
其它 Docker 用户：执行 `sudo docker network inspect bridge` 命令：

```json
"IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        }
```

使用上面的 Gateway 地址连接。

## 无法下载 IPDB/GeoIP 库 / 代理无效

参见：[配置代理服务器](./network/proxy-server.md)

## WebUI 管理 Token 在哪里？

参见：[更改 WebUI Token](./network/http-server.md#更改-webui-token)

## Transmission 有什么缺点/为什么弃用/废弃了

参见：[废弃对 Transmission 下载器的支持 #382](https://github.com/PBH-BTN/PeerBanHelper/issues/382)

## 反吸血进度检查器为什么显示进度超过 100% (例如：102%)，是不是出错了？怎么还能超过 100% 的？

不是的，进度检查器会累加此 IP 地址在特定种子上的下载进度。如果对方出现进度回退、断开更换端口重连、更换 PeerID、更换 Client name 重新下载时，下载器会认为这是一个新客户端，并从头开始计算下载数据（吸血者也使用此手段绕过吸血检查）。但对于 PBH 来说，只要对方 IP 地址未改变（或者处于特定区间内），并且下载的种子未更换的情况下，下载进度会持续增量累积，避免对方欺骗反吸血检查。例如一个文件大小是 1000MB，对方下载 102% 代表对方在这个 1000MB 大小的种子上，实际下载了 1020MB 的数据。

## PBH 提示我的下载器 “连续多次登录失败” ，并暂停了该怎么办？

您可以点击下载器的编辑按钮，然后直接点击确定保存。PBH 将会解除暂停状态并重新尝试登陆，此时会显示登陆失败的原因。请根据原因进行故障排查（例如：网络连接问题、WebUI 是否启用、用户名密码是否正确等）。排查完了再保存一次成功就解除暂停了。

## 什么是增量封禁？

非增量封禁：每次有新 IP 要封禁的时候，直接整个替换 IP 黑名单列表。在 qBittorrent 上容易造成下载器卡死。  
增量封禁：每次有新 IP 要封禁的时候，使用 banPeer API 增量添加封禁 IP；在解除封禁的时候，仍然是直接整个替换 IP 黑名单列表。

## 什么是验证 SSL 证书？

如果填写的地址是一个 HTTPS 地址，且此开关启用，则会验证 SSL 证书的有效性。如果证书无效，则报错保证安全。  
如果关闭，则信任所有 SSL 证书。

## 什么是 Basic Auth？

有的教程会让你通过反代或者 Nginx 额外添加一层用户名密码保证安全，其特点是浏览器访问的时候会弹框验证：

![basic-auth](./assets/basic-auth.png)

这个就是 Basic Auth。

## 如何永久封禁 IP

请使用 ![IP 黑名单](./module/ip-address-blocker.md)。
