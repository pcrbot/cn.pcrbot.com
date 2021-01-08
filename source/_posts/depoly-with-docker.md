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
- 将一到两个域名指向你的服务器（yobot 使用一个域名，HoshinoBot 的第三方插件可能需要一个域名）

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
sudo usermod -aG docker $(whoami)
```

> 关于文本编辑器，我个人推荐使用更全能的 ssh 工具，在本地完成文本编辑任务。vim 适合以前没有鼠标、没有组合快捷键的年代，不过现在已经有更好的选择了。
>
> 如果要在本地修改文件，请尽量不要使用 Windows 自带的记事本，请先安装编程专用文本编辑器，比如这个：[点击下载notepad3](http://pan.yobot.win/share/Windows%E5%B7%A5%E5%85%B7/Notepad3.exe)

### 部署过程

#### 准备一个随机密钥

你可以用任何方式生成一个随机字符串，比如

```bash
openssl rand -base64 12
```

请记下这个密钥，后面会用到

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

拉取镜像

```bash
docker pull pcrbot/hoshinobot
docker pull pcrbot/gocqhttp:ffmpeg
docker pull yobot/yobot:pypy
docker pull caddy
```

如果国内服务器连接 Docker 速度较慢，可以改用下面的方法

[阿里云镜像加速教程](https://help.aliyun.com/document_detail/60750.html)（目前限额免费）

#### 配置 HoshinoBot

```bash
# 取出 HoshinoBot 源码到当前目录
docker run --rm \
           -v ${PWD}:/tmp/Hoshino \
           pcrbot/hoshinobot \
           mv /HoshinoBot/ /tmp/Hoshino/Hoshino

# 如果使用的是非 root 用户，需要修改这些归属
# root 用户可跳过此句
sudo chown -R $(whoami) Hoshino

# 修改 HoshinoBot 配置文件
vim Hoshino/hoshino/config/__bot__.py
# 如果你不太熟悉 vim，也可以将文件取回本地后修改
```

配置文件中，需要修改下面三个字段，其他部分可按注释自行编辑。

```python
HOST = '0.0.0.0'
ACCESS_TOKEN = 'xxxxxx'  # 此处填写刚才生成的密钥
SUPERUSERS = ['000000']  # 此处填写主人的QQ号
```

```bash
# 启动 HoshinoBot
docker run -d \
           -v ${PWD}/Hoshino:/HoshinoBot \
           -v ${PWD}/Hoshino_data:/root/.hoshino \
           --name hoshino \
           --network qqbot \
           pcrbot/hoshinobot
```

#### 配置 yobot

启动 yobot

```bash
# 设置一些属性
export YOBOT_PUBLIC_ADDRESS="https://your-own-domain-here.com/"  # 将域名改为你自己的域名
export YOBOT_ACCESS_TOKEN="xxxxxx"  # 此处填写刚才生成的密钥

# 启动 yobot 并将数据存放在当前目录下 yobot_data 文件夹
docker run -d \
           -v ${PWD}/yobot_data:/yobot/yobot_data \
           -e YOBOT_PUBLIC_ADDRESS \
           -e YOBOT_ACCESS_TOKEN \
           --name yobot \
           --network qqbot \
           yobot/yobot:pypy
```

#### 配置 gocqhttp

```bash
# 生成配置文件，并将数据存放在当前目录下 gocqhttp_data 文件夹
docker run --rm \
           -v ${PWD}/gocqhttp_data:/data \
           pcrbot/gocqhttp:ffmpeg

# 如果使用的是非 root 用户，修改这些归属
# root 用户可跳过此句
sudo chown -R $(whoami) gocqhttp_data

# 修改 gocqhttp 配置文件
vim gocqhttp_data/config.hjson
```

这里我们需要修改如下的部分，对照下面的注释修改

> `go-cqhttp` 使用了 `hjson` 格式的配置文件，所以格式更加随意，不必感到困惑

```json
{
  "uin": 0,  // 作为机器人的 QQ 号
  "password": "",  // 作为机器人的 QQ 密码
  "encrypt_password": false,
  "password_encrypted": "",
  "enable_db": false,
  "access_token": "xxxxxx",  // 此处填写刚才生成的密钥
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
  "use_sso_address": false,
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
      "reverse_url": "ws://hoshino:8080/ws/",  // ip 用容器名即可
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

```bash
# 启动 gocqhttp 并将数据存放在当前目录下 gocqhttp_data 文件夹，同时挂载整个 /HoshinoBot 以共享静态资源
# 注意这里使用的是 `-it` 因为可能会出现登录验证需要交互处理
docker run -it \
           -v ${PWD}/gocqhttp_data:/data \
           -v ${PWD}/Hoshino:/HoshinoBot \
           --name gocqhttp \
           --network qqbot \
           pcrbot/gocqhttp:ffmpeg
# 启动后，如果出现登录验证，请按照提示进行验证。
```

登录成功后，按下连续组合键 `ctrl-P , ctrl-Q`，将此容器挂到后台

至此，你的 bot 已经可以开始对话了，尝试发送 `在？`（验证hoshinobot） 或 `version`（验证yobot）

#### 配置网络服务器

为了访问 yobot 网页，我们需要配置网络服务器

如果你已经安装过 `nginx`，请阅读下方 nginx 部分  
如果你尚未安装 `nginx` 或其他网络服务器，请阅读下方 caddy 部分

这里的例子只给出 yobot 的代理方法，如需代理 HoshinoBot 则用换一个域名再操作一次即可。

##### Nginx 反向代理

如果你已经安装过 `nginx`，请参考此部分，并忽略下方 caddy 部分

请事先准备好 SSL 证书

如果你的 Nginx 不在容器内，则无法自动获取 yobot 地址，需要手动查询地址，记下此 IP 填写到 nginx 配置文件中

```bash
# 查看 yobot 容器的 ip
docker inspect yobot | grep IPAddress
# 记下这个 ip，稍后会使用
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
        proxy_pass http://yobot:9222;  # 反向代理，请将此 `yobot` 替换为刚刚查询到的 IP 地址
        proxy_set_header X-Real-IP $remote_addr;  # 传递用户IP
    }

    # 阻止 cqhttp 接口被访问
    location /ws/ {
        deny all;
    }

    # 日志记录
    # access_log /var/log/nginx/yobot.log;
    # error_log /var/log/nginx/yobot.error.log;
}
```

重新加载 nginx 配置

```bash
nginx -s reload
```

##### Caddy 反向代理

请先将域名指向你的服务器，否则无法自动申请证书

caddy 使用非常简单，只需创建一个 `Caddyfile`（没有扩展名），填写如下内容

```bash
# 使用 vim 创建 Caddyfile，也可以用其他你喜欢的方式
vim ./Caddyfile
```

编写内容如下

```caddyfile
example.com {  # 此处修改为你的域名

    respond /ws/* "Forbidden" 403 {
        close
    }

    reverse_proxy * http://yobot:9222 {
        header_up +X-Real-IP {remote_host}
    }
}
```

使用 docker 启动 caddy

```bash
docker run -d \
           -v ${PWD}/Caddyfile:/etc/caddy/Caddyfile \
           --name caddy \
           --network qqbot \
           -p 80:80 \
           -p 443:443 \
           caddy
```

现在打开浏览器访问你的域名，就能看到 yobot 主页了

### 添加更多 Hoshino 插件

HoshinoBot 有很多社区插件，可以在[插件索引](https://github.com/pcrbot/HoshinoBot-plugins-index/blob/master/README.md)中查看，安装插件方法请参考插件自身的介绍页。

注意：在安装依赖时，需要使用 `pip3`，我们需要改为 `docker exec hoshinobot pip3`

（可选）我们也可以为这个命令设置一个别名方便记忆

```bash
# 为这一长串命令设置一个临时别名
alias hpip='docker exec hoshinobot pip3'
```

（可选）将其写入 `~/.bashrc` 结尾，即可永久生效

```bash
# 为这一长串命令设置一个永久别名
echo "alias hpip='docker exec hoshinobot pip3'" >>~/.bashrc
```

（可选）安装 `hsn` 插件管理器

> 更多信息请参考：<https://github.com/pcrbot/hsn>

```bash
# 下载 hsn
wget https://github.com/pcrbot/hsn/releases/download/v0.1.4/hsn-v0.1.4-linux-amd64.tar.gz
# 国内用户可改为 https://download.fastgit.org/pcrbot/hsn/releases/download/v0.1.4/hsn-v0.1.4-linux-amd64.tar.gz

# 解压
tar zxvf hsn-v0.1.4-linux-amd64.tar.gz

# 删除压缩包
rm hsn-v0.1.4-linux-amd64.tar.gz

# 给与运行权限
chmod +x hsn

# 移动至运行目录
mv hsn /usr/bin/

# 配置
hsn set --path=${PWD}/Hoshino
hsn set -i="https://github.com"
hsn set --pip="docker exec hoshinobot pip3"
# 国内用户可改为执行下面这些配置
# hsn set --path=${PWD}/Hoshino
# hsn set -i="https://hub.fastgit.org"
# hsn set --pip="docker exec hoshinobot pip3 -i https://mirrors.aliyun.com/pypi/simple/"
```
