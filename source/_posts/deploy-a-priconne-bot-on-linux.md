---
title: Linux 下部署一个公主连结 qq 群聊机器人
date: 2020-12-02T12:00:00.000Z
cover: /images/banner-little-lyrical.jpg
author: 地河
---

## 开篇

目前, 为公主连结玩家定制的 qq 群聊机器人 ( 以 [yobot](https://github.com/pcrbot/yobot) 和 [HoshinoBot](https://github.com/Ice-Cirno/HoshinoBot) 最具代表性 ) 已有非常多用户。它们可以提供一些诸如 公会战管理, 竞技场查询 等功能的应答, 为公主连结玩家提供了非常有用的帮助。

事实上, 这两个 bot 的代码均已开源, 任何人都可以自行搭建并根据自己的实际需要进行定制。

当然, 这需要您有一点基础, 您需要有

- 初中以上英语水平
- 熟悉搜索引擎 ( 单指谷歌 ) 的使用
- 基础的 shell 交互能力
- git 基础知识
- 知道如何使用 vim 编辑器

( 注 : 以上能力按照重要性依次递减 )

若您具备以上能力 ( 至少掌握前两条 ) , 您就可以尝试部署一个机器人, 以便在您的群组中提供有趣或者实用的应答。

### 为什么建议自己部署 bot

**[yobot](https://github.com/pcrbot/yobot) 和 [HoshinoBot](https://github.com/Ice-Cirno/HoshinoBot) 均以 [GPL 3.0](http://www.gnu.org/licenses/gpl-3.0.html) 协议开源。**

开源即是可以免费使用, 但开源不等同于免费, 开源让原本封闭的独裁的技术公开, 让更多的人有机会阅读源代码并从中学习, 包括源代码里面的思想、思路、亦或是实现方式。可以更容易发现 bug, 以及对开源项目提出改进意见, 进而使得一个开源项目更加完善。

---

此处并非要求您一定要为社区贡献代码成为 Contributor, 您使用这些项目, 您和您公会的朋友就可以使用实用或者有趣的功能, bot 的作者 [yuudi](https://github.com/yuudi) 和 [Ice-Cirno](https://github.com/Ice-Cirno) 也会感到开心。

---

虽然您也可以通过购买一些出租 bot 的人, 他们有的是希望有人能一起分担服务器费用, 以便获得更好的学习和开发环境, 也有带着纯粹的商业目的, 利用别人代码, 满足自己的钱包的现象。

大部分用户都对 服务器的配置 以及 各种 bot 的区别, 事实上, 一般的服务商提供的最低配置的服务器 ( 1h1g1m ) 都足以运行 bot 并为上百群提供应答, 这样算来, 花 9.5 r 买一台服务器租给 200 个群, 确实是发家致富的好手段 ( 笑 )。  
我们知道, [yobot](https://github.com/pcrbot/yobot) 和 [HoshinoBot](https://github.com/Ice-Cirno/HoshinoBot) 是出名的 公主连结群聊 bot , 那么各种 xcwbot , 可可萝 bot , 又是什么呢 ? 无非是 [yobot](https://github.com/pcrbot/yobot) 或 [HoshinoBot](https://github.com/Ice-Cirno/HoshinoBot) 换皮罢了, 部署一个 bot , 并根据自己的喜好把名字改成 xcw , 或者是 可可萝, 然后 xcwbot 或者是 可可萝 bot 就成了, 不要相信商家们 `定制 bot` 的噱头, 无论是 yobot 还是 HoshinoBot , 在现阶段搭建都是非常非常简单的, 而 定制 bot 的应该是提出需求, 并由接定制的商户将功能写好并对客户提交代码, 代搭和定制具有本质的区别。

- ( 注: 并非所有的 xcwbot 都是换皮, qq 平台存在原创非换皮闭源 xcwbot )
- ( 注: 并非所有的可可萝 bot 都是换皮 bot , 存在 已经弃坑的 kkl-android 项目和以 HoshinoBot 为基础开发的一个全平台的 kokkorobot, 但暂无将这两个项目用于商业行为的现象 )

相信大家都能对这种行为做出合理的判断。

此处, 由于某人的行为实在过火, 才有了这样的一段对话 :

Ice-Cirno : 我只是不想让我自己的代码帮别人数钱罢了。

我 : 可以建一个组织, 仓库仅组织可见。

Ice-Cirno : 挺麻烦的, 仓库早被 fork 无数次了。

我 : 或者在 README 里加上一句...

Ice-Cirno : 没意思, 都是防君子不防小人的。  
嘛...只要想保持开放, 是阻止不了这种恰饭人的。  
况且现在也无法转为封闭。

Ice-Cirno : 害, pcr 号一卖, repo 直接 archive  
不盯着钱, 每天都能开开心心, 用 bot 转发画师、字幕组, 不香吗。  
多投几篇论文，奖金不比这多 ?

此处单指某一直恰钱未对社区有过任何实际贡献的大户, 不点名表扬, 但是请好好想想为什么搞商业化的人这么多却只有你一个被点名。

### 预备

本教程将会帮助您部署一个 bot , 如果您确定使用这个教程来部署 bot , 意味着您将使用以下项目的 源代码 / 二进制文件 :

- [yobot](https://github.com/pcrbot/yobot/)
- [HoshinoBot](https://github.com/Ice-Cirno/HoshinoBot/)
- [go-cqhttp](https://github.com/Mrs4s/go-cqhttp/)
- [cq-picsearcher-bot](https://github.com/Tsuk1ko/cq-picsearcher-bot/) ( 可能 )

请切勿忘记这一点。

## 准备工作

您需一台服务器, 服务器价格昂贵, 但您只是想用来部署机器人的话,您可以租用服务器厂商的 虚拟服务器 ( vps ) 。当前, 境内的服务器大厂多数有学生优惠, 您可以在完成学生验证之后以超实惠的价格买到一台虚拟服务器。如果您不是学生, 您可尝试去某宝当假学生。

目前最划算的境内服务器是:阿里云轻量应用服务器, 配置为 1h2g5m
价格为 9.5 r / 月 其他性价比较高的服务器有:阿里云 ECS ( 学生优惠 ) , 腾讯云学生优惠, 配置为 1h2g1m 。
我的个人感受是腾讯云的配置高过阿里云。

境外服务器推荐: 您可以选 [谷歌云](https://console.cloud.google.com/?hl=zh-CN) ,[甲骨文云](https://www.oracle.com/cn/cloud/free/) ( 完成信用卡认证后免费 ), [vultr](https://vultr.com/) ( 非常便宜, cpu 有点拉跨, 总体而言性价比算高 ), [搬瓦工](https://bandwagonhost.com/index.php) (涨价后不香了, 但是支付方便, 另外坑多) , [oneprivide](https://oneprovider.com/) ( 便宜, 支付方便, 较划算, 客服有点糟糕 )。[linode](https://www.linode.com/) (便宜, 挺好, 不支持极为先进的支付宝, 必须信用卡)。

### 选择服务器的系统镜像

本教程测试环境是 CentOS 7.6 和 Ubuntu 18.04 , 未在 Debian 上进行测试, 理论上和 Ubuntu 应该一样。  
因此我建议您使用 CentOS 7.\* 或者 Ubuntu 18.04 ( 20.04 也可行) 。

## 开始部署

第一步, 您需要连接上您的服务器, 本教程指令均不出现 `sudo` , 为了不出现 `Permission denied` 的错误, 请直接在 root 账户执行指令。

root 账户是 Linux 系统 ( 或者说是 Unix 亦或是类 Unix 系统 ) 超级管理员账户, 权限大于 Windows 系统中的 `Administrator` 账户, 如果操作不当, 对服务器来说是非常危险的, 有经验的 Linux 用户会建议您新建一个低权限账户减少发生意外时的损失。

使用 `root` 账户部署 ? 这么说, 你很勇啊。  
当然啦, 我超勇的。相信我, 只要不用 root 账户进行不当操作, 那应该是没问题的... ( 大概 )

本教程默认您使用的是 root 账户, 您也可以创建一个低权限账户并在每个指令前加上 `sudo` 完成部署。

### 安装运行所需环境

下列指令请一条一条复制, 一段一段复制可能会出现问题。

```bash
#CentOS 用户请执行
yum -y update
yum -y groupinstall "Development tools"
yum -y install wget zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc* libffi-devel make git vim screen

#Debian 、Ubuntu 用户请执行
apt -y update
apt -y install build-essential
apt -y install -y make libssl-dev zlib1g-dev libbz2-dev libpcre3 libpcre3-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libperl-dev libncursesw5-dev xz-utils tk-dev zlib1g libffi-dev liblzma-dev screen git vim openssl gcc
```

### 安装 Python3.8

```bash
#Debian 、Ubuntu 用户请先执行
rm -rf /usr/bin/python3
rm -rf /usr/bin/pip3
#此处是删除 Debian 或者 Ubuntu 自带的 Python3 软链, 以便为编译安装的 Python3.8 自动添加进环境变量, 并非卸载服务器自带的 Python3
#CentOS 7.x 不自带 Python3 所以无需此操作

#此处开始安装 Python3.8
wget https://www.python.org/ftp/python/3.8.6/Python-3.8.6.tgz
#境内机器请将这一步的链接替换成 http://repo.huaweicloud.com/python/3.8.6/Python-3.8.6.tgz
tar xf Python-3.8.6.tgz
#xf 可比 zxvf 快多了 ( 笑 )
cd Python-3.8.6

#↓↓↓ CentOS 用户请执行 ↓↓↓
./configure
#↑↑↑ Ubuntu 和 Debian 用户请无视 ↑↑↑

#↓↓↓ Ubuntu 和 Debian 用户请执行 ↓↓↓
./configure  --enable-optimizations
#↑↑↑ CentOS 用户请无视 ↑↑↑

make&&make install

pip3 install --upgrade pip
#境内机器可以加上 -i https://pypi.tuna.tsinghua.edu.cn/simple/ 这个参数加快下载速度
```

`--enable-optimizations` 是 Python 编译优化的参数, 编译检查时加上这个参数大约能给 Python 带来 10% 的性能优化。  
如果 `gcc编译器` 版本低于 `5.4.0` , 那么加上这个参数会使 Python 编译失败, CentOS 自带的包管理工具 `yum` 不能帮您安装 `5.4.0` 以上的版本, Ubuntu 18.04 和 Debian 10 则分别自带了版本 `7.4.0` 和 `8.3.0` 的 `gcc编译器`。

您也可以自行为您的 CentOS 服务器升级 `gcc编译器` , 在编译 gcc 编译器 的时候需要大量时间, 您可自行尝试。

### 获取 go-cqhttp

go-cqhttp 是使用 go 语言对 cqhttp 协议重新实现, 并实现了很多原版 cq-http 没有实现的 api 。  
四酱老婆好棒！

**go-cqhttp 目前正在快速更新迭代, 本教程给出的链接可能不是最新版。**
您可以在 [这里](https://github.com/Mrs4s/go-cqhttp/releases) 获取到最新版的下载地址。

```bash
cd
mkdir mirai&&cd mirai
wget https://github.com/Mrs4s/go-cqhttp/releases/download/v0.9.29/go-cqhttp-v0.9.29-linux-amd64.tar.gz
tar xf go-cqhttp-v0.9.29-linux-amd64.tar.gz
./go-cqhttp
#首次运行 go-cqhttp , 会在当前目录下生成两个文件, 其中 device.json 保存设备信息, config.json 是 go-cqhttp 的配置文件
```

### go-cqhttp 的配置文件

config.json :

```json
{
    "uin": 0, // 此处填入你用来做 bot 的账号
    "password": "", // 此处填入你 bot 账号的密码
    "encrypt_password": false, // 是否对密码进行加密, 默认为否
    "password_encrypted": "", // 加密后的密码, 请勿修改此项
    "enable_db": true, // 是否开启内置数据库, 关闭后将无法使用 回复/撤回 等上下文相关接口
    "access_token": "", // 和 cqhttp 原版一样, 用于身份验证
    "relogin": { // 自动重新登陆配置
        "enabled": true, // 是否自动重新登陆, 默认为是
        "relogin_delay": 3, //重新登陆时间间隔, 单位是 秒
        "max_relogin_times": 0 // 尝试重新登陆的次数限制, 0 为无限重试
    },
    "_rate_limit": { // api 调用限制
        "enabled": false, // 是否限制 api 调用
        "frequency": 1, // 1s内能调用API的次数
        "bucket_size": 1 // 令牌桶的大小, 默认为1, 修改此值可允许一定程度内连续调用 api
    },
    "ignore_invalid_cqcode": false, // 是否忽略错误的CQ码, 当此处的值为 false 时, bot 会直接发送错误的 cq 码
    "force_fragmented": true, // 是否强制分片发送群长消息, 此项为 true 时, bot 不容易被腾讯封号 ( 有内鬼的情况则另说 )
    "heartbeat_interval": 0, // 心跳间隔时间，单位秒。小于 0 则关闭心跳，等于 0 使用默认值 ( 5秒 ), 此项用于检测机器人存活, 关闭此项容易使机器人断线
    "http_config": { // http 配置, 您部署 koishi 框架的机器人会用到
        "enabled": true,
        "host": "0.0.0.0",
        "port": 5700,
        "timeout": 0,
        "post_urls": {}
    },
    "ws_config": { // 正向 ws 配置, 您部署 cq-picsearcher-bot 时会用到
        "enabled": true,
        "host": "0.0.0.0",
        "port": 6700
    },
    "ws_reverse_servers": [ // 反向 ws 配置, 此连接方式的机器人通常能获得极高的性能
        { // 著名的 nonebot, 本教程要介绍的 HoshinoBot , yobot 均使用这种连接方式
            "enabled": false, //是否开启, 此处的值, 默认为false, 当此处的值为 true 时为开启
            "reverse_url": "ws://you_websocket_universal.server", // 反向 ws 上报地址, 填了此项, 就不会检查下面两项
            "reverse_api_url": "ws://you_websocket_api.server", // 反向 ws_api 上报地址, 当 reverse_url 字段为空的时候使用此处配置
            "reverse_event_url": "ws://you_websocket_event.server", // 反向 ws_event 上报地址, 当 reverse_url 字段为空的时候使用此处配置
            "reverse_reconnect_interval": 3000 // 断线重连时间, 单位是毫秒
        }
    ],
    "post_message_format": "string", // 上传数组还是字符串, 默认是字符串
    "debug": false, // 是否开启 debug , 此处指为 false 时 go-cqhttp 有较高性能
    "log_level": "" // 指定日志收集级别，将收集的日志单独存放到固定文件中，便于查看日志线索 当前支持 warn,error
    "web_ui": { // 网页交互界面, 等待四酱的后续开发
        "enabled": true, // 开关
        "host": "0.0.0.0", // 监听的地址
        "web_ui_port": 9999, // 端口
        "web_input": false // 网页输入
    }
}
```

device.json :

```json
{
  "protocol": 0, // 设备种类, 值为 0 代表 Android Pad, 值为 1 代表 Android 手机, 值为 2 代表 Android 手表
  "display": "xxx", // 无需考虑的配置项
  "finger_print": "xxx", // 无需考虑的配置项
  "boot_id": "xxx", // 无需考虑的配置项
  "proc_version": "xxx", // 无需考虑的配置项
  "imei": "xxx" // 无需考虑的配置项
}
```

当设备种类设定为 Android Pad 时, bot 无法接收 `group_notify` 事件、无法接收口令红包信息 ( 红包标题和运气王等 )。此时, 用于部署 bot 的账号不能在别的设备上用 QQ HD 登录。

当设备种类设定为 Android 手机 时, 功能无限制。此时, 用于部署 bot 的账号不能在别的设备上用手机 QQ 登录。

当设备种类设定为 Android 手表 时, bot 无法接收 `group_notify` 事件、无法接收口令红包信息, 也无法收取消息撤回时间。~~此时, 用于部署 bot 的账号不能在 Android 手表上登录。~~ ( 真的有人用手表吗 )

### 配置 go-cqhttp 反向 ws 配置

给出两种示例,

第一种:  
以 HoshinoBot 为例, HoshinoBot 的默认端口是 `8080` , 因此 go-cqhttp 的反向 ws 上报地址应为 `ws://127.0.0.1:8080/ws/`

```json
{
  "ws_reverse_servers": [
    {
      "enabled": true,
      "reverse_url": "ws://127.0.0.1:8080/ws/",
      "reverse_api_url": "",
      "reverse_event_url": "",
      "reverse_reconnect_interval": 3000
    }
  ]
}
```

第二种 :

```json
{
  "ws_reverse_servers": [
    {
      "enabled": true,
      "reverse_url": "",
      "reverse_api_url": "ws://127.0.0.1:8080/ws/api/",
      "reverse_event_url": "ws://127.0.0.1:8080/ws/event/",
      "reverse_reconnect_interval": 3000
    }
  ]
}
```

两种都是正确的, 任意一种即可。

### 获取 HoshinoBot

```bash
cd
git clone https://github.com/Ice-Cirno/HoshinoBot.git
cd HoshinoBot
cp -r hoshino/config_example hoshino/config

pip3 install -r requirements.txt
#境内服务器可以通过加上参数 -i https://pypi.tuna.tsinghua.edu.cn/simple/ 加快下载速度

#↓↓↓ 可选, 性能 ↓↓↓
pip3 install msgpack ujson python-Levenshtein
#境内服务器可以通过加上参数 -i https://pypi.tuna.tsinghua.edu.cn/simple/ 加快下载速度

vim hoshino/config/__bot__.py
#自定义你的机器人, 按照中文注释填好。
```

若您是在 vultr 或者其它境外厂商购买的服务器, 您的服务器可能会有中文乱码的现象, 您可以对照 [这里](https://github.com/Ice-Cirno/HoshinoBot/blob/master/hoshino/config_example/__bot__.py) 的中文注释修改您的配置。

### 获取静态资源

获取合成图片所需要的图包 :

```bash
cd ~/HoshinoBot
# wget https://drive.di.he.cn/res.tar.gz （暂时挂了）
wget https://download.yobot.win/hoshinobot/res.tar.gz
tar xf res.tar.gz
```

安装新版 jjc 查询合成图片所需要的字体 :

```bash
#↓↓↓ CentOS 请先执行 ↓↓↓
yum -y install fontconfig
#↑↑↑ Ubuntu 和 Debian 请无视 ↑↑↑

mkdir -p /usr/shared/fonts/chinese
# wget https://drive.di.he.cn/ttc.tar.gz （暂时挂了）
wget https://download.yobot.win/hoshinobot/ttc.tar.gz
tar zxvf ./ttc.tar.gz -C /usr/shared/fonts/chinese
mkfontscale
mkfontdir
```

### 测试 bot 是否搭建成功

尝试运行 bot , 看看其是否能工作。

1, 启动 `go-cqhttp` :

```bash
screen -S mirai
#创建一个新的窗口用于运行 go-cqhttp
cd ~/mirai
chmod +x go-cqhttp
#赋予执行权限
./go-cqhttp
#运行 go-cqhttp , 可能需要异地登录验证

#然后, 使用组合键 Ctrl + a ,d 挂起这个窗口
```

2, 启动 `HoshinoBot` :

```bash
screen -S hoshino
#创建一个新的窗口用于运行 HoshinoBot
cd ~/HoshinoBot
python3 run.py

#然后, 使用组合键 Ctrl + a ,d 挂起这个窗口
```

如果到这里为止一切正常, HoshinoBot 的控制台应该会输出类似于这样的一条日志 :

```yaml
[2020-09-21 22:30:45,435] 127.0.0.1:56363 GET /ws/ 1.1 101 - 114514
```

恭喜你成功搭建了 HoshinoBot , 现在立刻在群里 艾特 你的 bot 并发送一句 `kkp` 吧！

### 获取 yobot

两种安装 yobot 的方法。

#### 源码运行

首先修改 go-cqhttp 的示例, 使其向 `ws://127.0.0.1:9222/ws/` 上报事件, 下面的配置文件可供参考

```json
{
  "ws_reverse_servers": [
    {
      "enabled": true,
      "reverse_url": "ws://127.0.0.1:8080/ws/",
      "reverse_api_url": "",
      "reverse_event_url": "",
      "reverse_reconnect_interval": 3000
    },
    {
      "enabled": true,
      "reverse_url": "ws://127.0.0.1:9222/ws/",
      "reverse_api_url": "",
      "reverse_event_url": "",
      "reverse_reconnect_interval": 3000
    }
  ]
}
```

获取 yobot :

```bash
cd
git clone https://github.com/yuudi/yobot.git
cd yobot/src/client

pip3 install -r requirements.txt
#境内服务器可以通过加上参数 -i https://pypi.tuna.tsinghua.edu.cn/simple/ 加快下载速度

screen -S yobot
#创建一个新窗口用于运行 yobot

python3 main.py
sh yobotg.sh
```

私聊或群聊发送 `V` 或 `ver` 或 `version` , bot 会回复 yobot 版本。

放通端口, 使 yobot 的 web 能在 9222 端口被外部访问 :

```bash
#↓↓↓ Ubuntu 和 Debian 用户请无视 ↓↓↓
firewall-cmd --zone=public --add-port=9222/tcp --permanent
firewall-cmd --reload
#↑↑↑ Ubuntu 和 Debian 用户请无视 ↑↑↑
```

如果你是在大厂租用的服务器, 你的服务器商通常给你的服务器自带了防火墙, 服务器商不同, 开启方法也不相同, 你需要自行去服务商的服务器管理面板放通 9222 端口, 如果你不知道怎么操作, 你可以去谷歌搜索 <你的服务器商的名字> + 放通端口 。

验证 :

手机或者电脑访问 `http://服务器 ip :9222/` , 能打开就算搭建成功。

#### 作为 HoshinoBot 的插件运行

```bash
cd ~/HoshinoBot/hoshino/modules
mkdir yobot
cd yobot

git init
git submodule add https://github.com/yuudi/yobot.git

vim ~/HoshinoBot/hoshino/config/__bot__.py
#MODULE_ON 添加 'yobot'
#HOST修改为 0.0.0.0 ( 默认值为 127.0.0.1 )

#↓↓↓ Ubuntu 和 Debian 用户请无视 ↓↓↓
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --reload
#↑↑↑ Ubuntu 和 Debian 用户请无视 ↑↑↑

#如果你的 HoshinoBot 已经在运行了, 你应该先暂时关闭它
screen -ls
#查看挂起的窗口, 查看 hoshino 这个进程的 pid
kill -9 <pid>
#关闭进程
```

如果你是在大厂租用的服务器, 你的服务器商通常给你的服务器自带了防火墙, 服务器商不同, 开启方法也不相同, 你需要自行去服务商的服务器管理面板放通 8080 端口, 如果你不知道怎么操作, 你可以去谷歌搜索 <你的服务器商的名字> + 放通端口 。

手机或者电脑访问 `http://服务器 ip :8080/` , 能打开就算搭建成功。

使用 `python3 ~/HoshinoBot/run.py` 运行一次 HoshinoBot , 此时启动日志应有

```yaml
[2020-09-22 18:36:31,967 nonebot] INFO: Succeeded to import "hoshino.modules.yobot.yobot"
```

接下来使用组合键 Ctrl + C 关闭 Hoshino ,

```bash
vim ~/HoshinoBot/hoshino/modules/yobot/yobot/src/client/yobot_data/yobot_config.json
#修改 public_address 中的字段, 9222 修改为 8080
```

接下来, 就可以启动

```bash
screen -S bot
#创建新的用于运行 hoshino + yobot 的窗口

python3 ~/HoshinoBot/run.py
#启动命令

#然后, 使用组合键 Ctrl + a ,d 挂起这个窗口
```

### Hoshino 的 config

v2 版本的 Hoshino 需将 API key 填入 hoshino/config 里, 每次修改需要重启。

### 反向代理

设置反向代理的一个好处是可以为你的 yobot 的 web 引入 https 支持, https 协议可以通过 ssl 提供加密处理数据、验证对方身份以及数据完整性保护, 是一种安全的传输协议。

使用反向代理需确定自己有一个域名, 并将域名解析到服务器。

由中国大陆有特殊的法律, 您的服务器在中国大陆境内且使用的域名未通过备案的话, 您是无法使用 80 或者 443 端口的, 这将造成您的 web 无法被访问。唯二解决方法, 一是先去 [备案](https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&rsv_idx=1&tn=baidu&wd=%E5%9F%9F%E5%90%8D%E5%A4%87%E6%A1%88&fenlei=256&rsv_pq=af08bb33000fc69a&rsv_t=a9b5UmKd%2Fb7J%2FVFVWR7aK3g9nN6PanIhYZ%2Bf5%2FQxGgXMYmVKdxM%2FOyYfSiE&rqlang=cn&rsv_enter=1&rsv_dl=tb&rsv_sug3=14&rsv_sug1=11&rsv_sug7=101&rsv_sug2=0&rsv_btype=i&prefixsug=%25E5%259F%259F%25E5%2590%258D%25E5%25A4%2587%25E6%25A1%2588&rsp=4&inputT=3390&rsv_sug4=3972) , 二是使用境外服务器部署。

**注意 : 有些域名无法备案, 购买域名之前应该先去了解。**

下面开始介绍使用 [nginx](https://nginx.org/) 将在 `9222` 或 `8080` 端口运行的 yobot 的 web 转发到 80 端口, 默认您已经为您的服务器设置好了解析。

#### 安装 nginx

目前的 stable 版是 1.18.0, 本教程就以 1.18.0 为例。  
无论何时我都建议你下载 stable 版, 你可以在 [这里](https://nginx.org/en/download.html) 查看当前的 stable 版。

```bash
cd&&wget http://nginx.org/download/nginx-1.18.0.tar.gz
tar -zxvf nginx-1.18.0.tar.gz
cd nginx-1.18.0

./configure --with-http_ssl_module --with-http_v2_module
#如果没有加上这两个参数, 就无法引入 https 支持。

make&&make install
```

#### 验证 Nginx

```bash
cd /usr/local/nginx/sbin
./nginx

curl 127.0.0.1
```

此处应输出 nginx 的欢迎页面

#### 环境变量

将 nginx 加入环境变量以简化后续操作

```bash
vim /etc/profile
```

在这个文件的结尾填入

```nginx
export NGINX_HOME=/usr/local/nginx
export PATH=$PATH:$NGINX_HOME/sbin
```

使用 `source /etc/profile` 使其生效。

验证 :

```bash
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --reload
#放通 80 端口
```

然后, 使用你的手机或者电脑的浏览器, 输入你的域名并回车, 能访问到 nginx 的欢迎页面。

#### 申请 Let’s Encrypt 的 ssl 证书

```bash
cd&&git clone https://github.com/certbot/certbot
cd certbot

#↓↓↓ 以下命令是一个整体, 请整段复制到任意编辑器然后将本条命令第二行和第三行中 example.com 替换成你自己的域名后粘贴到终端
./certbot-auto certonly --manual \
-d *.example.com \
-d example.com --agree-tos \
--manual-public-ip-logging-ok --preferred-challenges \
dns-01 --server https://acme-v02.api.letsencrypt.org/directory
```

然后, 它会自动安装一些依赖, 安装完毕后它会要求你输入你的邮箱。  
~~( **输你自己的 ! wdnmd** )~~

输入完邮箱, 它会询问你是否同意它往您的邮箱里发小广告, 建议回答 no 。

随后, 它会要求你给你的域名添加一条 txt 记录 (这一步是为了验证域名的所有权) , 你需要在域名控制台添加, 您会在你的服务器的终端看到以下内容 :

```yaml
Please deploy a DNS TXT record under the name
_acme-challenge.example.com with the following value:

xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

Before continuing, verify the record is deployed.
```

注意此处的 example.com 应为您自己的域名

此处应该这样添加:
类型: txt , 主机记录: \_acme-challenge , 记录值: `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

回终端后按下回车, 大概率会要求进行第二次验证, 按照这个方法再次验证一次就行。

成功之后, 您服务器目录下的 `/etc/letsencrypt/live/你的域名` 下会生成一些文件, 其中 `fullchain.pem` 是你的证书, `privkey.pem` 是你的私钥。

#### 配置 nginx

源码运行的 yobot 的配置示例

```nginx
server {
  listen 80;
  listen [::]:80;

   listen 443 ssl http2;
   listen [::]:443 ssl http2;
   ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
   ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

  server_name example.com;

  location /
  {
    proxy_pass http://localhost:9222;
    proxy_set_header X-Real-IP $remote_addr;
  }

  location /yobot/assets/ {
    alias /root/yobot/src/client/public/static/;
    expires 30d;
  }

  location /yobot/output/ {
    alias /root/yobot/src/client/yobot_data/output/;
    charset utf-8;
    expires 30d;
  }
}
```

作为 HoshinoBot 插件运行的 yobot 的配置文件示例 :

```nginx
server {
  listen 80;
  listen [::]:80;

   listen 443 ssl http2;
   listen [::]:443 ssl http2;
   ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
   ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

  server_name example.com;

  location /
  {
    proxy_pass http://localhost:8080;
    proxy_set_header X-Real-IP $remote_addr;
  }

  location /yobot/assets/ {
    alias /root/HoshinoBot/hoshino/modules/yobot/yobot/src/client/public/static/;
    expires 30d;
  }

  location /yobot/output/ {
    alias /root/HoshinoBot/hoshino/modules/yobot/yobot/src/client/yobot_data/output/;
    charset utf-8;
    expires 30d;
  }
}
```

修改 nginx 的配置文件

```bash
vim /usr/local/nginx/conf/nginx.conf
```

需将这个配置文件的内容复制粘贴到 `nginx.conf` 的第八十一行, 其中的 `example.com`修改为你自己的域名。

注意 `nginx.conf` 是 nginx 的主配置文件, 如果你不知道其各项参数的含义, 不要做多余的修改。建议在修改前对这个文件进行备份。

2020 / 10 / 21 更新 : `nginx.conf` 不是 json , 无需加逗号。

重启 nginx 使其生效

```bash
nginx -s reload
```

修改 yobot 的配置文件

```bash
#源码版
vim ~/yobot/src/client/yobot_data/yobot_config.json
# public_adress 字段的值修改为 https://example.com/
#↑↑↑ example.com 替换成你自己的域名 ↑↑↑

#插件版
vim ~/HoshinoBot/hoshino/modules/yobot/yobot/src/client/yobot_data/yobot_config.json
# public_adress 字段的值修改为 https://example.com/
#↑↑↑ example.com 替换成你自己的域名 ↑↑↑
```

#### 验证反向代理

浏览器访问 `https://example.com/` 应访问到 yobot 的 homepage

#### 再次提高安全性

修改 yobot 的配置文件, 仅允许本机监听

```bash
#源码版
vim ~/yobot/src/client/yobot_data/yobot_config.json
# HOST 字段的值修改为 127.0.0.1

#插件版
vim ~/HoshinoBot/hoshino/config/__bot__.py
# HOST 字段的值修改为 127.0.0.1
```

### 获取 cq-picsearcher-bot

这个项目与公主连结并无关系, 但是功能实在优秀, 用户可以根据自己的需要选择安装。

#### 安装 nodejs

```bash
#CentOS用户请执行:
curl -sL https://rpm.nodesource.com/setup_10.x | bash -
yum -y install nodejs

#Ubuntu , Debian 用户请执行
curl -sL https://deb.nodesource.com/setup_10.x | bash -
apt -y install nodejs

#不建议安装更高版本的 nodejs
```

#### 安装 cq-picsearcher-bot

```bash
cd
git clone https://github.com/Tsuk1ko/cq-picsearcher-bot.git
cd cq-picsearcher-bot
npm install -g yarn

#↓↓↓ 境内服务器请执行 ↓↓↓
yarn config set registry https://registry.npm.taobao.org --global
yarn config set disturl https://npm.taobao.org/dist --global
#↑↑↑ 境外服务器请忽略 ↑↑↑

yarn install
#使用 yarn 安装依赖

cp config.default.json config.json

vim config.json
#修改配置文件
```

#### 修改 cqps 的配置文件

必须设置的项 : 第十五行的 `admin` 字段修改为你自己的 QQ 号 ( 做主人 ) 。

其余设置项 : 见 [cqps 作者 神代綺凜 对配置文件的详细介绍](https://github.com/Tsuk1ko/cq-picsearcher-bot/wiki/%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E) 。

#### 启动 cqps

```bash
cd ~/cq-picsearcher-bot

yarn start
```

注 : cqps 使用正向 ws 的通信方式, 默认端口和 go-cqhttp 的正向 ws 默认端口一致, 如果没更改过 cqps 的配置文件中的 `port` 字段, 则这样就算完成 cqps 的部署。

#### 验证 cqps

bot 会主动联系你 ( 笑 ) 。

### 安装更多功能

访问 <https://github.com/pcrbot/> 寻找你想要的插件, 并按照说明添加即可。

## 后续维护

通常只需要会英语就能看懂控制台的报错, 通常将报错信息复制到谷歌就可以解决你的问题。

更新卡池, rank 表可以在 pcrbot 的 github 的组织的仓库中找到自动更新的插件。

## 编写功能

如果你没有学过 Python , 请先阅读 [廖雪峰的 Python3 教程](https://www.liaoxuefeng.com/wiki/1016959663602400) 至 异步 IO 中的 `协程` 一节。

如果你学过 Python , 请阅读 [HoshinoBot 开发文档 ( 社区版 )](https://github.com/pcrbot/hoshinobot-development-documentation) 。

## 加入我们

如果你希望为社区贡献代码, 你可以熟悉 git 的使用后, 加入 [pcrbot 的 github 组织](https://github.com/pcrbot) 。

如果你想加入我们, 你可以与 <michikawachin@outlook.com> 联系。

## 致谢

本项目需致谢 [Adpex](https://github.com/Adpex), [Aspirin](https://github.com/AkiraXie) [Lancercmd](https://github.com/Lancercmd), [Ice-Cirno](https://github.com/Ice-Cirno), [ishkong](https://github.com/ishkong), [thgsohack](https://github.com/kkbllt), [yoshino](https://github.com/111234567890), [yuudi](https://github.com/yuudi) 等人。
