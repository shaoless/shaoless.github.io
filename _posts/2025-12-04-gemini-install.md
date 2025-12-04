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
> Tun模式：TUN Mode（虚拟网卡模式）下，Clash会创建一个虚拟Tun设备（类似VPN的虚拟网卡），在网络层（Layer3）拦截所有系统流量（TCP、UDP、ICMP），自动路由到代理服务器。无需手动设置系统代理，甚至可以禁用它。

```优点```

- 全局接管：所有应用流量强制通过代理，包括游戏、下载工具、甚至不兼容代理的软件。支持 UDP 和 ICMP，适合玩游戏或全系统代理。
- 透明代理：用户无需配置每个应用，Clash 自动管理路由表、防火墙规则和 DNS 劫持（可配置 tun.auto-route 和 tun.auto-redir）。
- 性能更好：在 Windows 上比旧的 TAP 模式更快、更稳定（Premium 核心支持 gVisor 或 system 栈）。

```缺点```

- 需权限：要求安装 Service Mode（见下文），可能需管理员权限。
- 资源占用稍高：虚拟网卡会增加少量 CPU/内存使用。
- 潜在问题：浏览器“安全 DNS”功能可能干扰（需关闭）；与某些 VPN（如 Tailscale）冲突。

> 非Tun模式：工作原理通过设置 Windows 的系统代理（HTTP/SOCKS），让支持代理的应用程序（如浏览器、部分软件）将流量转发到 Clash 处理。Clash 仅劫持这些应用的 TCP/UDP 流量。

```优点```

- 简单易用，无需额外权限
- 资源占用低，适合日常浏览。

```缺点```

- 不兼容性强：许多应用不遵循系统代理（如某些游戏、命令行工具、UWP 应用如 Microsoft Store 游戏）。这些流量会绕过代理，导致无法“翻墙”或分流。
- 仅应用层：无法处理 ICMP（Ping）等低层协议，UDP 支持有限（游戏常需 UDP）。
- DNS 泄漏风险：浏览器可能绕过代理 DNS 查询。

---

## 结果展示

![alt text](/assets/img/gemini-success.png)
gemini的运行感觉比较慢，需要等待很久，应该还是网络问题。

最终Gemini的启动界面如下：

![alt text](/assets/img/gemini.png)

使用体验在了解后发。
