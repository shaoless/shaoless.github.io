---
layout: single
title: "项目结构设计"
date: 2025-11-24 20:00:00 +0800 # 确保日期格式正确
categories: [dev]
tags: [project]
excerpt: "项目结构设计"
---
```shell
MotorTestEngine/
│
├── MotorTestEngine.Core/             ← 核心模型与接口
│   ├── Models/                       ← 电机、试验、结果等实体
│   ├── Calculators/                  ← 所有性能计算器
│   └── Services/                     ← 计算服务、数据服务
│
├── MotorTestEngine.Data/             ← EF Core 数据访问层
│   ├── AppDbContext.cs
│   └── Migrations/
│
├── MotorTestEngine.UI/               ← WPF 主界面
│   ├── Views/
│   │   ├── MainWindow.xaml
│   │   ├── TestView.xaml
│   │   └── ReportView.xaml
│   │
│   ├── ViewModels/
│   │   ├── MainViewModel.cs
│   │   ├── TestViewModel.cs
│   │   └── ReportViewModel.cs
│   │
│   └── App.xaml.cs                   ← Prism 启动入口
│
├── MotorTestEngine.Reports/          ← 报告生成模块
│   ├── PdfReportGenerator.cs         ← iTextSharp 生成 PDF
│   └── ExcelReportGenerator.cs       ← NPOI 或 ClosedXML
│
└── MotorTestEngine.Shared/           ← 共享资源
    ├── Converters/                   ← WPF 转换器
    ├── Helpers/                      ← 工具类
    └── Properties/                   ← 枚举、常量
```

这是一个C#项目可以参考的目录设计，其中MotorTestEngine是项目名称，MotorTestEngine.Core、MotorTestEngine.Data、MotorTestEngine.UI、MotorTestEngine.Reports、MotorTestEngine.Shared是项目的主要模块。

这种设计可以让项目更加清晰，易于维护，并且可以很好的分离各个模块。