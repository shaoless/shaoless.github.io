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

## 四大核心层次

### 1. 领域层 (Domain Layer) - 业务的心脏

这是系统的核心，纯粹地表达业务逻辑和规则，不应包含任何技术实现。

* **职责**:
  * 封装核心业务逻辑和规则。
  * 定义业务对象（实体、值对象）。
  * 定义业务流程中所需的接口（如 `IOrderRepository`）。
* **包含内容**:
  * **实体 (Entities)**: 如 `Order`, `Customer`。
  * **值对象 (Value Objects)**: 如 `Address`。
  * **领域服务 (Domain Services)**。
  * **仓储接口 (Repository Interfaces)**。
  * **领域事件 (Domain Events)**。

### 2. 应用层 (Application Layer) - 业务的协调者

这一层很薄，负责协调领域对象来完成一个完整的业务用例，不包含任何业务规则。

* **职责**:
  * 为表现层提供业务入口。
  * 协调领域对象完成任务。
  * 处理事务、权限等。
* **包含内容**:
  * **应用服务 (Application Services)**: 如 `OrderService`。
  * **数据传输对象 (DTOs)**: 用于与表现层交互。
  * **命令/查询 (Commands/Queries)**。

### 3. 基础设施层 (Infrastructure Layer) - 技术的实现者

这是所有具体技术细节的所在地，负责实现领域层和应用层定义的接口。

* **职责**:
  * 实现数据持久化（如与数据库交互）。
  * 与其他外部系统通信（调用第三方API、发送邮件等）。
  * 提供缓存、日志等具体技术方案。
* **包含内容**:
  * **仓储实现**: `OrderRepository_EFCore`。
  * **数据库上下文**: `DbContext`。
  * **Web API客户端**: `HttpClient` 封装。
  * **消息队列实现**: RabbitMQ, Kafka 等。

### 4. 表现层 (User Interface / Presentation Layer) - 用户的入口

用户与系统交互的界面，负责展示信息和接收用户指令。

* **职责**:
  * 向用户显示数据。
  * 将用户操作转换为对应用层服务的调用。
* **包含内容**:
  * **Web**: API控制器、MVC视图。
  * **桌面应用**: 视图、视图模型 (ViewModels)。

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

## 主要区别

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