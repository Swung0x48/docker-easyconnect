# docker-easyconnect

让深信服开发的**非自由**的 EasyConnect 代理软件运行在 docker 中，并开放 Socks5 供宿主机连接以使用代理。

这个 image 基于 EasyConnect 官方“Linux”版的 deb 包。

望批评、指正。欢迎提交 issue、PR，包括但不仅限于 bug、各种疑问、代码和文档（最近有点语无伦次）的改进。

### 从 Dockerfile 构建

带 VNC 服务端：

```
git clone https://github.com/Swung0x48/docker-easyconnect.git
cd docker-easyconnect
EC_VER=7.6.3  # 此变量填写 ec_urls 文件夹中的版本，`7.6.3`或`7.6.7`，`7.6.7` 未经测试。
docker image build --build-arg EC_URL=$(cat ec_urls/${EC_VER}.txt) --tag Swung0x48/docker-easyconnect -f Dockerfile .
```

### 修改环境变量文件 `.env`

请将 `.env` 文件中 `PASSWORD` 字段改为你自己的密码，作为 VNC 登录密码。最多8位。 

### 首次启动容器

```
docker run --device /dev/net/tun --cap-add NET_ADMIN --net host  -ti --env-file=.env -v $HOME/.ecdata:/root -p 5901:5901 -p 1080:1080 Swung0x48/docker-easyconnect
```

使用任意 VNC 客户端登录 `127.0.0.1:5901`， 输出 `PASSWORD` 指定的密码。在 VNC 客户端界面登录 EasyConnect。注意选择 `Remember Password` 。

### 配置、登陆信息持久化

首次登录容器后会在 `$HOME/.ecdata:/root` 生成配置文件。

### 配置自动登录和自动断线重连

首次登录成功后， 用 `docker stop <hash>` ( `hash` 可通过 `docker ps` 命令查看 ) 命令 关闭容器。

修改 `.env` 文件，修改 `AUTOLOGIN=` 为 `AUTOLOGIN=1`。

使用 `docker run <hash>` 命令重新启动 docker 容器。 此时的容器应该已经可以自动登录和断线重连。

### Socks5

EasyConnect 创建 `tun0` 后，Socks5 代理会在容器的 `1080` 端口开启。

非常感谢上游 repo [Hagb/docker-easyconnect](https://github.com/Hagb/docker-easyconnect) ！