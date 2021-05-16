---
title: Linux 下部署一个公主连结 qq 群聊机器人
date: 2020-12-02T12:00:00.000Z
cover: /images/banner-little-lyrical.jpg
author: 地河君_official
---

## 开篇

目前, 为公主连结玩家定制的 qq 群聊机器人 ( 以 [yobot](https://github.com/pcrbot/yobot) 和 [HoshinoBot](https://github.com/Ice-Cirno/HoshinoBot) 最具代表性 ) 已有非常多用户。它们可以提供一些诸如 公会战管理, 竞技场查询 等功能的应答, 为公主连结玩家提供了非常有用的帮助。

事实上, 这两个 bot 的代码均已开源, 任何人都可以自行搭建并根据自己的实际需要进行定制。

当然, 这需要你有一点基础, 你需要有

> - 初中以上英语水平
> - 熟悉搜索引擎 ( 单指谷歌 ) 的使用
> - 基础的 shell 交互能力
> - git 基础知识
> - 知道如何使用 vim 编辑器

( 注 : 以上能力按照重要性依次递减 )

若你具备以上能力 ( 至少掌握前两条 ) , 你就可以尝试部署一个机器人, 以便在你的群组中提供有趣或者实用的应答。

### 为什么建议自己部署 bot

**[yobot](https://github.com/pcrbot/yobot) 和 [HoshinoBot](https://github.com/Ice-Cirno/HoshinoBot) 均以 [GPL 3.0](http://www.gnu.org/licenses/gpl-3.0.html) 协议开源。**

开源即是可以免费使用, 但开源不等同于免费, 开源让原本封闭的独裁的技术公开, 让更多的人有机会阅读源代码并从中学习, 包括源代码里面的思想、思路、亦或是实现方式。可以更容易发现 bug, 以及对开源项目提出改进意见, 进而使得一个开源项目更加完善。

---

此处并非要求你一定要为社区贡献代码成为 Contributor, 你使用这些项目, 你和你公会的朋友就可以使用实用或者有趣的功能, bot 的作者 [yuudi](https://github.com/yuudi) 和 [Ice-Cirno](https://github.com/Ice-Cirno) 也会感到开心。

> Ice-Cirno : 我只是不想让我自己的代码帮别人数钱罢了。
>
> 我 : 可以建一个组织, 仓库仅组织可见。
>
> Ice-Cirno : 挺麻烦的, 仓库早被 fork 无数次了。
>
> 我 : 或者在 README 里加上一句...
>
> Ice-Cirno : 没意思, 都是防君子不防小人的。<br>嘛...只要想保持开放, 是阻止不了这种恰饭人的。<br>况且现在也无法转为封闭。<br>
>
> Ice-Cirno : 害, pcr 号一卖, repo 直接 archive<br>不盯着钱, 每天都能开开心心, 用 bot 转发画师、字幕组, 不香吗。<br>多投几篇论文，奖金不比这多 ?

### 预备

本教程将会帮助你部署一个 bot , 如果你确定使用这个教程来部署 bot , 意味着你将使用以下项目的 源代码 / 二进制文件 :

- [yobot](https://github.com/pcrbot/yobot/) 
- [HoshinoBot](https://github.com/Ice-Cirno/HoshinoBot/) 
- [go-cqhttp](https://github.com/Mrs4s/go-cqhttp/) 
- [cq-picsearcher-bot](https://github.com/Tsuk1ko/cq-picsearcher-bot/) ( 可能 )

请切勿忘记这一点。

## 准备工作

你需一台服务器, 服务器价格昂贵, 但你只是想用来部署机器人的话,你可以租用服务器厂商的 虚拟服务器 ( vps ) 。当前, 境内的服务器大厂多数有学生优惠, 你可以在完成学生验证之后以超实惠的价格买到一台虚拟服务器。如果你不是学生, ~~你可尝试去某宝当假学生。~~

目前境内大厂推出的活动 ( 例如学生优惠 / 开发者优惠 ) 都比较实惠, 尤其是某良心云 ( 自行在搜索 " 良心云 " ) 时不时出点香爆的活动, 可自行关注, 不要原价购买, ~~**除非你真的很有钱**~~。

~~境外服务器推荐:~~

我也不知道, 我也想知道, 觉得好的请务必推荐给我。。

如果你实在不知道如何选择, 你可以尝试使用 [ipip.net 提供的 ping 功能](https://tools.ipip.net/ping.php) , ping 一下 mswifi.3g.qq.com , 然后按响应时间排序, 在 ping 的地点那一列能看到服务器的提供商, 优先选择 ping 值低的提供商。

### 选择服务器的系统镜像

本教程测试环境是 CentOS 7.6 和 Ubuntu 18.04 , 未在 Debian 上进行测试, 理论上和 Ubuntu 应该一样。<br>因此我建议你使用 CentOS 7.* 或者 Ubuntu 18.04 ( 20.04 也可行) 。

## 开始部署

第一步, 你需要连接上你的服务器, 本教程指令均不出现 `sudo` , 为了不出现  `Permission denied` 的错误, 请直接在 root 账户执行指令。

> root 账户是 Linux 系统 ( 或者说是 Unix 亦或是类 Unix 系统 ) 超级管理员账户, 权限大于 Windows 系统中的 `Administrator` 账户, 如果操作不当, 对服务器来说是非常危险的, 有经验的 Linux 用户会建议你新建一个低权限账户减少发生意外时的损失。

相信我, 只要不用 root 账户进行不当操作, 那应该是没问题的... ( 大概 )

本教程默认你使用的是 root 账户, 你也可以创建一个低权限账户并在每个指令前加上 `sudo` 完成部署。

### 安装运行所需环境

下列指令请一条一条复制, 一段一段复制可能会出现问题。

注 : 此处是为了防止编译时发生错误。

```shell
# CentOS 用户请执行
yum -y update
yum -y groupinstall "Development tools"
yum -y install wget zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc* libffi-devel make git vim screen

# Debian 、Ubuntu 用户请执行
apt -y update
apt -y install build-essential
apt -y install -y make libssl-dev zlib1g-dev libbz2-dev libpcre3 libpcre3-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libperl-dev libncursesw5-dev xz-utils tk-dev zlib1g libffi-dev liblzma-dev screen git vim openssl gcc
```

### 安装 Python3.9

Python 3.9 通过测试。

```shell
wget https://www.python.org/ftp/python/3.9.5/Python-3.9.5.tgz
# 下载 Python 源代码
# 国内源 : https://mirrors.huaweicloud.com/python/3.9.5/Python-3.9.5.tgz

tar xf Python-3.9.5.tgz
# 解压

cd Python-3.9.5
# 进入这个文件夹

# ↓↓↓ CentOS 用户请执行 ↓↓↓
./configure
# ↑↑↑ Ubuntu 和 Debian 用户请无视 ↑↑↑ 
# 编译检查, 生成 Makefile

# ↓↓↓ Ubuntu 和 Debian 用户请执行 ↓↓↓
./configure  --enable-optimizations
# ↑↑↑ CentOS 用户请无视 ↑↑↑ 
# 编译检查, 生成 Makefile

make&&make install
# 编译安装

pip3 install --upgrade pip
# 境内机器可以加上 -i https://pypi.tuna.tsinghua.edu.cn/simple/ 这个参数加快下载速度
```

`--enable-optimizations` 是 Python 编译优化的参数, 编译检查时加上这个参数大约能给 Python 带来 10% 的性能优化。<br>如果 `gcc编译器` 版本低于 `5.4.0` , 那么加上这个参数会使 Python 编译失败, CentOS 自带的包管理工具 `yum` 不能帮你安装 `5.4.0` 以上的版本, ~~Ubuntu 或者 Debian 的 `apt` 却可以。~~

你也可以自行为你的 CentOS 服务器升级 `gcc编译器` , 在编译 gcc编译器 的时候需要大量时间, 你可自行借助搜索引擎尝试。

### 获取 go-cqhttp

go-cqhttp 是使用 go 语言对 cqhttp 协议重新实现, 并实现了很多原版 cq-http 没有实现的 api 。<br>四酱老婆好棒！

```shell
cd
# 进入到用户文件夹

mkdir go-cqhttp&&cd go-cqhttp
# 创建 go-cqhttp 文件夹并将工作路径切换到这个文件夹

wget https://github.com/Mrs4s/go-cqhttp/releases/download/v0.9.39/go-cqhttp-v0.9.39-linux-amd64.tar.gz
# 可使用 github 镜像站提高境内服务器下载 github 文件的速度
# 例如 hub.fastgit.com, gh.xcw.best, github.dihe.moe
# 下载 go-cqhttp

tar xf go-cqhttp-v0.9.39-linux-amd64.tar.gz
# 解压

chmod +x go-cqhttp
# 给 go-cqhttp 执行权限

./go-cqhttp
# 首次运行 go-cqhttp , 在当前目录下生成配置文件 config.hjson

# ./go-cqhttp update
# 将 go-cqhttp 更新到最新版本
# 不推荐
```

### go-cqhttp 的配置文件

config.hjson :

hjson 支持注释, 生成的 config.hjson 有非常详细的注释, 此处不再赘述。注意账号密码需填写准确。

go-cqhttp 使用明文储存密码, 如果你遇到了困难需要向他人, 请注意截图不要截这一部分。

另外, 还推荐修改一处配置项 :

```json
{
    force_fragmented: true
}
```

此处修改是打开强制分片发送消息, 分片发送将会带来更快的速度以及减少一部分腾讯对机器人消息的风控。

稍后, 你会成功登陆, 当前目录还会生成一个名为 device.json 的文件, 它保存的是登陆设备的信息 :

```json
{
	"protocol": 0, // 设备种类, 值为 0 代表 iPad, 值为 1 代表 Android 手机, 值为 2 代表 Android 手表
	"display": "xxx", // 无需考虑的配置项
	"finger_print": "xxx", // 无需考虑的配置项
	"boot_id": "xxx", // 无需考虑的配置项
	"proc_version": "xxx", // 无需考虑的配置项
	"imei": "xxx" // 无需考虑的配置项
}
```

当设备种类设定为 iPad 时, 功能无限制。此时, 用于部署 bot 的账号不能在别的设备上用 QQ HD 登录。<br>~~呜呜呜, 年轻人的第一台 iPad。~~

当设备种类设定为 Android 手机 时, 功能无限制。此时, 用于部署 bot 的账号不能在别的设备上用手机 QQ 登录。

当设备种类设定为 Android 手表 时, bot 无法接收 `group_notify` 事件、无法接收口令红包信息, 也无法收取消息撤回时间。~~此时, 用于部署 bot 的账号不能在 Android 手表上登录。~~ ( 所以真的有人用手表吗, 别吧 )

### 配置 go-cqhttp 反向 ws 配置

由于 Hoshino 使用反向 websocket 与 go-cqhttp 通信, 所以我们应该对配置文件中的反向 websocket 部分进行配置。

两种示例, 两种都是正确的, 随便选一种就可以。

以 HoshinoBot 为例, HoshinoBot 的默认端口是 `8080` , 因此 go-cqhttp 的反向 ws 上报地址应为 `ws://127.0.0.1:8080/ws/` 。

你可以使用 vim 编辑器编辑配置文件 :

```shell
vim config.hjson
# 不了解 vim 编辑器的速速去了解先
```

找到 `ws_reverse_servers` 部分, 将其修改为 :

```json
{
    ws_reverse_servers: [
        {
            enabled: true
            reverse_url: ws://127.0.0.1:8080/ws/
            reverse_api_url: ""
            reverse_event_url: ""
            reverse_reconnect_interval: 3000
        }
    ]
}
```

或者 :

```json
    ws_reverse_servers: [
        {
            enabled: true
            reverse_url: ""
            reverse_api_url: ws://127.0.0.1:8080/ws/api/
            reverse_event_url: ws://127.0.0.1:8080/ws/event/
            reverse_reconnect_interval: 3000
        }
    ]
```

两种都是正确的, 任意一种即可。

然后退出 vim 编辑器。

### 获取 HoshinoBot

重头戏来了。

```shell
cd
# 进入到用户文件夹

mkdir pcrbot
# 创建一个文件夹

cd pcrbot
# 将工作路径切换到这个文件夹

git clone https://github.com/Ice-Cirno/HoshinoBot.git
# clone 仓库


```

关于安装依赖 :

```bash
# 方法一: 使用 poetry ( 推荐 )
pip3 install poetry
# 安装 poetry

wget https://pan.dihe.moe/pyproject.toml
# 这是一份写好了的 pyproject.toml

poetry install
# 等待, 并心存希望吧

# 方法二: 直接使用 pip
pip3 install -r https://pan.dihe.moe/requirements.txt
# 可在这个指令后加上 -i http://mirrors.aliyun.com/pypi/simple/ 切换到国内源
```

```bash
cd HoshinoBot
# 切换到 HoshinoBot 目录

cp -r HoshinoBot/hoshino/config_example HoshinoBot/hoshino/config
# 复制配置文件示例

vim hoshino/config/__bot__.py
# 修改配置项以自定义你的机器人, 按照中文注释填好。
```

若你的服务器有中文乱码的现象, 你可以对照 [这里](https://github.com/Ice-Cirno/HoshinoBot/blob/master/hoshino/config_example/__bot__.py) 的中文注释修改你的配置。

### 获取静态资源

获取合成图片所需要的图包  :

```shell
cd ~/pcrbot/HoshinoBot
# 切换工作路径

wget https://pan.dihe.moe/res.tar.gz
# 下载静态资源

tar xf res.tar.gz
# 解压静态资源
```

安装新版 jjc 查询合成图片所需要的字体 :

```shell
# ↓↓↓ CentOS 执行 ↓↓↓
yum -y install fontconfig

#↓↓↓ Ubuntu 和 Debian 执行 ↓↓↓
apt -y install fontconfig

mkdir -p /usr/share/fonts/chinese
# 创建字体文件夹

wget https://pan.dihe.moe/msyh.tar.gz
# 下载字体

tar zxvf msyh.tar.gz -C /usr/share/fonts/chinese
# 解压到字体目录

mkfontscale
mkfontdir
# 创建字体缓存
```

### 测试 bot 是否搭建成功

尝试运行 bot , 看看其是否能工作。

1, 启动 `go-cqhttp` :

```shell
screen -S go-cqhttp
# 创建一个新的窗口用于运行 go-cqhttp 

cd ~/go-cqhttp

./go-cqhttp
# 运行 go-cqhttp 

# 可能需要异地登录验证, 可能需要滑块验证
# 滑块验证请看 : https://docs.go-cqhttp.org/faq/slider.html

# 验证可能有点阴间, 请不要放弃

# 验证成功之后, 使用 ./go-cqhttp 这个命令来启动 go-cqhttp, 账号会开始登陆

# 有极低概率获取到无效的登陆地址而导致登陆失败, 重试即可解决

# 成功登陆后, go-cqhttp 会尝试连接到 Websocket Universal 服务器 ws://127.0.0.1:8080/ws/ 
# 你现在的 8080 端口上还没有运行 HoshinoBot , 所以会连接不上, 看到连接不上的提示不要慌, 使用组合键 Ctrl + a ,d 挂起这个窗口
```

2, 启动 `HoshinoBot` :

```shell
screen -S hoshino
#创建一个新的窗口用于运行 HoshinoBot

cd ~/pcrbot/HoshinoBot
# 切换工作路径

# 运行 Hoshino:
# ↓↓↓ 使用了 poetry 的场合 ↓↓↓
poetry run python run.py

# ↓↓↓ 直接使用 pip 的场合 ↓↓↓
python3 run.py
```

如果到这里为止一切正常, HoshinoBot 的控制台应该会输出类似于这样的一条日志 :

```yaml
[2020-09-21 22:30:45,435] 127.0.0.1:56363 GET /ws/ 1.1 101 - 114514
```

然后, 使用组合键 Ctrl + a ,d 挂起这个窗口。

恭喜 ! 你成功搭建了 HoshinoBot , 现在立刻在群里 艾特 你的 bot 并发送一句 `kkp` 吧 !

### Hoshino 的 api key

Hoshino 部分功能需要填写 apikey 后才可使用。

#### Mikan RSS token

使用 HoshinoBot 的番剧推送功能需要 Mikan 的 rss token,

你可以在 mikan 注册账号之后, 在 [这个页面](https://mikanani.me/) 鼠标右键点击 RSS 订阅 的图标, 点击 复制链接地址, 然后得到这样一条地址

`https://mikanani.me/RSS/MyBangumi?token=abcdfegABCFEFG%2b123%3d%3d`

将其 token 部分的 `%2b`替换为`+` , 将 `%2f` 替换为 `/` , 将 `%3d `替换为 `=` 后, 得到这样的一个字符串 : `abcdfegABCFEFG%2b123==` , 这就是你的 rss token。

```shell
vim ~/pcrbot/HoshinoBot/hoshino/config/mikan.py
```

将它填写到 `mikan.py` 吧。

#### 报时文本

编写报时文本是个体力活, 你可以使用别人编写好了的, 

```shell
cd ~/pcrbot/HoshinoBot/hoshino/config
# 切换工作路径

rm -f hourcall.py
# 删除空配置文件

wget https://pan.dihe.moe/hourcall.py
# 下载报时文本
```

#### 推特转发

需推特开发者账号, 可在 [这里](https://developer.twitter.com/en) 申请, 一旦失败, 此账号将永久失去再次申请的资格。来自中国大陆的申请大概率不通过, 请使用海外手机号提高通过概率。

**注意 : 使用 Google Voice 等虚拟手机号极高概率会使推特账号永久封禁。**

如果你已经具有推特开发者账号, 你可以在 [这里](https://developer.twitter.com/en/portal/apps/new) 创建一个应用, 取得应用的 `consumer_key` , `consumer_secret` , `access_token_key` , `access_token_secret` 四个参数, 并将其填入配置文件的 `twitter.py` 。

```shell
vim ~/pcrbot/HoshinoBot/hoshino/config/twitter.py
```

#### pcrdfans 的授权 key

使用 pcrdfans 提供的 jjc 查询功能需要 pcrdfans 的授权 key 。

你可以在 <https://pcrdfans.com/bot> 申请。现在申请渠道已关闭。

如果你已经有了授权 key , 你可以把它填到配置文件的 priconne.py 去。

```shell
vim ~/pcrbot/HoshinoBot/hoshino/config/priconne.py
```

#### 设置入群欢迎

```shell
vim ~/pcrbot/HoshinoBot/hoshino/config/groupmaster.py
```

在 `increase_welcome` 中按照这样的格式填写 :

```json
increase_welcome = {
    "default": "欢迎入群 ! 你已经是群大佬了, 快来跟群萌新打个招呼吧 ~",
    367501912: "欢迎来到 Hoshino 群 !",
    770947581: "欢迎来到 yobot 1 群 !",
    1044314369: "欢迎来到 yobot 2 群 !",
    774394459: "欢迎来到 yobot 5 群 !",
}
```

### 获取 yobot

两种安装 yobot 的方法。这里只写一种。<br>第二种安装方法在未来将不被支持。~~懂的都懂。~~

#### 源码运行

首先修改 go-cqhttp 的示例并重启, 使其同时向 `ws://127.0.0.1:8080/ws/` 和 `ws://127.0.0.1:9222/ws/` 上报事件, 下面的配置文件可供参考 :


```json
{
	ws_reverse_servers: [
        {
            enabled: true
            reverse_url: ws://127.0.0.1:8080/ws/
            reverse_api_url: ""
            reverse_event_url: ""
            reverse_reconnect_interval: 3000
        }

        {
            enabled: true
            reverse_url: ws://127.0.0.1:9222/ws/
            reverse_api_url: ""
            reverse_event_url: ""
            reverse_reconnect_interval: 3000
        }
    ]
}
```

获取 yobot :

```shell
cd ~/pcrbot
# 切换工作路径到 pcrbot 文件夹

git clone https://github.com/yuudi/yobot.git
# clone 仓库

cd yobot/src/client
# 切换工作路径

screen -S yobot
# 创建一个新窗口用于运行 yobot 
```

```bash
# 使用了 poetry 的场合:
poetry run python main.py
# 生成 yobotg.sh

vim yobotg.sh
# 修改 yobotg.sh 的第七行的 python3 main.py -g 为 poetry run python main.py -g

sh yobotg.sh
# 启动 yobot

# 直接使用 pip 的场合:
python3 main.py

sh yobotg.sh
```

然后, 使用组合键 Ctrl + a , d 挂起这个窗口

私聊或群聊发送 `V` 或 `ver` 或 `version` , bot 会回复 yobot 版本。

放通端口, 使 yobot 的 web 能在 9222 端口被外部访问 :

```shell
firewall-cmd --zone=public --add-port=9222/tcp --permanent
firewall-cmd --reload
# 针对 CentOS 用户, Ubuntu 和 Debian 无需执行。
```

如果你是在大厂租用的服务器, 你的服务器商通常给你的服务器自带了防火墙, 服务器商不同, 开启方法也不相同, 你需要自行去服务商的服务器管理面板放通 9222 端口, 如果你不知道怎么操作, 你可以去谷歌搜索 <你的服务器商的名字> + 放通端口 。

验证 : 

手机或者电脑访问 `http://服务器 ip :9222/` , 能打开就算搭建成功。

###  反向代理

设置反向代理的一个好处是可以为你的 yobot 的 web 引入 https 支持, https 协议可以通过 ssl 提供加密处理数据、验证对方身份以及数据完整性保护, 是一种安全的传输协议。

使用反向代理需确定自己有一个域名, 并将域名解析到服务器。

由中国大陆有特殊的法律, 你的服务器在中国大陆境内且使用的域名未通过备案的话, 你是无法使用 80 或者 443 端口的, 这将造成你的 web 无法被访问。唯二解决方法, 一是先去 [备案](https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&rsv_idx=1&tn=baidu&wd=%E5%9F%9F%E5%90%8D%E5%A4%87%E6%A1%88&fenlei=256&rsv_pq=af08bb33000fc69a&rsv_t=a9b5UmKd%2Fb7J%2FVFVWR7aK3g9nN6PanIhYZ%2Bf5%2FQxGgXMYmVKdxM%2FOyYfSiE&rqlang=cn&rsv_enter=1&rsv_dl=tb&rsv_sug3=14&rsv_sug1=11&rsv_sug7=101&rsv_sug2=0&rsv_btype=i&prefixsug=%25E5%259F%259F%25E5%2590%258D%25E5%25A4%2587%25E6%25A1%2588&rsp=4&inputT=3390&rsv_sug4=3972) , 二是使用境外服务器部署。

**注意 : 有些域名无法备案, 购买域名之前应该先去了解。**

如果你想要保护隐私, ~~那么你应该拒绝国内云~~。

下面开始介绍使用 [nginx](https://nginx.org/) 将在 `9222` 或 `8080` 端口运行的 yobot 的 web 转发到 80 端口。

假设你购买了域名, 你可以添加一条 A 记录到你的服务器, 如果你不知道怎么加, [Google](https://www.google.com.hk/webhp?hl=zh-CN&sourceid=cnhp&gws_rd=ssl) will help you.

当你在命令行输入 `nslookup example.com` ( 其中 example.com 为你自己解析到服务器的域名 ) 并在 `address` 行看到了你服务器的公网 ip 时, 你就成功的设置好了解析。

#### 安装 nginx 

目前的 stable 版是 1.18.0, 本教程就以 1.18.0 为例。<br>无论何时我都建议你下载 stable 版, 你可以在 [这里](https://nginx.org/en/download.html) 查看当前的 stable 版。


```shell
cd
# 切换工作路径到用户文件夹

wget http://nginx.org/download/nginx-1.18.0.tar.gz
# 下载 nginx
# 国内源 : https://cn-pan.di.he.cn/Linux/nginx-1.18.0.tar.gz

tar xf nginx-1.18.0.tar.gz
# 解压

cd nginx-1.18.0
# 切换工作路径

./configure --with-http_ssl_module --with-http_v2_module
# 编译检查, 生成 MakeFile , 启用 ssl 模块和 http2 模块
# 如果没有加上这两个参数, 就无法引入 https 支持。

make&&make install
# 编译安装
```

#### 验证安装

```shell
cd /usr/local/nginx/sbin
# 切换工作路径

./nginx
# 启动 nginx

curl 127.0.0.1
# 向 127.0.0.1 发出 GET 请求, 返回结果会输出在命令行
```

此处应输出 nginx 的欢迎页面。

#### 环境变量

将 nginx 加入环境变量以简化后续操作

```shell
vim /etc/profile
```

在这个文件的结尾填入

```nginx
export NGINX_HOME=/usr/local/nginx
export PATH=$PATH:$NGINX_HOME/sbin
```

退出 vim 编辑器, 使用 `source /etc/profile` 使其生效。

验证 :

```shell
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --reload
# 放通 80 端口
```

然后, 使用你的手机或者电脑的浏览器, 输入你的域名并回车, 能访问到 nginx 的欢迎页面。

#### 申请 Let’s Encrypt 的 ssl 证书

```shell
cd
# 切换工作路径到用户文件夹

# CentOS 选手:
yum -y install certbot

# Ubuntu, Debian 选手:
apt -y install certbot

# 请复制下面一整段命令将其粘贴到一个你喜欢的文本编辑器上, 不要用 Windows 记事本
certbot certonly --manual \
-d *.example.com \
-d example.com --agree-tos \
--manual-public-ip-logging-ok --preferred-challenges \
dns-01 --server https://acme-v02.api.letsencrypt.org/directory
# 将文本编辑器上的这段命令里的第二行和第三行的 example.com 修改成你自己的域名
# 然后, 将编辑器上的这段命令复制下来, 粘贴到服务器的终端执行
```

然后, 它会自动安装一些依赖, 安装完毕后它会要求你输入你的邮箱。<br>~~( **输你自己的 ! wdnmd** )~~

输入完邮箱, 它会询问你是否同意它往你的邮箱里发小广告, 建议回答 no 。

随后, 它会要求你给你的域名添加一条 txt 记录 ( 这一步是为了验证域名的所有权 ) , 你需要在域名控制台添加, 你会在你的服务器的终端看到以下内容 :

```yaml
Please deploy a DNS TXT record under the name 
_acme-challenge.example.com with the following value: 

xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx 

Before continuing, verify the record is deployed.
```

注意此处的 example.com 应为你自己的域名

此处应该这样添加:
类型: txt , 主机记录: _acme-challenge , 记录值: `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` 

回终端后按下回车, 会要求进行第二次验证, 按照这个方法再次验证一次就行。

成功之后, 你服务器目录下的 `/etc/letsencrypt/live/你的域名` 下会生成一些文件, 其中 `fullchain.pem` 是你的证书, `privkey.pem` 是你的私钥。

#### 配置 nginx 

源码运行的 yobot 的配置示例

```
server {
  listen 80;
  listen [::]:80;
  server_name example.com;
  rewrite ^(.*)$ https://$server_name$request_uri;
}

server {
  listen 443 ssl http2;
  listen [::]:443 ssl http2;

  ssl_stapling on;
  ssl_stapling_verify on;
  
  ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
  ssl_ciphers 'AES128+EECDH:AES128+EDH:!aNULL';

  ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
  ssl_session_cache shared:SSL:10m;

  resolver 8.8.4.4 8.8.8.8 valid=300s;
  resolver_timeout 10s;

  ssl_prefer_server_ciphers on;
  # ssl_dhparam /etc/letsencrypt/live/dhparam.pem;

  add_header Strict-Transport-Security max-age=63072000;
  add_header X-Frame-Options DENY;
  add_header X-Content-Type-Options nosniff;

  server_name example.com;

  if ($http_user_agent ~* "qihoobot|Baiduspider|Googlebot|Googlebot-Mobile|Googlebot-Image|Mediapartners-Google|Adsbot-Google|Feedfetcher-Google|Yahoo! Slurp|Yahoo! Slurp China|YoudaoBot|Sosospider|Sogou spider|Sogou web spider|MSNBot|ia_archiver|Tomato Bot")
    {
      return 403;
    }
  
  location /
  {
    proxy_pass http://localhost:9222;
    proxy_set_header X-Real-IP $remote_addr;
  }

  location /yobot/assets/ {
    alias ~/pcrbot/yobot/src/client/public/static/; 
    expires 30d;
  }
  
  location /yobot/output/ {
    alias ~/pcrbot/yobot/src/client/yobot_data/output/;
    charset utf-8;
    expires 30d;
  }

  error_page   500 502 503 504  /50x.html;
  location = /50x.html {
      root   html;
  }
}
```

修改 nginx 的配置文件

```
vim /usr/local/nginx/conf/nginx.conf
```

需将这个配置文件的内容复制粘贴到 `nginx.conf` 的第八十一行, 其中的 `example.com`修改为你自己的域名。

注意 `nginx.conf` 是 nginx 的主配置文件, 如果你不知道其各项参数的含义, 不要做多余的修改。建议在修改前对这个文件进行备份。

重启 nginx 使其生效

```shell
nginx -s reload
```

修改 yobot 的配置文件

```shell
vim ~/pcrbot/yobot/src/client/yobot_data/yobot_config.json

# public_adress 字段的值修改为 https://example.com/

# 此处 example.com 替换成你自己的域名
```

#### 验证反向代理

浏览器访问 `https://example.com/` 应访问到 yobot 的 homepage

#### 再次提高安全性

修改 yobot 的配置文件, 仅允许本机监听

```shell
vim ~/pcrbot/yobot/src/client/yobot_data/yobot_config.json
# HOST 字段的值修改为 127.0.0.1
# 如果你没有做好反向代理的工作, 请不要修改 !
```

### 获取 cq-picsearcher-bot

这个项目与公主连结并无关系, 但是功能实在优秀, 用户可以根据自己的需要选择安装。

#### 安装 nodejs

```shell
# CentOS用户请执行 :
curl -sL https://rpm.nodesource.com/setup_12.x | bash - 
yum -y install nodejs

# Ubuntu , Debian 用户请执行 :
curl -sL https://deb.nodesource.com/setup_12.x | bash - 
apt -y install nodejs

# 不建议安装更高版本的 nodejs 
```

#### 安装  cq-picsearcher-bot

```shell
cd
# 切换工作路径到用户文件夹

git clone https://github.com/Tsuk1ko/cq-picsearcher-bot.git
# clone 仓库

cd cq-picsearcher-bot
# 切换工作路径

npm install -g yarn
# 使用 npm 全局安装 yarn
# 然后 npm 就可以爪巴了

# yarn 切换到国内源
yarn config set registry https://registry.npm.taobao.org --global
yarn config set disturl https://npm.taobao.org/dist --global
# ↑↑↑ 境外服务器请忽略 ↑↑↑

yarn install
# 使用 yarn 安装依赖, 相比于使用 npm 节约了亿点点时间

cp config.default.json config.json
# 复制 config.default.json 并将其命名为 config.json

vim config.json
# 修改配置文件
```

#### 修改 cqps 的配置文件

必须设置的项 : 

- 第十五行的 `admin` 字段修改为你自己的 QQ 号 ( 做主人 ) 。
- 在 <https://saucenao.com> 创建账号, 并在 [这里](https://saucenao.com/user.php?page=search-api) 找到你的 api key , 将其填入到配置文件的第一百二十一行的 `saucenaoApiKey` 里。

其余设置项 : 见 [cqps 作者 神代綺凜 对配置文件的详细介绍](https://github.com/Tsuk1ko/cq-picsearcher-bot/wiki/%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E) 。

#### 启动 cqps

```shell
cd ~/cq-picsearcher-bot
# 切换工作路径

yarn start
# 启动 cqps
```

注 : cqps 使用正向 ws 的通信方式, 默认端口和 go-cqhttp 的正向 ws 默认端口一致, 如果没更改过 cqps 的配置文件中的 `port` 字段, 则这样就算完成 cqps 的部署。

#### 验证安装

bot 会主动联系你 ( 笑 ) 。

### 安装更多功能

访问 <https://github.com/pcrbot/HoshinoBot-plugins-index> 寻找你想要的插件, 并按照说明添加即可。

## 优化

如果不愿意折腾的话可以忽略这一部分。。

### 使用 http 协议发图

默认你已经装好了 nginx , 且未改变过默认配置 ( 如果你在浏览器中访问 <http://你服务器的 ip 地址> 是 nginx 的欢迎页面, 说明你的设置是没问题的 )

```shell
wget https://pan.dihe.moe/res.tar.gz
# 下载

tar xf res.tar.gz
# 解压

mv res /usr/local/nginx/html
# 移动到网站目录

chmod 777 /usr/local/nginx/html/res -R
# 给可读可写权限
```

然后修改 hoshino 的 \__bot__.py :

```python
RES_DIR = r'/usr/local/nginx/html/res/'

RES_PROTOCOL = 'http'

RES_URL = 'http://<你服务器的 ip 地址>/res/'
```

重启 Hoshino , 完事。

### 使用 pm2 运行 go-cqhttp

先关闭你启动的 go-cqhttp 进程 :

```shell
screen -r go-cqhttp
# 进入 go-cqhttp 这个 screen

# Ctrl + C 组合键, 终止 go-cqhttp 进程

exit
# 关闭 screen
```

然后 :

```shell
yarn global add pm2
# yarn 全局安装 pm2

cd ~/go-cqhttp
# 切换工作路径

pm2 start ./go-cqhttp
# 使用 pm2 守护运行 go-cqhttp
```

常用命令 : 

```shell
pm2 stop go-cqhttp
# 关闭 go-cqhttp

pm2 restart go-cqhttp
# 重启 go-cqhttp

pm2 log go-cqhttp
# 查看日志
```

## 后续维护

此静态资源包可能 rank 表已经过时, 你可以向其他 bot 索要 rank 表, 并用 xftp 之类的软件将你的图片上传到你的服务器上。

或者使用自动更新卡池, rank 表的插件, 你可以在 pcrbot 的 github 组织中寻找。

如果你需要帮助, 你可以加入 Hoshino 群 , yobot 1 群 , yobot 2 群 , 或者 yobot 5 群, 一般情况下有礼貌的提问都会有人帮助你解决。

## 编写功能

如果你没有学过 Python , 请先阅读 [廖雪峰的 Python3 教程](https://www.liaoxuefeng.com/wiki/1016959663602400) 至 异步 IO 中的 `协程` 一节。

如果你学过 Python , 请阅读 [ HoshinoBot 开发文档 ( 社区版 ) ](https://github.com/pcrbot/hoshinobot-development-documentation) 。

## 加入我们

如果你希望为社区贡献代码, 你可以熟悉 git 的使用后, 加入 [pcrbot 的 github 组织](https://github.com/pcrbot) 。

如果你想加入我们, 你可以在 [这个仓库](https://github.com/pcrbot/join-us) 里提交一个 issue, 随后在与 GitHub 关联的邮箱里查看邀请。

## 致谢

本项目需致谢 [Adpex](https://github.com/Adpex), [Aspirin](https://github.com/AkiraXie) [Lancercmd](https://github.com/Lancercmd), [Ice-Cirno](https://github.com/Ice-Cirno), [ishkong](https://github.com/ishkong), [thgsohack](https://github.com/kkbllt), [WenyiDong](https://github.com/WenyiDong), [yoshino](https://github.com/111234567890), [yuudi](https://github.com/yuudi) 等人。