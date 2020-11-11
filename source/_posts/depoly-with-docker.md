---
title: 使用 Docker 部署 HoshinoBot 与 yobot
date: 2020-11-08T12:00:00.000Z
cover: /images/banner-shizuru.jpg
---

## 前言

### 什么是 Docker

Docker 是一种容器方案，将多个运行环境分离，就像虚拟机一样，但原理不同。容器比虚拟机更轻便，只将环境隔离并不需要虚拟硬件，性能损耗极小。

### 为什么要用 Docker

方便部署、方便迁移、干净、安全。

## 准备工作

- 一个服务器（家庭、学校、公司的宽带一般没有公网不建议使用）
- 一个域名（如果服务器在大陆区域，那么域名必须备案。港澳台与境外服务器可以不用备案）
- 将一个域名指向你的服务器（建议使用一个二级域名）

## 部署教程

（以下教程均以 Linux 服务器为例，新手建议选择 Ubuntu 最新版）

### 安装必要软件

#### 安装 Docker

```bash
# 下载安装脚本
# 此方法适用于 `CentOS 7` `Debian 9/10` `Fedora 30/31` `Ubuntu 16/18/20`，其他系统请自行搜索方法手动安装
curl -fsSL https://get.docker.com -o get-docker.sh

# 安装 docker
sudo sh get-docker.sh
# 国内服务器可改用下面这个指令，即可选择国内源下载
# sudo sh get-docker.sh --mirror Aliyun

# 安装完成，删除安装脚本
rm get-docker.sh

# 如果使用的是非 root 用户，需要将自己加入 docker 组中，可能需要重新登录后才能获取权限
# （使用非 root 用户进行日常操作是个好习惯）
# 使用 root 的勇士可以无视下面这个步骤
sudo usermod -aG docker myname  # 将 myname 替换为自己的用户名
```

#### 安装其他工具

```bash
# Ubuntu / Debian
sudo apt install -y wget vim
# Centos / RHEL
sudo yum install -y wget vim
```

> 关于文本编辑器，我个人推荐使用更全能的 ssh 工具，在本地完成文本编辑任务。vim 适合以前没有鼠标、没有组合快捷键的年代，不过现在已经有更好的选择了。
>
> 如果要在本地修改文件，请尽量不要使用 Windows 自带的记事本，请先安装编程专用文本编辑器，比如这个：[点击下载notepad3](http://pan.yobot.win/share/Windows%E5%B7%A5%E5%85%B7/Notepad3.exe)

### 部署过程

#### 准备 Docker

创建 docker 网络，我们后面使用的多个容器将通过这个网络进行连接

```bash
docker network create qqbot
```

现在可以创建一个工作目录，存放后续的文件

```bash
mkdir -p ~/qqbot
cd ~/qqbot
# 当然你可以改成其他你喜欢的目录
```

#### 下载并导入镜像

```bash
# 下载镜像包
wget https://down.yu.al/others/qqbot/hoshino/hyg.tar.gz

# 解压，此处使用 gzip，只解压一层获取 tar 文件
gzip -d hyg.tar.gz

# 导入镜像
docker load -i hyg.tar

# 导入完成，删除镜像包
rm hyg.tar
```

此时可使用 `docker images` 指令查看已导入的镜像，此镜像包中包含了 `hoshinobot`、`yobot/yobot`、`gocqhttp`、`caddy` 四个镜像

#### 配置 HoshinoBot

```sh
# 取出 HoshinoBot 源码到当前目录
docker run --rm -v ${PWD}:/tmp/Hoshino hoshinobot mv /HoshinoBot/ /tmp/Hoshino/Hoshino

# 如果使用的是非 root 用户，修改这些归属
sudo chown -R myname Hoshino  # 将 myname 替换为自己的用户名

# 修改 HoshinoBot 配置文件
vim Hoshino/hoshino/config/__bot__.py
# 如果你不太熟悉 vim，也可以将文件取回本地后修改
```

配置文件中，需要将 `HOST` 设置为 `0.0.0.0`，`SUPERUSERS` 设置为主人的QQ号，其他部分可按注释自行编辑。

```sh
# 启动 HoshinoBot
docker run -d -v ${PWD}/Hoshino:/HoshinoBot --name hoshino --network qqbot hoshinobot
```

#### 配置 yobot

```sh
# 创建 yobot 配置文件
mkdir yobot_data
touch yobot_data/yobot_config.json

# 编辑 yobot 配置文件
vim yobot_data/yobot_config.json
```

配置文件中填写如下内容，其中的值替换为你自己的域名

```json
{
    "public_address": "https://your-own-domain-here.com/"
}
```

启动 yobot

```sh
# 启动 yobot 并将数据存放在当前目录下 yobot_data 文件夹
docker run -d -v ${PWD}/yobot_data:/yobot/yobot_data --name yobot --network qqbot yobot/yobot
```

#### 配置 gocqhttp

```sh
# 生成配置文件，并将数据存放在当前目录下 gocqhttp_data 文件夹
docker run --rm -v ${PWD}/gocqhttp_data:/data gocqhttp

# 如果使用的是非 root 用户，修改这些归属
sudo chown -R myname gocqhttp_data  # 将 myname 替换为自己的用户名

# 修改 gocqhttp 配置文件
vim gocqhttp_data/config.json
```

这里我们需要修改如下的部分，对照下面的注释修改，请注意不要复制注释

```json
{
  "uin": 0,  // 作为机器人的 QQ 号
  "password": "",  // 作为机器人的 QQ 密码
  "encrypt_password": false,
  "password_encrypted": "",
  "enable_db": false,
  "access_token": "",
  "relogin": {
    "enabled": true,
    "relogin_delay": 3,
    "max_relogin_times": 0
  },
  "_rate_limit": {
    "enabled": false,
    "frequency": 1,
    "bucket_size": 1
  },
  "post_message_format": "string",
  "ignore_invalid_cqcode": false,
  "force_fragmented": true,
  "heartbeat_interval": 5,
  "http_config": {
    "enabled": false
  },
  "ws_config": {
    "enabled": false
  },
  "web_ui": {
    "enabled": false
  },
  "ws_reverse_servers": [  // 这里是与bot连接的部分
    // 第一个连接，连接到 hoshinobot
    {
      "enabled": true,
      "reverse_url": "ws://hoshino:8080/ws/",  // 可以填写容器名
      "reverse_reconnect_interval": 3000
    },
    // 第二个连接，连接到 yobot
    {
      "enabled": true,
      "reverse_url": "ws://yobot:9222/ws/",
      "reverse_reconnect_interval": 3000
    }
  ]
}
```

修改完毕，启动 gocqhttp

```sh
# 启动 gocqhttp 并将数据存放在当前目录下 gocqhttp_data 文件夹
# 注意这里使用的是 `-it` 因为可能会出现登录验证需要交互处理
docker run -it -v ${PWD}/gocqhttp_data:/data --name gocqhttp --network qqbot gocqhttp
# 启动后，如果出现登录验证，请按照提示进行验证。
```

登录成功后，按下连续组合键 `ctrl-P , ctrl-Q`，将此容器挂到后台

至此，你的 bot 已经可以开始对话了，尝试发送 `在？`（验证hoshinobot） 或 `version`（验证yobot）

#### 配置网络服务器

为了访问 yobot 网页，我们需要配置网络服务器

如果你已经安装过 `nginx`，请阅读下方 nginx 部分  
如果你尚未安装 `nginx` 或其他网络服务器，请阅读下方 caddy 部分

##### Nginx 反向代理

请事先准备好 SSL 证书（不会申请的话，建议换后面的 caddy 自动申请）

如果你的 Nginx 不在容器内，则无法自动获取 yobot 地址，需要手动查询地址，记下此 IP 填写到 nginx 配置文件中

```sh
# 查看 yobot 容器的 IP
docker inspect yobot | grep IPAddress
```

请在已有的 nginx 配置文件中加入以下内容

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name example.com;  # 此处修改为你的域名
    return 301 https://$host$request_uri;  # 将 http 请求重定向至 https
}
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    server_name example.com;  # 此处修改为你的域名
    ssl_certificate /home/www/ssl/ssl_certificate.crt;  # 此处修改为你的证书路径
    ssl_certificate_key /home/www/ssl/ssl_certificate.key;  # 此处修改为你的私钥路径

    # 一些 SSL 安全参数，可以视情况修改
    ssl_session_timeout 1d;
    ssl_session_cache shared:MozSSL:1m;  # about 4000 sessions
    ssl_session_tickets off;

    # curl https://ssl-config.mozilla.org/ffdhe2048.txt > /path/to/dhparam
    # ssl_dhparam /path/to/dhparam;

    # intermediate configuration
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers off;

    # HSTS (ngx_http_headers_module is required) (63072000 seconds)
    add_header Strict-Transport-Security "max-age=63072000" always;

    # OCSP stapling
    ssl_stapling on;
    ssl_stapling_verify on;

    # 反向代理

    location /
    {
        proxy_pass http://yobot:9222;  # 反向代理，如果你的 Nginx 不在容器中，请将此 `yobot` 替换为刚刚查询到的 IP 地址
        proxy_set_header X-Real-IP $remote_addr;  # 传递用户IP
    }

    # 阻止 cqhttp 接口被访问
    location /ws/ {
        deny all;
    }
}
```

重新加载 nginx 配置

```sh
nginx -s reload
```

##### Caddy 反向代理

请先将域名指向你的服务器，否则无法自动申请证书

caddy 使用非常简单，只需创建一个 `Caddyfile`（没有扩展名），填写如下内容

```caddyfile
example.com {  # 此处修改为你的域名

    respond /ws/* "Forbidden" 403 {
        close
    }

    reverse_proxy * http://yobot:9222 {
        header_up +X-Real-IP {remote}
    }
}
```

使用 docker 启动 caddy

```sh
docker run -d -v ${PWD}/Caddyfile:/etc/caddy/Caddyfile --name caddy --network qqbot -p 80:80 -p 443:443 caddy
```

现在打开浏览器访问你的域名，就能看到 yobot 主页了

### 添加更多 Hoshino 插件

编写中……
