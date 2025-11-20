---
layout: single        # 使用单篇文章布局
title: Claude Code的安装使用
date: 2025-11-15 14:27:00 +0800 # 确保日期格式正确
categories: [daily]
excerpt: Claude
---

## Claude Code的安装使用

```shell
C:\Users\Less> irm https://claude.ai/install.ps1 | iex
Setting up Claude Code...
✔ Claude Code successfully installed!
Version: 2.0.46
Location: C:\Users\Less\.local\bin\claude.exe
Next: Run claude --help to get started

⚠ Setup notes:
  • Native installation exists but C:\Users\Less\.local\bin is not in your PATH. Add it by opening: System Properties →
  Environment Variables → Edit User PATH → New → Add the path above. Then restart your terminal.

✅ Installation complete!

C:\Users\Less> claude --help
claude : 无法将“claude”项识别为 cmdlet、函数、脚本文件或可运行程序的名称。请检查名称的拼写，如果包括路径，请确保路径
正确，然后再试一次。
所在位置 行:1 字符: 1
+ claude --help
+ ~~~~~~
    + CategoryInfo          : ObjectNotFound: (claude:String) [], ParentContainsErrorRecordException
    + FullyQualifiedErrorId : CommandNotFoundException
```

**将C:\Users\Less\.local\bin\claude.exe添加到环境变量PATH中，重启终端后就可以了。**
