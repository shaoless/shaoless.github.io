---
layout: single
title: DDD分层架构核心概念 (DDD Layered Architecture Core Concepts)
categories: dev
tags: ddd
excerpt: 领域驱动设计（DDD）中的经典分层架构，理解如何组织一个健壮、可维护的复杂系统。
---

## DDD分层架构核心概念 (DDD Layered Architecture Core Concepts)

本文档旨在介绍领域驱动设计（DDD）中经典的分层架构，帮助理解如何组织一个健壮、可维护的复杂系统。

## 核心思想：依赖倒置

DDD分层架构形如洋葱，核心是业务，外层是技术。其最重要的原则是**依赖倒置**：所有依赖关系都必须指向更核心的层次。

```shell
+------------------------------------------------------+
|                                                      |
|          表现层 (User Interface / Presentation)        |
|                  (依赖 -> 应用层)                      |
|                                                      |
|  +--------------------------------------------------+  |
|  |                                                  |  |
|  |               应用层 (Application)                 |
|  |                  (依赖 -> 领域层)                  |
|  |                                                  |  |
|  |  +----------------------------------------------+  |  |
|  |  |                                              |  |  |
|  |  |               领域层 (Domain)                  |  |  |  <-- 业务核心
|  |  |              (不依赖任何其他层)                |  |  |
|  |  |                                              |  |  |
|  |  +----------------------------------------------+  |  |
|  |                                                  |  |
|  +--------------------------------------------------+  |
|                                                      |
|  +--------------------------------------------------+  |
|  |                                                  |  |
|  |             基础设施层 (Infrastructure)              |
|  |      (实现领域层/应用层接口, 依赖 -> 领域层)       |  |
|  |                                                  |  |
|  +--------------------------------------------------+  |
|                                                      |
+------------------------------------------------------+
```

---

## 架构变体和选择

DDD的经典架构是四层，但在实际项目中需要根据系统复杂度选择合适的变体。

>四层 vs 三层架构的取舍

### 三层架构（表现层 + 领域层 + 基础设施层）

- 适用场景：业务逻辑相对简单，CRUD主导的应用，控制器可以直接调用领域对象
- 优点：减少样板代码，设计更轻量，开发速度快
- 缺点：业务增长时控制器容易臃肿，横切关注点（如事务、日志）难以集中处理
- 合理性：对于小型应用或原型开发是合理的，但不适用于复杂的电机测试系统

#### 四层架构的优势

- 职责分离：明确区分"做什么（Domain）"和"如何做（Application）"
- 横切关注点处理：事务、权限、日志、缓存等统一在应用层管理
- 可测试性：各层可以独立测试，便于单元测试和集成测试

#### 选择建议

简单CRUD应用：三层架构就好，Application层可有可无
中等复杂度：引入薄应用层，处理事务和基础验证
高复杂度企业应用：坚持四层架构，领域层专注于业务规则

---

## 四大核心层次

### 1. 领域层 (Domain Layer) - 业务的心脏

这是系统的核心，纯粹地表达业务逻辑和规则，不应包含任何技术实现。

- **职责**:
  - 封装核心业务逻辑和规则。
  - 定义业务对象（实体、值对象）。
  - 定义业务流程中所需的接口（如 `IOrderRepository`）。

- **包含内容**:
  - **实体 (Entities)**: 如 `Order`, `Customer`。
  - **值对象 (Value Objects)**: 如 `Address`。
  - **领域服务 (Domain Services)**。
  - **仓储接口 (Repository Interfaces)**。
  - **领域事件 (Domain Events)**。

### 2. 应用层 (Application Layer) - 业务的协调者

这一层很薄，负责协调领域对象来完成一个完整的业务用例，不包含任何业务规则。

- **职责**:
  - 为表现层提供业务入口。
  - 协调领域对象完成任务。
  - 处理事务、权限等。

- **包含内容**:
  - **应用服务 (Application Services)**: 如 `OrderService`。
  - **数据传输对象 (DTOs)**: 用于与表现层交互。
  - **命令/查询 (Commands/Queries)**。

### 3. 基础设施层 (Infrastructure Layer) - 技术的实现者

这是所有具体技术细节的所在地，负责实现领域层和应用层定义的接口。

- **职责**:
  - 实现数据持久化（如与数据库交互）。
  - 与其他外部系统通信（调用第三方API、发送邮件等）。
  - 提供缓存、日志等具体技术方案。

- **包含内容**:
  - **仓储实现**: `OrderRepository_EFCore`。
  - **数据库上下文**: `DbContext`。
  - **Web API客户端**: `HttpClient` 封装。
  - **消息队列实现**: RabbitMQ, Kafka 等。

### 4. 表现层 (User Interface / Presentation Layer) - 用户的入口

用户与系统交互的界面，负责展示信息和接收用户指令。

- **职责**:
  - 向用户显示数据。
  - 将用户操作转换为对应用层服务的调用。
- **包含内容**:
  - **Web**: API控制器、MVC视图。
  - **桌面应用**: 视图、视图模型 (ViewModels)。

---

## 代码示例：充血领域模型

DDD的核心在于拥有一个“充血”的领域模型，即模型本身包含业务行为，能维护自身状态的一致性。

```csharp
// Order.cs - 一个充血的领域模型示例
public class Order
{
    public int Id { get; private set; } // 状态由内部维护，外部无法随意修改
    public string Status { get; private set; }
    private readonly List<OrderItem> _items = new List<OrderItem>();
    public IReadOnlyList<OrderItem> Items => _items;

    // 构造函数用于创建有效对象
    public Order(...) { /* ... */ }

    // 核心业务行为被封装在方法中
    public void Cancel(string reason)
    {
        // 业务规则校验
        if (Status == "Shipped")
        {
            throw new InvalidOperationException("Cannot cancel a shipped order.");
        }
        
        // 改变自身状态
        this.Status = "Cancelled";
        
        // 可以发布领域事件，通知其他模块
        // AddDomainEvent(new OrderCancelledEvent(this.Id, reason));
    }

    public void AddItem(Product product, int quantity)
    {
        // 业务规则校验
        if (Status != "Pending")
        {
            throw new InvalidOperationException("Can only add items to a pending order.");
        }
        _items.Add(new OrderItem(product, quantity));
        this.RecalculateAmount(); // 内部状态变更
    }

    private void RecalculateAmount() { /* ... */ }
}
```

### **完整的应用层服务示例**

应用层负责协调领域对象并处理横切关注点：

```csharp
public class MotorTestApplicationService
{
    private readonly IMotorRepository _motorRepository;
    private readonly IUnitOfWork _unitOfWork;
    private readonly ITestValidationService _validationService; // 应用层特有服务

    [Transactional] // 应用层处理事务
    public async Task<TestResultDto> ExecuteMotorTestAsync(ExecuteMotorTestCommand command)
    {
        // 1. 验证权限和输入（应用层职责）
        await _validationService.ValidateTestPermissionsAsync(command.UserId, command.MotorId);
        
        // 2. 获取领域对象
        var motor = await _motorRepository.GetByIdAsync(command.MotorId);
        
        // 3. 执行领域逻辑（领域层职责）
        var result = motor.ExecuteTest(command.TestType, command.Parameters);
        
        // 4. 持久化并发布事件
        await _unitOfWork.SaveChangesAsync();
        
        // 5. 返回DTO（应用层职责）
        return new TestResultDto(result);
    }
}

```

---

## 示例流程：用户下单

一个请求如何在各层之间流动：

1. **[表现层]**: `OrdersController` 接收到一个包含 `CreateOrderDto` 的HTTP请求。
2. **[应用层]**: `Controller` 调用 `OrderApplicationService.PlaceOrder(dto)`。
3. **[领域层 & 基础设施层]**:
    * `ApplicationService` 使用仓储接口 (`IOrderRepository`) 从数据库（**基础设施层**的实现）获取`Customer`和`Product`领域对象。
    * `ApplicationService` 调用领域对象的方法 `customer.PlaceOrder(...)`。所有业务规则在**领域层**内部执行。
    * `ApplicationService` 使用仓储接口 (`IOrderRepository`) 将新创建的`Order`对象保存到数据库（**基础设施层**的实现）。
4. **[返回]**: 执行结果逐层返回，最终由**表现层**以HTTP响应的形式呈现给用户。

**简化流程图:**
`User -> [Controller] -> [ApplicationService] -> [Domain Models] <-> [Repositories (Infrastructure)] -> DB`

## 实体、值对象和DTO的区别

### 实体 (Entity)

实体是具有唯一标识和生命周期的对象，其身份是通过ID来识别的，而不是通过属性值。

```csharp
public class Motor // 电机实体
{
    public int Id { get; set; }  // 标识符
    public string MotorModel { get; set; }
    public string MotorNo { get; set; }
    
    // 实体有业务行为
    public void UpdateModel(string newModel)
    {
        if (string.IsNullOrEmpty(newModel))
            throw new ArgumentException("模型不能为空");
        MotorModel = newModel;
    }
}
```

### 值对象 (Value Object)

值对象没有唯一标识符，通过其属性值来定义相等性的对象。

```csharp
public class Money // 金额值对象
{
    public decimal Amount { get; }
    public string Currency { get; }
    
    public Money(decimal amount, string currency)
    {
        Amount = amount;
        Currency = currency;
    }
    
    public Money Add(Money other)
    {
        if (Currency != other.Currency)
            throw new InvalidOperationException("不能相加不同货币");
        return new Money(Amount + other.Amount, Currency);
    }
}
```

### DTO (Data Transfer Object)

DTO主要用于在不同层或系统间传输数据的简单对象。

```csharp
public class MotorDto // 用于传输电机数据
{
    public int Id { get; set; }
    public string MotorModel { get; set; }
    public string MotorNo { get; set; }
    public string DisplayName { get; set; } // 格式化后的显示名称
    public string Status { get; set; } // 翻译后的状态显示
}
```

### 主要区别

| 特性 | 实体 | 值对象 | DTO |
|------|------|--------|-----|
| **标识** | 有唯一ID | 无ID，基于值相等 | 通常有ID |
| **可变性** | 可变 | 不可变 | 可变 |
| **行为** | 有业务行为 | 可能有相关行为 | 基本无业务行为 |
| **生命周期** | 有 | 无 | 无 |
| **用途** | 核心业务对象 | 描述特征 | 数据传输 |

### 总结

**实体**关注"身份"，有唯一标识符

**值对象**关注"值"，通过属性值判断相等性

**DTO**关注"传输"，用于层间数据传递


## DDD目录结构示例 - 电机试验系统

## 完整DDD目录结构示例

```shell
DDD/
├── K.App.Domain/                    # 领域层
│   ├── Entities/                    # 实体
│   │   ├── Motor.cs                 # 电机实体
│   │   ├── TestExecution.cs         # 测试执行实体
│   │   └── TestType.cs              # 测试类型实体
│   ├── ValueObjects/                # 值对象
│   │   ├── PowerRating.cs           # 功率等级值对象
│   │   ├── Voltage.cs               # 电压值对象
│   │   ├── Current.cs               # 电流值对象
│   │   ├── Temperature.cs           # 温度值对象
│   │   └── TestResult.cs            # 测试结果值对象
│   ├── Services/                    # 领域服务
│   │   ├── IMotorDomainService.cs   # 领域服务接口
│   │   ├── MotorDomainService.cs    # 领域服务实现
│   │   └── ITestDomainService.cs    # 测试领域服务
│   ├── Aggregates/                  # 聚合根
│   │   └── MotorAggregate.cs        # 电机聚合根
│   ├── Events/                      # 领域事件
│   │   ├── MotorTestStartedEvent.cs # 电机测试开始事件
│   │   ├── MotorTestCompletedEvent.cs # 电机测试完成事件
│   │   └── MotorCreatedEvent.cs     # 电机创建事件
│   ├── Exceptions/                  # 领域异常
│   │   ├── MotorAlreadyExistsException.cs
│   │   ├── MotorTestNotSupportedException.cs
│   │   └── InvalidMotorConfigurationException.cs
│   ├── Enums/                       # 领域相关枚举
│   │   ├── MotorTestType.cs         # 电机测试类型
│   │   ├── MotorStatus.cs           # 电机状态
│   │   └── ConnectionType.cs        # 连接类型
│   └── Repositories/                # 仓储接口（由领域层定义）
│       ├── IMotorRepository.cs      # 电机仓储接口
│       ├── ITestExecutionRepository.cs # 测试执行仓储接口
│       └── ITestTypeRepository.cs   # 测试类型仓储接口
│
├── K.App.Application/               # 应用层
│   ├── Services/                    # 应用服务
│   │   ├── MotorApplicationService.cs # 电机应用服务
│   │   ├── TestApplicationService.cs  # 测试应用服务
│   │   └── DeviceApplicationService.cs # 设备应用服务
│   ├── Commands/                    # 命令
│   │   ├── CreateMotorCommand.cs    # 创建电机命令
│   │   ├── ExecuteTestCommand.cs    # 执行测试命令
│   │   └── UpdateMotorCommand.cs    # 更新电机命令
│   ├── Queries/                     # 查询
│   │   ├── GetMotorQuery.cs         # 获取电机查询
│   │   ├── GetTestExecutionQuery.cs # 获取测试执行查询
│   │   └── GetTestResultsQuery.cs   # 获取测试结果查询
│   ├── DTOs/                        # 数据传输对象
│   │   ├── Requests/                # 请求DTO
│   │   │   ├── CreateMotorRequest.cs
│   │   │   └── ExecuteTestRequest.cs
│   │   └── Responses/               # 响应DTO
│   │       ├── MotorDto.cs
│   │       ├── TestResultDto.cs
│   │       └── DeviceStatusDto.cs
│   ├── Exceptions/                  # 应用层异常
│   │   └── ApplicationValidationException.cs
│   └── Mappers/                     # DTO映射器
│       └── DomainToDtoMapper.cs
│
├── K.App.Infrastructure/            # 基础设施层
│   ├── Persistence/                 # 持久化
│   │   ├── DBContext/               # 数据库上下文
│   │   │   ├── AppDbContext.cs      # 应用数据库上下文
│   │   │   └── CustomDbContextFactory.cs
│   │   ├── Entities/                # EF实体
│   │   │   ├── MotorEntity.cs       # 电机EF实体
│   │   │   ├── TestExecutionEntity.cs # 测试执行EF实体
│   │   │   └── EntityTypeConfig/    # 实体类型配置
│   │   │       ├── MotorEntityConfig.cs
│   │   │       └── TestExecutionEntityConfig.cs
│   │   └── Migrations/              # 数据库迁移
│   │
│   ├── Repositories/                # 仓储实现
│   │   ├── MotorRepository.cs       # 电机仓储实现
│   │   ├── TestExecutionRepository.cs # 测试执行仓储实现
│   │   └── GenericRepository.cs     # 通用仓储实现
│   │
│   ├── Services/                    # 基础设施服务
│   │   ├── ExcelExportService.cs    # Excel导出服务
│   │   ├── ModbusCommunicationService.cs # Modbus通信服务
│   │   ├── FileStorageService.cs    # 文件存储服务
│   │   └── EmailNotificationService.cs # 邮件通知服务
│   │
│   ├── External/                    # 外部服务集成
│   │   ├── DeviceIntegration/       # 设备集成
│   │   │   ├── DeviceConnectionFactory.cs
│   │   │   ├── TcpDeviceConnector.cs
│   │   │   └── Rs485DeviceConnector.cs
│   │   └── ThirdPartyApis/          # 第三方API
│   │       └── ExternalDataApiService.cs
│   │
│   ├── Common/                      # 基础设施公共组件
│   │   ├── Aspects/                 # AOP切面
│   │   │   └── TransactionAspect.cs
│   │   ├── Extensions/              # 扩展方法
│   │   │   ├── EntityExtensions.cs
│   │   │   └── QueryableExtensions.cs
│   │   └── Utils/                   # 工具类
│   │       ├── CalculationHelper.cs # 计算助手
│   │       └── ConnectionHelper.cs  # 连接助手
│   │
│   └── Configuration/               # 配置
│       ├── DependencyInjection.cs   # 依赖注入配置
│       └── DeviceConfigurationService.cs # 设备配置服务
│
├── K.App.Common/                    # 共享层
│   ├── Constants/                   # 常量
│   │   └── RegisterAddresses.cs     # 寄存器地址常量
│   ├── Enums/                       # 共享枚举
│   │   └── PowerUnit.cs             # 功率单位枚举
│   ├── Interfaces/                  # 共享接口
│   │   ├── IDomainEvent.cs          # 领域事件接口
│   │   ├── IEventHandler.cs         # 事件处理器接口
│   │   └── IUnitOfWork.cs           # 工作单元接口
│   ├── Events/                      # 共享事件
│   │   └── IntegrationEvent.cs      # 集成事件基类
│   └── Utils/                       # 共享工具
│       ├── StringExtensions.cs      # 字符串扩展方法
│       └── NumberExtensions.cs      # 数值扩展方法
│
└── K.App/                           # UI层 (WPF)
    ├── Views/                       # 视图
    │   ├── MainWindow.xaml          # 主窗口
    │   ├── MotorViews/              # 电机相关视图
    │   │   ├── MotorListView.xaml   # 电机列表视图
    │   │   └── MotorDetailView.xaml # 电机详情视图
    │   └── TestViews/               # 测试相关视图
    │       ├── TestExecutionView.xaml # 测试执行视图
    │       └── TestResultView.xaml    # 测试结果视图
    │
    ├── ViewModels/                  # 视图模型
    │   ├── MainWindowViewModel.cs   # 主窗口视图模型
    │   ├── MotorViewModels/         # 电机相关视图模型
    │   │   ├── MotorListViewModel.cs
    │   │   └── MotorDetailViewModel.cs
    │   └── TestViewModels/          # 测试相关视图模型
    │       ├── TestExecutionViewModel.cs
    │       └── TestResultViewModel.cs
    │
    ├── Services/                    # UI服务
    │   ├── NavigationService.cs     # 导航服务
    │   ├── DialogService.cs         # 对话框服务
    │   └── MessageService.cs        # 消息服务
    │
    ├── Modules/                     # Prism模块
    │   ├── MotorModule.cs           # 电机模块
    │   └── TestModule.cs            # 测试模块
    │
    ├── Common/                      # UI公共组件
    │   ├── Behaviors/               # 行为
    │   │   └── ValidationBehavior.cs
    │   └── Converters/              # 转换器
    │       ├── EnumToDescriptionConverter.cs
    │       └── BooleanToVisibilityConverter.cs
    │
    ├── Styles/                      # 样式资源
    │   ├── Common.xaml              # 公共样式
    │   └── Controls.xaml            # 控件样式
    │
    ├── Images/                      # 图像资源
    └── App.xaml                     # 应用程序入口
    └── App.xaml.cs
```

### 关键目录说明：

#### Domain层 (核心业务逻辑)

**Entities/**: 核心业务实体，有唯一标识

**ValueObjects/**: 描述性对象，无唯一标识，通过值判断相等

**Services/**: 跨多个实体的业务逻辑

**Events/**: 业务领域内的重要事件

**Repositories/**: 由领域层定义的仓储契约

#### Application层 (业务流程编排)

**Services/**: 协调领域对象执行业务用例

**Commands/Queries**: 命令查询模式的请求对象

**DTOs/**: 层间数据传输对象

#### Infrastructure层 (技术实现)

**Persistence/**: 数据库访问实现

**Repositories/**: 仓储接口的具体实现

**Services/**: 具体的技术服务实现

**External/**: 外部系统集成

#### Common层 (共享组件)

**Interfaces/**: 跨层共享的接口

**Constants/**: 系统常量

**Utils/**: 通用工具方法

#### UI层 (用户界面)

**Views/ViewModels**: UI展示逻辑

**Services/**: UI相关的服务

**Modules/**: UI模块化组织

#### 依赖关系

**Domain** ← (独立)

**Application** ← **Domain**, **Common**

**Infrastructure** ← **Application**, **Domain**, **Common**

**UI** ← **Infrastructure**, **Application**, **Domain**, **Common**

这样的目录结构确保了：

1. **关注点分离**: 每层职责明确
2. **单向依赖**: 依赖只能从外层指向内层
3. **可扩展性**: 新功能可以轻松添加
4. **可测试性**: 各层可以独立测试
