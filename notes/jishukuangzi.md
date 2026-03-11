---
timezone: UTC+8
---

# jishukuangzi

**GitHub ID:** jishukuangzi

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
### **一、睿应层课程概述**

-   主题：响应式合约（RCs）基础及技术概念
    
-   目标：
    
    1.  理解 RCs 与传统智能合约的区别
        
    2.  掌握控制反转（Inversion of Control）概念
        
    3.  了解 RCs 如何自主监控区块链事件并执行操作
        
    4.  探索 RCs 的实际应用场景
        

* * *

### **二、RCs 与传统智能合约的区别**

| 特性 | 传统智能合约 | 响应式合约 (RCs) |
| --- | --- | --- |
| 执行方式 | 被动，需要外部账户调用 | 主动，持续监控并自主执行 |
| 触发条件 | 外部交易（EOA调用） | 区块链事件或状态变化 |
| 控制权 | 在调用者手中 | 控制权在合约自身（IoC） |
| 应用场景 | 基础逻辑操作（如转账） | 自动化交易、数据收集、套利、再平衡等 |

* * *

### **三、关键概念**

1.  **响应性（Reactivity）**
    
    -   RC 能持续监听区块链事件，并在条件满足时自动执行操作。
        
    -   思路：观察 → 判断 → 执行。
        
2.  **控制反转（Inversion of Control, IoC）**
    
    -   控制权从外部调用者转向合约自身。
        
    -   合约自主决定何时执行逻辑，而不依赖外部交易触发。
        
3.  **事件监控与自动响应**
    
    -   订阅区块链事件或状态变化（如价格更新、交易完成、流动性变化）。
        
    -   根据预设规则自动执行动作（下单、套利、重平衡等）。
        

* * *

### **四、实际应用场景**

1.  **数据收集**：从预言机获取链外数据，更新链上状态
    
2.  **UniSwap 停止订单**：价格达到阈值自动买卖
    
3.  **DEX 套利**：发现价格差异时自动套利
    
4.  **资金池再平衡**：自动调整池中资产比例
    

* * *

### **五、理解小结**

-   RC = 传统合约 + 主动性 + 自动事件响应
    
-   关键点：**主动监控 + 控制反转 + 自动执行规则**
    
-   适合**需要实时决策和自动化操作的场景**  
    

## 一、学习目标（构建一个跨链事件与回调合约）

本次学习主要围绕 **Reactive Network 的跨链事件机制**，目标包括：

1.  学习如何使用 **Reactive Network** 构建跨链事件系统。
    
2.  理解如何在 **一个区块链触发事件**，并通过 **Reactive Smart Contracts (RSCs)** 在 **另一条链上执行回调操作**。
    
3.  学习如何利用 **合约继承、事件机制以及跨链回调** 来实现链上应用之间的交互。
    

* * *

# 二、Reactive Network 基本概念

**Reactive Network** 是一种用于构建 **事件驱动型区块链应用** 的网络。

传统智能合约通常是：

> 用户发送交易 → 合约执行逻辑

而 Reactive Network 的核心思想是：

> **事件触发 → 自动执行响应逻辑**

也就是说：

某个链上的 **事件(Event)** 可以自动触发另一个合约的 **回调函数(callback)**。

这种模式叫：

**Reactive Programming（响应式编程）**

* * *

# 三、跨链事件系统的工作流程

Reactive Network 可以实现 **跨链事件响应机制**，基本流程如下：

### Step 1：源链触发事件

在 **源链（Source Chain）** 上执行某个操作，例如：

-   转账
    
-   NFT Mint
    
-   合约调用
    

合约会 **发出一个事件（Event）**。

例如：

```
emit TransferTriggered(user, amount);
```

* * *

### Step 2：Reactive Network 监听事件

Reactive Network 会：

-   监听链上的事件
    
-   识别符合条件的事件
    
-   将事件信息发送到 Reactive 合约
    

* * *

### Step 3：Reactive Smart Contract 处理事件

Reactive Smart Contract（RSC）会：

-   接收事件数据
    
-   执行预设逻辑
    
-   触发回调函数
    

* * *

### Step 4：目标链执行回调

RSC 会在 **目标链（Target Chain）** 上调用某个函数。

例如：

```
mintReward(user, amount);
```

最终实现：

**源链事件 → 目标链操作**

* * *

# 四、Reactive Smart Contracts (RSCs)

**Reactive Smart Contracts** 是 Reactive Network 的核心组件。

它的作用是：

> 根据链上事件自动执行逻辑。

主要功能包括：

1.  监听事件
    
2.  解析事件数据
    
3.  执行回调函数
    
4.  触发跨链操作
    

RSC 相当于：

**区块链里的自动化机器人（Event Automation Agent）**

* * *

# 五、合约继承在系统中的作用

在 Reactive Network 开发中，合约通常会使用 **继承（Inheritance）**。

继承的作用包括：

1.  **复用代码**
    

基础 Reactive 合约已经提供：

-   事件监听
    
-   回调接口
    
-   网络通信
    

开发者只需要继承这些基础合约即可。

例如：

```
contract MyReactiveContract is ReactiveBaseContract
```

* * *

2.  **扩展功能**
    

开发者可以在子合约中添加：

-   自定义事件处理逻辑
    
-   自定义回调函数
    
-   跨链交互逻辑
    

* * *

# 六、事件（Event）机制

在 Solidity 中，**Event** 用于记录链上行为。

示例：

```
event TransferTriggered(address user, uint256 amount);
```

当事件被触发时：

```
emit TransferTriggered(msg.sender, amount);
```

事件的作用：

1.  记录交易日志
    
2.  供外部系统监听
    
3.  触发 Reactive Network 的响应机制
    

* * *

# 七、跨链回调机制

Reactive Network 通过 **跨链回调（Cross-chain Callback）** 实现不同区块链之间的交互。

基本原理：

1.  **链A触发事件**
    
2.  **Reactive Network监听事件**
    
3.  **Reactive合约接收事件**
    
4.  **在链B执行回调函数**
    

例如：

```
Source Chain: Event Triggered
      ↓
Reactive Network Listener
      ↓
Reactive Smart Contract
      ↓
Target Chain Callback
```

* * *

# 八、技术优势

Reactive Network 的跨链事件机制具有以下优势：

### 1\. 自动化执行

无需人工操作，事件发生后系统自动执行。

* * *

### 2\. 跨链交互

可以连接多个区块链，实现跨链应用。

* * *

### 3\. 模块化开发

通过 **合约继承**，开发者可以快速构建复杂系统。

* * *

### 4\. 事件驱动架构

相比传统智能合约：

```
用户交易 → 合约执行
```

Reactive 模式：

```
事件发生 → 自动触发逻辑
```

更适合自动化应用。

* * *

# 九、总结

本次学习主要理解了 **Reactive Network 的跨链事件机制**：

1.  Reactive Network 采用 **事件驱动架构**。
    
2.  当某条链上触发 **Event** 时，Reactive Network 会监听该事件。
    
3.  **Reactive Smart Contracts (RSCs)** 会接收事件并执行逻辑。
    
4.  最终可以在 **另一条链上执行回调函数**，实现跨链交互。
    

通过 **合约继承、事件机制和跨链回调**，开发者可以构建复杂的 **跨链自动化应用系统**。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

### **一、课程概述**

-   主题：响应式合约（RCs）基础及技术概念
    
-   目标：
    
    1.  理解 RCs 与传统智能合约的区别
        
    2.  掌握控制反转（Inversion of Control）概念
        
    3.  了解 RCs 如何自主监控区块链事件并执行操作
        
    4.  探索 RCs 的实际应用场景
        

* * *

### **二、RCs 与传统智能合约的区别**

| 特性 | 传统智能合约 | 响应式合约 (RCs) |
| --- | --- | --- |
| 执行方式 | 被动，需要外部账户调用 | 主动，持续监控并自主执行 |
| 触发条件 | 外部交易（EOA调用） | 区块链事件或状态变化 |
| 控制权 | 在调用者手中 | 控制权在合约自身（IoC） |
| 应用场景 | 基础逻辑操作（如转账） | 自动化交易、数据收集、套利、再平衡等 |

* * *

### **三、关键概念**

1.  **响应性（Reactivity）**
    
    -   RC 能持续监听区块链事件，并在条件满足时自动执行操作。
        
    -   思路：观察 → 判断 → 执行。
        
2.  **控制反转（Inversion of Control, IoC）**
    
    -   控制权从外部调用者转向合约自身。
        
    -   合约自主决定何时执行逻辑，而不依赖外部交易触发。
        
3.  **事件监控与自动响应**
    
    -   订阅区块链事件或状态变化（如价格更新、交易完成、流动性变化）。
        
    -   根据预设规则自动执行动作（下单、套利、重平衡等）。
        

* * *

### **四、实际应用场景**

1.  **数据收集**：从预言机获取链外数据，更新链上状态
    
2.  **UniSwap 停止订单**：价格达到阈值自动买卖
    
3.  **DEX 套利**：发现价格差异时自动套利
    
4.  **资金池再平衡**：自动调整池中资产比例
    

* * *

### **五、理解小结**

-   RC = 传统合约 + 主动性 + 自动事件响应
    
-   关键点：**主动监控 + 控制反转 + 自动执行规则**
    
-   适合**需要实时决策和自动化操作的场景**
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->


## 1️⃣ 理解 Reactive 与传统 EVM 的根本区别

-   **传统 EVM 智能合约**：
    
    -   合约是“被动”的：它只在交易调用时执行（transaction-driven）。
        
    -   外部事件或条件不会主动触发合约逻辑，你必须显式发送交易来改变状态。
        
    -   比如 ERC20 的 `transfer`，必须有人发交易才会执行。
        
-   **Reactive 智能合约**：
    
    -   合约是“主动”的：它可以订阅事件，一旦事件发生，自动触发对应逻辑（event-driven）。
        
    -   执行逻辑与事件解耦，形成 **Event → Reactive Logic → Callback Transaction** 链路。
        
    -   类似前端的 React：状态变了（事件来了），系统自动响应（执行逻辑）
        

## 2️⃣ 核心架构：ReactVM + Reactive Network

-   **ReactVM**：
    
    -   类似 EVM，但加入了事件监听和调度机制。
        
    -   支持在链上或链下处理事件逻辑（可按需选择同步/异步处理）。
        
    -   执行逻辑的方式不再依赖主动交易触发，而是基于事件订阅。
        
-   **Reactive Network**：
    
    -   提供事件分发、状态同步、Callback Transaction 发送的基础设施。
        
    -   负责把链上事件传递给 Reactive Logic，并把逻辑执行结果发回链上。
        

**画一个简化链路：**链上事件 (Event) → Reactive Network 接收→ Reactive Logic 处理→ 生成 Callback Transaction→ 链上执行 → 更新状态  

## 3️⃣什么是事件驱动智能合约（Reactive Smart Contracts）

**定义**：合约逻辑不再只是响应交易，而是响应链上或链下事件，自动执行。

-   **特点**：
    
    1.  **自动化响应**：事件触发逻辑，不需要用户主动调用。
        
    2.  **可组合**：多个事件可以绑定不同逻辑，形成复杂业务流程。
        
    3.  **链下计算支持**：一些逻辑可以在链下执行，提高效率。  
        

**应用场景示例**：

-   NFT 盲盒开箱：购买事件触发生成随机 NFT。
    
-   DeFi 风控：价格波动事件触发清算逻辑。
    
-   DAO 自动管理：投票事件触发资金分配。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
