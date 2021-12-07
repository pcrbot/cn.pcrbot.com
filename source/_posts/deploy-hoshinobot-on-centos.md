---
title: CentOS 下安装 HoshinoBot 和 yobot
author: 7loli
date: 2021-12-02 17:00:00
cover: /images/banner-pudding.jpg
---

## 1 创建纯净版的 centos 7.6 服务器
**如果已有相应服务器，直接跳过这步到 2**

长时间使用请**自行寻找合适的服务器**，教程里使用阿里云新用户（需要绑定手机号）白嫖 1 个月的轻量级应用服务器。

[点击这里快速白嫖](https://ecs-buy.aliyun.com/trial)，配置选择如下：
* 业务场景：选择第四项 - 通用推荐
* 使用身份：选择第三项 - ~~24岁是~~ 学生
* 产品规格：1 核 2 Gib (绝对够用了，别贪，**选高配置然后忘记取消续费就哭去吧**)
* 操作系统：**CentOS 7.6**
* 预装应用：不选择
* 网络带宽: 1M
* 云盘大小: 40 GiB
* 其他默认，勾选同意协议-免费试用
* 创建完成后跳转管理控制台，选择远程连接

后续找不到控制台入口的话，[点击这里](https://homenew.console.aliyun.com/home/dashboard/ProductAndService)并搜索 ECS，会弹出云服务器ECS控制台入口。

~~新版的远程连接简直反人类：~~ 左侧边栏选择 实例与镜像-实例 ，在实例最右侧 操作-更多-密码/密钥-重置实例密码，然后重启实例后：操作-远程连接-Workbench远程连接-立即登录，输入重置的密码即可跳转至控制页面。


## 2 系统变量配置
如果已有 centos 7.6 服务器，只需要下面这些简洁的命令行，**按步骤一条一条的** 复制粘贴回车即可，# 开头为注释部分不需要复制。
### 2.1 更新系统环境
```bash
yum -y update

yum -y groupinstall "Development tools"

yum -y install wget zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc* libffi-devel make git vim screen
# ffmpeg 为发图片必备组件
yum install ffmpeg ffmpeg-devel

```

### 2.2 安装 python3
默认使用 python 3.7.1，**其他版本未经测试**(~~骗你的我试了 3.9 不能同时运行 yobot，需要修改源码~~)
```bash
# 使用华为镜像下载 python3.7.1
wget https://mirrors.huaweicloud.com/python/3.7.1/Python-3.7.1.tgz

tar xf Python-3.7.1.tgz

cd Python-3.7.1

./configure

make&&make install

pip3 install --upgrade pip
```

## 3 安装 go-cqhttp
go-cqhttp 只负责模拟登录 QQ 收发消息，消息处理逻辑则由 **yobot** 和 **HoshinoBot** 决定。

此教程下的 yobot 和 HoshinoBot 是分离的，即可以根据需求 **只安装 yobot 或 HoshinoBot** 。

```bash
/usr/local/bin/pip3 install --upgrade pip
```

## 3 安装 go-cqhttp
```
# 创建并进入 go-cqhttp 目录
cd ~&&mkdir go-cqhttp&&cd go-cqhttp
# 下载最新版的压缩包
wget https://github.com/Mrs4s/go-cqhttp/releases/download/v1.0.0-beta8-fix1/go-cqhttp_linux_amd64.tar.gz

tar xf go-cqhttp_linux_amd64.tar.gz

chmod +x go-cqhttp

./go-cqhttp
```
初次运行之后会提示选择通信方式，输入数字 **3** 回车两次即可，此时会生成 config.yml 配置文件。

这里我们备份配置文件后并新建 config.yml（主要去掉给开发人员看的注释）：
```bash
# 备份配置文件
mv config.yml config.yml.bak
# 执行下面的指令后点击 i 进入 insert/插入 模式
vi config.yml
# 重要
# 插入内容看下面
```

复制并粘贴以下内容（建议同时修改 **uin** QQ 号和 **password** 密码）：
```yml
account:
  uin: 1233456 # 你的 QQ 账号
  password: '' # 密码为空时使用扫码登录
  encrypt: false
  status: 0
  relogin:
    delay: 3
    interval: 3
    max-times: 0    
  use-sso-address: true

heartbeat:
  interval: 5

message:
  post-format: string
  ignore-invalid-cqcode: false
  # 这里修改成了 true
  force-fragment: true
  fix-url: false
  proxy-rewrite: ''
  report-self-message: false
  remove-reply-at: false
  extra-reply-data: false
  skip-mime-scan: false

output:
  log-level: warn
  log-aging: 15
  log-force-new: true
  log-colorful: true
  debug: false

default-middlewares: &default
  access-token: ''
  filter: ''
  rate-limit:
    enabled: false
    frequency: 1
    bucket: 1

database:
  leveldb:
    enable: true
  cache:
    image: data/image.db
    video: data/video.db

servers:
  # 反向WS设置
  # 8080 为 HoshinoBot 默认端口号
  - ws-reverse:
      universal: ws://127.0.0.1:8080/ws/
      api: ""
      event: ""
      reconnect-interval: 3000
      middlewares:
        <<: *default # 引用默认中间件
  # 9222 为 yobot 默认端口号
  - ws-reverse:
      universal: ws://127.0.0.1:9222/ws/
      api: ""
      event: ""
      reconnect-interval: 3000
      middlewares:
        <<: *default # 引用默认中间件
```
**单击 ESC后，英文模式下按住 `shift` + `:` ，输入 `wq` ，回车即可完成保存**

修改完相应的账号密码，输入 `nohup ./go-cqhttp &` 即可后台运行 go-cqhttp，默认输出日志文件为工作目录下的 `nohup.out` 。

初次使用 go-cqhttp 时，可能会由于异地登录等问题无法群发消息或图片，在服务器上放置一段时间即可（至少七天以上？）。

**新版已实现验证码登录，记得把命令行界面调大点不然显示不全**

## 4 安装 HoshinoBot

```bash

cd ~

git clone https://github.com/Ice-Cirno/HoshinoBot.git

cd HoshinoBot

pip3 install -r requirements.txt

cp -r hoshino/config_example/ hoshino/config/

# 这一步启用或关闭模组
vi hoshino/config/__bot__.py

# 后台运行 HoshinoBot
nohup python3 run.py &
```

程序运行后生成的数据库和配置文件处于 `~/.hoshino/service_config/` 下

更多的 HoshinoBot 模组，[点击查看 HoshinoBot 作品索引](https://github.com/pcrbot/HoshinoBot-plugins-index)


## 5 安装 yobot

### 5.1 下载并安装 yobot
yobot 可独立进行安装，不依赖于 HoshinoBot，注意配置好步骤 3 中的反向 ws 端口号即可（默认为9222）。


```bash
cd ~

git clone https://github.com/yuudi/yobot.git

cd yobot/src/client

pip3 install -r requirements.txt

python3 main.py

# 后台运行 yobot
nohup sh yobotg.sh &
```
### 5.2 访问网页版 yobot
在服务器安全组中开启端口 9222，具体方法自行百度或[参照此文](https://developer.aliyun.com/article/767328)

之后在浏览器中输入公网 ip:9222 即可访问 yobot 网页端，更多指令，[参照 yobot 官网](https://yobot.win/)
