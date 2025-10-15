---
layout: single        # 使用单篇文章布局
title: "nvm4w安装后不能使用npm"
date: 2025-10-15 14:27:00 +0800 # 确保日期格式正确
categories: [daily]
tags: [powershell,nvm,npm]
excerpt: "powershell"
---

## PowerShell中使用nvm4w安装Node.js后，无法使用npm命令

nvm4w是Node.js版本管理工具，可以方便地管理多个Node.js版本。

安装nvm4w后，在PowerShell中输入`npm -v`命令，出现如下错误：

```powershell
npm
# npm : 无法加载文件 C:\nvm4w\nodejs\npm.ps1。未对文件 C:\nvm4w\nodejs\npm.ps1 进行数字签名。无法在当前系统上运行该脚本。有关运行脚本和设置执行策略的详细信息，请参阅 https:/go.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。
# 所在位置 行:1 字符: 1
# + npm
# + ~~~
# + CategoryInfo          : SecurityError: (:) []，PSSecurityException
# + FullyQualifiedErrorId : UnauthorizedAccess
node -v
v22.20.0
nvm -v
1.2.2
```

## 解决方式

1. 不使用powershell，使用cmd或者其他终端工具
2. 修改powershell的执行策略
3. 命令如下，完美解决

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```
