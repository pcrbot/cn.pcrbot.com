---
title: authMS - HoshinoBot 授权管理
date: 2020-08-15 12:00:00
subtitle:
categories:
tags:
cover: /images/banner-misato.jpg
---

（社区作品）

适用于HoshinoBot v2的授权插件

源码与使用说明：<https://github.com/pcrbot/authMS>

## 特点

* **无GUI** ,不依赖Native环境, 依托于nonebot架构, 对不同的操作系统友好
* 支持新群试用
* 支持自定义到期后是否自动退群
* 支持无卡密操作, 直接对一个群的授权进行修改/清零, 同时执行自动退群(可选)
* 支持自定义入群发言, 退群发言
* 授权检查范围广, 支持以下情形:
  * 被拉近50人以下的群(因为默认不需要被邀请者同意)
  * 超过50人群, 需要机器人同意的群
  * 机器人掉线期间被邀请加入的群
  * 维护者手动登录机器人帐号加入的群
