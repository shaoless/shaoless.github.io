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

## 更完善的设计

如果希望未来系统具备：

- 可扩展（Extendable）

- 可维护（Maintainable）

- 最低耦合（Low Coupling）

- 可插拔（Modular）

- 可测试（Testable）

- 可部署（可拆 DLL、可做 NuGet、可做 Microservice）

🟢 第一层级：现在的结构（基础结构(上面的)）

---

🟡 第二层级：推荐扩展的结构（专业结构）

---
1️⃣ Application 层（非常推荐）

把业务逻辑都放在 Core 中，但从架构角度 Core 应只包含：

- 领域模型（Motor, TestResult）

- 接口（IReportGenerator, ICalculator）

- 纯业务逻辑无框架依赖

而 “应用流程” 应该放在 Application 层，例如：

- TestManager（调度测试 → 保存数据库 → 生成报告）

- MotorTestOrchestrator（调用 F1MethodService → 整理结果）

- Commands / Queries（如果未来引入 CQRS）

---
2️⃣ Infrastructure 层（可选）

现在的 Data 和 Reports 就属于 Infrastructure，不过可以整合为：

```shell
MotorTestEngine.Infrastructure
    ├── Data/
    ├── Reports/
    ├── ExternalServices/

```

更有利于未来扩展，比如：

- 接入 OPC UA

- Modbus 设备通讯

- 外部 Web API

- 实时曲线记录服务

---
3️⃣ Module 模块化（Prism 强项）

如果未来 UI 部分会变大（比如：电机测试系统通常会有 10+ 界面），应该拆模块：

```shell
MotorTestEngine.Modules.Testing
MotorTestEngine.Modules.Reporting
MotorTestEngine.Modules.Settings
MotorTestEngine.Modules.Dashboard
```

每个模块可以包含：

- Views

- ViewModels

- Services

- 自身注入的 DI 注册

然后由 Main UI 加载：

```csharp
protected override void ConfigureModuleCatalog(IModuleCatalog moduleCatalog)
{
    moduleCatalog.AddModule<TestingModule>();
    moduleCatalog.AddModule<ReportingModule>();
}

```

真正的企业级插件式扩展

---

4️⃣ Domain 层分得更细（可选）

把 Core 拆为：

```shell
MotorTestEngine.Domain      ← 纯业务，不依赖 EF、WPF
MotorTestEngine.Domain.Services
MotorTestEngine.Domain.Models
MotorTestEngine.Domain.Interfaces

```

---

🟠 第三层级：企业级结构（非常大型系统用）

未来如果需要更高规模（比如要变成 Web + WPF 混合平台），可以改成：

```shell
src/
 ├── UI.WPF/              ← Prism
 ├── UI.Web/              ← ASP.NET Core Razor 或 Blazor
 ├── Application/
 ├── Domain/
 ├── Infrastructure/
 ├── Modules/
 ├── Shared/
tests/
 ├── UnitTests/
 ├── IntegrationTests/
 └── UITests/
docs/
 ├── architecture/
 └── usermanual/

```

---
🧩 最终的“专业级”结构如下：

```shell
MotorTestEngine/
│
├── MotorTestEngine.UI/                 ← Prism WPF Shell
│   ├── Modules/
│   └── App.xaml.cs (全局DI)
│
├── MotorTestEngine.Application/        ← 调度、工作流
│   └── Services/
│
├── MotorTestEngine.Domain/             ← 纯业务模型 + 接口
│   ├── Models/
│   ├── Interfaces/
│   └── Services/
│
├── MotorTestEngine.Infrastructure/      ← EF、报告、外部服务
│   ├── Data/
│   ├── Reports/
│   └── External/
│
└── MotorTestEngine.Shared/             ← 通用工具、Converter、校验
```

在新建项目时，除了UI层用Prism等WPF的项目模版、其他层都是使用Class Library模版。

---

🎉 这样的结构设计可以让项目更加专业化，易于维护，并且可以很好的分离各个模块。

如果还要增加单元测试的功能（实际开发中单元测试其实必不可少）

```shell
src/
 ├── MotorTestEngine.UI/              ← WPF + Prism
 ├── MotorTestEngine.Application/     ← 流程、调度、用例
 ├── MotorTestEngine.Domain/          ← 模型、接口、纯逻辑
 ├── MotorTestEngine.Infrastructure/  ← EF、报告、外部服务
 └── MotorTestEngine.Shared/

tests/
 ├── MotorTestEngine.Tests.Unit/          ← 单元测试（Mock）
 └── MotorTestEngine.Tests.Integration/   ← 集成测试（数据库/报告）
````
