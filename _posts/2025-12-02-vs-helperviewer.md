---
layout: single
title: "VS Helper Viewer的安装和使用"
date: 2025-12-02 12:00:00 +0800
categories: tools
tags: tools
excerpt: "Helper Viewer"
---

## Helper Viewer的安装和使用

在使用VS开发过程中，如果想了解每个类，属性、方法的功能和命名空间，可以安装并使用Helper Viewer插件。

如果安装后，就可以鼠标放在相应的内容上按F1就可以调到介绍文档

1. 安装位置：帮助->添加或删除帮助内容，并且调整帮助->Set Helper Perferances中的设置，选择“在帮助查看器中启动”，这样就可以通过帮助->查看帮助来打开Helper Viewer。
2. 在帮助查看其中，选择内容管理，就可以选择需要下载的帮助内容，建议安装源选择“联机”，这样帮助资源比较丰富。可以自定义安装位置。

## 存在的问题

如果安装后，后续将文件安装目录误删，再点击查看帮助会提示“vs helper viewer 提示指定的用于安装帮助内容无效”。这里有两种方式处理

1. 如果记得之前的目录名称可以直接新建一个相同的目录。但是如果只新建目录，会看到错误提示“提示查看的内容文件缺失或者损坏”这样任然打开不了Helper Viewer。需要再新建的文件夹新建一个“**CatalogType.xml**”文件，并且添加以下内容：

   ```xml
    <?xml version="1.0" encoding="utf-8"?><catalogType>UserManaged</catalogType>
   ```

2. 如果已经忘记原目录，可以通过注册表查看“HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Help\v2.3\Catalogs\VisualStudio15”中的LocationPath可以看到相应的存储目录。可以修改也可以保持原样来新建目录。然后重复步骤1的操作即可。
