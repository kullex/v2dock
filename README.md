# v2dock

> 使用 docker-compose 同时部署一个 v2ray 和一个 v2ray + WebSocket + TLS 服务端

### 食用前自备

1. 域名一枚
2. Cloudflare CDN 账户一个
3. 独立 VPS一台

### 服务端配置

#### 1. 安装 docker、docker-compose 和 Git

如果你的 VPS 使用的是 Debian 系列的系统，可以执行下面的命令一键安装：

```shell
sudo apt install docker.io docker-compose git
```

其他系统的话自行解决。

#### 2. 克隆项目并进入项目目录

```shell
git clone https://github.com/kullex/v2dock.git && cd v2dock
```

#### 3. 复制环境变量文件

```shell
cp ./env-sample ./.env
vim ./.env
```

#### 4. 配置环境变量

```shell
### SSL ###################################################

CLOUDFLARE_EMAIL=你的 Cloudflare 登陆邮箱
CLOUDFLARE_API_KEY=你的 Cloudflare API Key

### DOMAIN ################################################

DOMAIN=你要绑定的域名

### V2Ray #################################################

V2RAY_PORT=v2ray 端口号，不使用 ws + tls 访问，可以做后置代理

V2RAY_BACKEND_UUID=做后置用时验证的 UUID，配合 V2RAY_PORT + IP 食用
V2RAY_FRONTEND_UUID=做前置用时验证的 UUID，配合 域名 + ws + tls 食用

V2RAY_PATH=随便一串字符串，WebSocket 路径
```

  #### 5. OK，开始吧

启动服务：

```shell
sudo docker-compose up -d
```

停止的话：

```shell
sudo docker-compose stop
```

如果修改了配置，需要重新 Build

```shell
sudo docker-compose up -d --build
```

### 客户端配置

#### 不使用 ws + tls 直连参考配置：

```json
{
  "inbounds": [
    {
      "listen": "127.0.0.1",
      "port": 1080,
      "protocol": "socks",
      "tag": "socksinbound",
      "settings": {
        "auth": "noauth",
        "udp": false,
        "ip": "127.0.0.1"
      }
    },
    {
      "listen": "127.0.0.1",
      "port": 8001,
      "protocol": "http",
      "tag": "httpinbound",
      "settings": {
        "timeout": 0
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "服务器地址或者域名",
            "users": [
              {
                "id": "后置 v2ray UUID",
                "alterId": 64,
                "security": "auto",
                "level": 0
              }
            ],
            "port": "v2ray 端口"
          }
        ]
      },
      "tag": "backend"
    }
  ],
  "routing": {
    "name": "all_to_main",
    "domainStrategy": "AsIs",
    "rules": [
      {
        "type": "field",
        "outboundTag": "backend",
        "port": "0-65535"
      }
    ]
  }
}
```

如果你需要添加前置代理，可以参考 [《白话文教程：代理转发》](https://toutyrater.github.io/advanced/outboundproxy.html)

#### 使用 ws + tls 参考配置：

```json
{
  "inbounds": [
    {
      "listen": "127.0.0.1",
      "port": 1080,
      "protocol": "socks",
      "tag": "socksinbound",
      "settings": {
        "auth": "noauth",
        "udp": false,
        "ip": "127.0.0.1"
      }
    },
    {
      "listen": "127.0.0.1",
      "port": 8001,
      "protocol": "http",
      "tag": "httpinbound",
      "settings": {
        "timeout": 0
      }
    }
  ],
  "outbounds": [
    {
      "sendThrough": "0.0.0.0",
      "mux": {
        "enabled": false,
        "concurrency": 8
      },
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "服务器域名",
            "users": [
              {
                "id": "作为前置的 UUID",
                "alterId": 64,
                "security": "auto",
                "level": 0
              }
            ],
            "port": 443
          }
        ]
      },
      "tag": "frontend",
      "streamSettings": {
        "wsSettings": {
          "path": "你配置的 V2RAY_PATH",
          "headers": {}
        },
        "security": "tls",
        "network": "ws"
      }
    }
  ],
  "routing": {
    "name": "all_to_main",
    "domainStrategy": "AsIs",
    "rules": [
      {
        "type": "field",
        "outboundTag": "frontend",
        "port": "0-65535"
      }
    ]
  }
}
```

