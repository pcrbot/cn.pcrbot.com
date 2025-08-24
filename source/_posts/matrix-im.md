---
title: Matrix 聊天服务介绍
date: 2023-06-028T00:00:00.101Z
cover: /images/banner-ayumi.jpg
---

## 介绍

作为即时通讯软件，Matrix 几乎是理想中的完美方案，这篇文章介绍一下 Matrix 的特点和用法。

官方的介绍：<https://matrix.org>

### 完全的端到端加密

只有发送者和接收者可以解密会话内容，任何第三方，包括服务器，都无法查看私有的聊天内容。

### 功能齐全

私聊、群聊、空间（群中群）、消息列（Thread）、表情回应、消息编辑、可选的已读回执、语音视频、机器人、应用集成、时间线、等等……

### 联邦制

Matrix 是一个标准。任何符合标准的软件都可能加入 Matrix 网络中，任何两个使用 Matrix 协议的用户都可以随意跨网络会话。

## 使用

### 选择一个客户端

在 Matrix 官网上列举了[一些推荐的客户端](https://matrix.org/ecosystem/clients/)

这里推荐一些：

#### 网页端

- [Element](http://app.element.io/) （半官方的客户端，大而全）
- [cinny](https://app.cinny.in/) （简洁美观，但某些功能缺失，比如投票）

#### 桌面客户端

一般来说，桌面端都是网页端的封装。如非必要，使用网页端即可。

如确实需要桌面端，可以选择这些：

- [Element](https://github.com/element-hq/element-desktop/releases/latest) （基于electron的封装）
- [Cinny](https://github.com/cinnyapp/cinny-desktop/releases/latest) （基于tauri的封装）

#### 手机端

- Element（半官方的客户端，大而全）
    - [Google Play 商店](https://play.google.com/store/apps/details?id=im.vector.app)
    - [苹果 App Store](https://apps.apple.com/app/id1631335820)
    - [APK 下载](https://github.com/element-hq/element-android/releases/latest)
- FluffyChat（简介精美，支持多账号）
    - [Google Play 商店](https://play.google.com/store/apps/details?id=chat.fluffy.fluffychat)
    - [苹果 App Store](https://apps.apple.com/app/fluffychat/id1551469600)
    - [APK 下载](https://github.com/krille-chan/fluffychat/releases/latest)

### 选择一个服务商

进入客户端后，你需要选择一个服务商注册登录，根据喜好和自己网络情况选一个开放的服务商即可。

| 服务器                                   | 介绍                                       | 网络测试                                                                                   |
| ---------------------------------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------ |
| [Element](https://app.element.io/)       | Matrix / Element 官方服务器，讨论任何主题  | <span class="ping" ping="https://matrix.org/_matrix/federation/v1/version"></span>         |
| [Mozilla](https://chat.mozilla.org/)     | Mozilla / Firefox 官方服务器，讨论任何主题 | <span class="ping" ping="https://mozilla.modular.im/_matrix/federation/v1/version"></span> |
| [Gitter](https://https://app.gitter.im/) | gitter 服务器，主要讨论软件开发            | <span class="ping" ping="https://gitter.ems.host/_matrix/federation/v1/version"></span>    |
| [pcrbot.com](https://matrix.pcrbot.com/) | 本站提供的主服务器                         | <span class="ping" ping="https://matrix.pcrbot.com/_matrix/federation/v1/version"></span>  |
| [yobot.homes](https://chat.yobot.homes/) | 本站提供的备用服务器（某些省份更好直连）   | <span class="ping" ping="https://matrix.yobot.homes/_matrix/federation/v1/version"></span> |

<script>
document.addEventListener("DOMContentLoaded", function () {
    Array.from(document.getElementsByClassName('ping')).forEach(async function (element) {
        const observer = new IntersectionObserver(entries => {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    observer.unobserve(element);
                    (async function() {
                        element.textContent = '延迟:';
                        const url = element.getAttribute('ping');
                        for (let i = 0; i < 4; i++) {
                            const now = new Date().getTime();
                            try {
                                await fetch(url, { method: 'GET', mode: 'no-cors' });
                                const latency = new Date().getTime() - now;
                                const indicator = latency < 200 ? '🟢' : '🟡';
                                element.textContent += ` ${indicator} ${latency}ms`;
                            } catch {
                                element.textContent += ' 🔴 不可用';
                            }
                            await new Promise(resolve => setTimeout(resolve, 1000));
                        }
                    })();
                }
            });
        })
        observer.observe(element);
    });
});
</script>

### 找到感兴趣的群聊

在客户端的“探索”功能里，你可以看到开放的群聊并选择加入，也欢迎加入本站的交流群 [#zgq:pcrbot.com](https://matrix.to/#/#zgq:pcrbot.com)

## 搭建

如果你想拥有自己的 Matrix 服务器，可以选择一个[完全托管服务器](https://matrix.org/ecosystem/hosting/)，也可以用开放的[服务器程序](https://matrix.org/ecosystem/servers/)部署在自己的服务器上。

- [Synapse](https://element-hq.github.io/synapse/latest/setup/installation.html)（半官方的服务端，大而全）
- [Tuwunel](https://matrix-construct.github.io/tuwunel/introduction.html)（rust实现的服务端，占用极低）

## 常见问题（真的很常见）

### 多端登录后，一直显示正在等待解密

这是因为 Matrix 的端到端加密机制。两个设备登录后，密钥默认没有同步（有时候翻译为认证）。

解决方法：首次登录时，会通知“有未认证的设备”，按照提示认证即可。如果错过了，可以前往设置->设备，进行认证。
