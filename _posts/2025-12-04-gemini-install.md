---
layout: single
title: "Installing Gemini on Windows"
date: 2025-12-04 12:00:00 +0800
categories: [Tech]
tags: [Gemini, Windows]
excerpt: Gemini的安装和使用
---

## 下载安装包

前提条件：安装[nodejs](https://nodejs.org/en/download/)

然后根据[Gemini](https://geminicli.com)官网的安命令操作

```shell
# 可以直接运行不用安装进行测试使用
# Using npx (no installation required)
npx https://github.com/google-gemini/gemini-cli

# Mac 还可以使用brew安装

brew install gemini-cli

# Release
npm install -g @google/gemini-cli

# Preview
npm install -g @google/gemini-cli@preview
# Stable
npm install -g @google/gemini-cli@latest

# Nightly
npm install -g @google/gemini-cli@nightly

```

安装完成后，可以使用`gemini`命令进行测试

```shell
gemini 

 ███            █████████  ██████████ ██████   ██████ █████ ██████   █████ █████
░░░███         ███░░░░░███░░███░░░░░█░░██████ ██████ ░░███ ░░██████ ░░███ ░░███
  ░░░███      ███     ░░░  ░███  █ ░  ░███░█████░███  ░███  ░███░███ ░███  ░███
    ░░░███   ░███          ░██████    ░███░░███ ░███  ░███  ░███░░███░███  ░███
     ███░    ░███    █████ ░███░░█    ░███ ░░░  ░███  ░███  ░███ ░░██████  ░███
   ███░      ░░███  ░░███  ░███ ░   █ ░███      ░███  ░███  ░███  ░░█████  ░███
 ███░         ░░█████████  ██████████ █████     █████ █████ █████  ░░█████ █████
░░░            ░░░░░░░░░  ░░░░░░░░░░ ░░░░░     ░░░░░ ░░░░░ ░░░░░    ░░░░░ ░░░░░

Tips for getting started:
1. Ask questions, edit files, or run commands.
2. Be specific for the best results.
3. Create GEMINI.md files to customize your interactions with Gemini.
4. /help for more information.
```

开始会让你进行登录

```shell
> Google Account
> Gemini API Key
> 其他
```

我选择账号登录后，会打开浏览器进行登录，登录完成后就可以在命令行中使用Gemini了。

但是我使用VPN的情况下还是提示失败
![alt text](/assets/img/gemini-error.png)

通过查询得知因为Gemini对IP限制很严格

github有相应的[解决办法](https://github.com/google-gemini/gemini-cli/issues/8170#issuecomment-3489203449)

就是打开代理软件的Tun Mode

我试着打开

![alt text](/assets/img/tunmode.png)

这里要求打开Tun Mode需要先安装Service Mode

![alt text](/assets/img/servicemode.png)

安装后再打开Tun Mode就可以了
![alt text](/assets/img/clashx.png)

![alt text](/assets/img/gemini-success.png)
gemini的运行感觉比较慢，需要等待很久，应该还是网络问题。

最终Gemini的启动界面如下：

![alt text](/assets/img/gemini.png)

使用体验在了解后发。
