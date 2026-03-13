---
timezone: UTC+8
---

# yangyang-hub

**GitHub ID:** yangyang-hub

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->
# Oracles 学习笔记

## 1\. Oracle 的作用

**Oracle（预言机）** 是连接 **区块链（on-chain）** 和 **现实世界数据（off-chain）** 的桥梁。

因为：

-   智能合约运行在 **确定性环境（deterministic environment）**
    
-   无法直接访问外部数据（例如 API、天气、价格）
    

所以需要 **Oracle 将链下数据带到链上**。

常见数据类型：

-   价格数据（ETH/USD）
    
-   天气数据
    
-   体育比赛结果
    
-   IoT设备数据
    
-   政府或公共数据库
    

* * *

# 2\. Oracle Problem（预言机问题）

问题核心：

> 如何在不破坏 **去中心化和信任最小化** 的情况下，把链下数据带到链上？

挑战：

-   数据来源是否可信
    
-   是否存在单点故障
    
-   是否可能被操纵
    

* * *

# 3\. Oracle 的工作流程

基本流程：

1️⃣ Oracle 从 **外部数据源** 获取数据  
例如：

-   API
    
-   交易所
    
-   IoT设备
    

2️⃣ Oracle 对数据进行验证或聚合

3️⃣ Oracle 使用 **私钥签名交易**

4️⃣ 将数据提交到 **区块链智能合约**

* * *

# 4\. 提高安全性的方式

为了防止数据被操控，Oracle网络通常采用：

### ① 多数据源聚合

从多个数据源获取数据，减少单点风险

### ② 多节点网络

多个 Oracle 节点提供数据

### ③ Multisig（多重签名）

需要多个节点签名才能提交数据

优点：

-   防止单个节点作恶
    
-   提高可信度
    

* * *

# 5\. 常见 Oracle 项目

| 项目 | 特点 |
| --- | --- |
| Chainlink | 最大的去中心化预言机网络 |
| Band Protocol | 跨链 Oracle 网络 |

* * *

# 6\. Oracle 的应用场景

### DeFi

价格预言机

用途：

-   借贷利率
    
-   清算
    
-   资产兑换
    

例子：  
ETH/USD price feed

* * *

### 保险

触发自动赔付

例子：

-   台风
    
-   地震
    
-   航班延误
    

* * *

### 体育竞猜

Oracle提供比赛结果

* * *

# 7\. Chainlink 示例（价格预言机）

智能合约获取 ETH/USD 价格：

```
function getLatestPrice() public view returns (int)
```

调用：

```
priceFeed.latestRoundData()
```

返回：

-   price
    
-   roundID
    
-   timestamp 等信息
    

作用：  
获取 **最新 ETH/USD 价格**

* * *

# 8\. Oracle 的限制

智能合约 **不能主动运行**。

必须由：

**EOA（Externally Owned Account）**  
发起交易。

所以：

-   Oracle数据更新
    
-   合约函数调用
    

都需要 **用户或外部交易触发**。

这意味着：

> 智能合约不能真正“实时响应”。

* * *

# 9\. Reactive Contracts（RC）

Reactive Contracts 的特点：

**监听事件 → 自动执行操作**

例如：

```
监听 Oracle 价格更新
→ 自动执行清算
→ 自动执行交易
```

RC 可以：

-   监听链上事件
    
-   跨链触发交易
    
-   自动执行逻辑
    

* * *

# 10\. Oracle + Reactive Contracts

结合后可以实现：

```
现实世界事件
        ↓
Oracle 上传数据
        ↓
Reactive Contract 监听事件
        ↓
自动执行链上操作
```

例子：

| 事件 | 自动操作 |
| --- | --- |
| ETH价格暴跌 | 自动清算 |
| 天气灾害 | 自动保险赔付 |
| 比赛结束 | 自动结算竞猜 |

* * *

# 11\. 核心总结

**Oracle**

-   连接链上与链下数据
    
-   解决智能合约无法访问外部世界的问题
    

**Oracle Problem**

-   如何保证数据可信
    

**解决方案**

-   多节点
    
-   多数据源
    
-   Multisig
    
-   去中心化 Oracle 网络
    

**Oracle + Reactive Contracts**

-   实现真正的 **实时自动化 Web3 应用**
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->


# Subscriptions

## 1\. 什么是 Subscription

**Subscription（订阅）** 是 Reactive Contracts 的核心机制，用来让合约**自动监听其他合约的事件并触发逻辑**。

流程：

1.  合约通过 `subscribe()` 注册要监听的事件
    
2.  当链上出现符合条件的 event log
    
3.  Reactive Network 通知订阅合约
    
4.  合约执行 `react()` 处理事件
    

简化流程：

```
Event 发生
     ↓
Reactive Network 检测到
     ↓
通知订阅的 Reactive Contract
     ↓
调用 react()
```

* * *

# 2\. 订阅服务接口（ISubscriptionService）

Reactive Contracts 通过 **系统合约 SubscriptionService** 进行订阅。

```
function subscribe(
    uint256 chain_id,
    address _contract,
    uint256 topic_0,
    uint256 topic_1,
    uint256 topic_2,
    uint256 topic_3
) external;
```

取消订阅：

```
function unsubscribe(...)
```

### 参数说明

| 参数 | 含义 |
| --- | --- |
| chain_id | 事件来源链 |
| _contract | 触发事件的合约 |
| topic_0~3 | event topics |

Topics 对应 Solidity 事件的 indexed 参数。

* * *

# 3\. 订阅条件规则

订阅是通过 **chain + contract + topics** 进行过滤。

### Wildcard（通配符）

| 类型 | 写法 |
| --- | --- |
| 任何 chain | 0 |
| 任何 contract | address(0) |
| 任何 topic | REACTIVE_IGNORE |

### 必须满足

**至少一个参数必须是具体值**

否则订阅没有意义。

* * *

# 4\. 常见订阅示例

## 订阅某个合约的所有事件

```
service.subscribe(
    CHAIN_ID,
    contractAddress,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE
);
```

* * *

## 订阅某个 Event 类型

例如 Uniswap Sync event

```
service.subscribe(
    CHAIN_ID,
    address(0),
    topic0,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE
);
```

* * *

## 同时限定合约 + event

```
service.subscribe(
    CHAIN_ID,
    contractAddress,
    topic0,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE
);
```

* * *

# 5\. Constructor 订阅（静态订阅）

通常在 **constructor 中初始化订阅**：

```
constructor(...) {
    if (!vm) {
        service.subscribe(...);
    }
}
```

原因：

Reactive Contract **会在两个环境部署**

| 环境 | 作用 |
| --- | --- |
| Reactive Network | 有 system contract |
| ReactVM | 没有 system contract |

所以要避免 ReactVM 执行 subscribe。

* * *

# 6\. IReactive 接口

Reactive Contracts 必须实现：

```
function react(LogRecord calldata log)
```

### LogRecord 结构

```
struct LogRecord {
    chain_id
    _contract
    topic_0
    topic_1
    topic_2
    topic_3
    data
    block_number
    block_hash
    tx_hash
}
```

作用：

提供完整的 **event log 信息**

* * *

# 7\. Callback 机制

当 Reactive Contract 处理事件后：

会触发 **Callback**

```
event Callback(
    uint256 chain_id,
    address _contract,
    uint64 gas_limit,
    bytes payload
);
```

作用：

让 Reactive Network 执行后续操作。

* * *

# 8\. 动态订阅（Dynamic Subscription）

Reactive Contracts 可以根据事件：

-   自动订阅
    
-   自动取消订阅
    

流程：

```
事件触发
 ↓
react() 执行
 ↓
emit Callback
 ↓
Reactive Network 调用 subscribe/unsubscribe
```

* * *

# 9\. 动态订阅示例逻辑

react() 中：

```
if topic == SUBSCRIBE
    -> emit callback 调用 subscribe()

if topic == UNSUBSCRIBE
    -> emit callback 调用 unsubscribe()

else
    -> 处理业务逻辑
```

示例：

```
if (log.topic_0 == SUBSCRIBE_TOPIC_0) {
    emit Callback(... subscribe payload ...);
}
```

* * *

# 10\. Subscription 限制

Reactive Network 对订阅有一些限制：

### 不支持

❌ 不支持范围匹配

```
<
>
range
bitwise
```

* * *

❌ 不支持复杂逻辑

例如：

```
(topicA OR topicB)
```

只能通过 **多次 subscribe** 实现。

* * *

❌ 不允许

```
所有 chain + 所有 contract
```

* * *

# 11\. 重复订阅

允许重复 subscribe。

但：

-   每次调用都会产生 gas
    
-   系统不会自动去重
    

开发者需要 **保证 idempotency**。

* * *

# 12\. unsubscribe 成本高

原因：

需要 **查找并删除存储中的 subscription**。

因此建议：

-   减少 unsubscribe
    
-   尽量设计长期订阅
    

* * *

# 13\. 最佳实践

### 1️⃣ constructor 初始化订阅

避免 runtime 注册。

* * *

### 2️⃣ 使用 topic 精确过滤

减少不必要事件。

* * *

### 3️⃣ 避免过多 subscription

否则会产生 **combinatorial explosion**。

* * *

### 4️⃣ react() 逻辑要轻量

因为 callback 有 **gas limit**。

* * *

# 14\. 总结

Reactive Contracts 订阅机制核心：

1️⃣ 使用 `subscribe()` 监听事件  
2️⃣ Reactive Network 捕获 event  
3️⃣ 调用 `react()`  
4️⃣ 合约处理事件并 emit Callback  
5️⃣ 网络执行后续操作

核心能力：

-   跨链事件监听
    
-   自动触发逻辑
    
-   动态订阅管理
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->




# Reactive Contracts (RCs) 架构与执行机制

## 一、 双重执行环境：一个合约，两个状态

在 Reactive 网络中，每个部署的合约实际上运行在两个平行的实例中。它们代码完全相同，但**状态（变量）独立存储**。

1.  **Reactive Network (RN) 状态：**
    
    -   **角色：** 合约的“指挥部”。
        
    -   **职责：** 直接与系统合约交互，负责订阅/取消订阅原始链（ETH, Polygon 等）的事件。
        
2.  **ReactVM 状态：**
    
    -   **角色：** 隔离的“实验室”。
        
    -   **职责：** 专门执行业务逻辑（`react()` 函数），处理接收到的事件并生成回调请求。
        
    -   **限制：** 每个部署地址拥有独立的 ReactVM，不同地址的合约互不干扰。
        

* * *

## 二、 核心机制：如何识别与限制环境？

为了让同一套代码在两个环境中各司其职，系统通过 `detectVm()` 函数进行环境检测。

### 1\. 环境检测原理（Assembly Check）

系统通过检查特定地址 `0x...fffFfF`（系统合约地址）是否存在代码来判断环境：

-   **Code Size > 0** $\\rightarrow$ 在 **Reactive Network** 中（系统合约存在）。
    
-   **Code Size == 0** $\\rightarrow$ 在 **ReactVM** 中（受限环境，无系统合约）。
    

### 2\. 访问控制修饰器 (Modifiers)

-   `rnOnly`: 仅允许在 Reactive Network 执行（如：修改订阅列表、管理员暂停）。
    
-   `vmOnly`: 仅允许在 ReactVM 执行（如：响应事件逻辑）。
    

* * *

## 三、 变量与状态管理

由于合约在两个环境中运行，变量通常分为两组：

-   **RN 变量 (继承自** `AbstractReactive`**)：**
    
    -   `service`: 用于调用订阅服务的接口。
        
    -   `vm`: 布尔标志位，标记当前环境。
        
-   **ReactVM 变量 (业务逻辑相关)：**
    
    -   如 `triggered`, `done`, `threshold` 等。这些变量在 `react()` 逻辑中被修改，用于控制自动化行为。
        

* * *

## 四、 交易执行流 (Transaction Flow)

### 1\. 外部驱动交易 (Reactive Network 层)

-   **用户发起：** 管理员手动调用 `pause()` 或 `resume()`。这些函数使用 `rnOnly` 修饰，通过调用 `service.unsubscribe/subscribe` 来开关“传感器”。
    
-   **事件分发：** RN 监控到原始链事件后，将其派发给对应的 ReactVM 实例。
    

### 2\. 自动触发交易 (ReactVM 层)

-   **触发机制：** 无法由用户直接调用，仅在接收到订阅事件时由系统触发。
    
-   **执行逻辑：** 调用 `react()` $\\rightarrow$ 状态计算 $\\rightarrow$ 满足条件 $\\rightarrow$ 触发 **Callback**（回调交易）。
    
-   **最终行动：** Callback 请求被发回 Reactive Network，最终在目标链（如 Sepolia）上执行实际的链上动作（如执行 Swap）。
    

* * *

## 五、 典型代码逻辑梳理 (以 Uniswap 止损订单为例)

1.  **构造函数 (Constructor):**
    
    -   初始化所有变量。
        
    -   `if (!vm)`：如果是在 **RN** 环境，立即调用 `service.subscribe` 订阅 Uniswap 的 `Sync` 事件。
        
2.  **React 函数 (**`vmOnly`**):**
    
    -   接收事件日志 (`LogRecord`)。
        
    -   判断价格是否低于 `threshold`。
        
    -   如果满足，修改 `triggered = true` 并发出 `emit Callback(...)` 指令。
        

* * *

### 总结对比表

| 维度 | Reactive Network (RN) | ReactVM |
| 部署地址 | 0x... (主网地址) | 隔离的虚拟机环境 |
| 主要功能 | 管理订阅、行政管理 (Pause/Resume) | 运行业务逻辑、计算事件数据 |
| Gas 消耗 | 用户或系统支付 | 逻辑执行（通常极低或受限） |
| 系统调用 | 可调用 service.subscribe | 禁止直接调用订阅服务 |
| 输出结果 | 状态更新或订阅变更 | 触发跨链 Callback |
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->






# Reactive Contracts (RCs) 基础与核心概念

## 一、 核心定义：什么是反应式合约？

**反应式合约 (Reactive Contracts, RCs)** 是一种能够**自主监控**区块链事件，并根据预设逻辑**自动触发**后续动作的智能合约。

-   **传统合约** = 被动（Passive）。像一个等待拨打的电话号码。
    
-   **反应式合约** = 主动（Reactive）。像一个全天候运行的自动化传感器。
    

* * *

## 二、 关键技术范式：控制反转 (Inversion of Control, IoC)

这是理解 RCs 的核心灵魂。

### 1\. 传统模式：外部驱动

-   **执行者：** 必须由外部账户 (EOA) 或中心化机器人 (Bot) 发起交易。
    
-   **痛点：** 依赖私钥持有者在线，且机器人服务通常是中心化的，存在单点故障。
    

### 2\. RC 模式：控制反转

-   **执行者：** 合约通过监听链上事件（Topic 0）自主决定何时执行。
    
-   **价值：** 实现了**去中心化的自动化**。不再需要模拟人类签名的外部实体，只要输入和输出都在链上，逻辑就在 Reactive Network 中闭环运行。
    

* * *

## 三、 反应式合约的工作原理

一个 RC 的生命周期包含以下三个阶段：

1.  **订阅与监听 (Monitor)：** 开发者指定感兴趣的链、合约地址和特定事件（如 `Transfer`, `Swap`, `FlashLoan` 等）。
    
2.  **逻辑处理 (React)：** \* RC 是**有状态的 (Stateful)**，可以跨时间存储数据。
    
    -   当事件触发时，RC 根据当前事件数据 + 历史状态数据进行计算。
        
3.  **自主执行 (Execute)：** 计算达成条件后，RC 自动在目标 EVM 链上发起交易。
    

* * *

## 四、 四大典型应用场景

* * *

## 五、 总结：RC 带来的四大变革

-   **动态响应：** 从“请求-响应”转向“事件-驱动”。
    
-   **完全去中心化：** 消除了对第三方托管机器人和私钥管理服务的依赖。
    
-   **跨链互通：** 原生支持跨多个 EVM 兼容链的事件编排。
    
-   **高效可靠：** 减少人工干预带来的延迟和安全风险。
    

# Events 与 Callbacks

## 1\. Events（事件）

在 **Ethereum** 中，**Event** 用于让智能合约向链外传递信息。

特点：

-   写入 **transaction logs**
    
-   **不会改变区块链状态**
    
-   可以被 **dApp / 后端监听**
    
-   EVM 会对 event 进行 **索引**
    

常见用途：

-   监听 Token Transfer
    
-   监听价格变化（Oracle）
    
-   监听合约状态更新
    

* * *

## 2\. 定义与触发 Event

### 定义 Event

```
event PriceUpdated(string symbol, uint256 newPrice);
```

### 触发 Event

```
emit PriceUpdated("ETH", newEthPrice);
```

触发后：

```
Smart Contract
      ↓
Transaction Log
      ↓
dApp / Backend 监听
```

* * *

## 3\. Oracle 示例

使用 **Chainlink** 获取价格：

```
Chainlink Oracle
      ↓
Smart Contract 更新价格
      ↓
emit PriceUpdated
      ↓
dApp / Reactive Contract 监听
```

* * *

# 4\. Reactive Contracts 处理事件

Reactive Contracts 必须实现 `IReactive` 接口。

核心方法：

```
function react(LogRecord calldata log) external;
```

作用：

```
Event
   ↓
Reactive Network 监听
   ↓
调用 react(log)
   ↓
执行合约逻辑
```

* * *

## LogRecord 结构

事件信息会被打包为：

```
struct LogRecord {
    uint256 chain_id;
    address _contract;
    uint256 topic_0;
    uint256 topic_1;
    uint256 topic_2;
    uint256 topic_3;
    bytes data;
    uint256 block_number;
    uint256 block_hash;
    uint256 tx_hash;
}
```

包含：

-   来源链
    
-   事件合约
    
-   topics
    
-   数据
    
-   block / tx 信息
    

* * *

# 5\. Callback（跨链回调）

Reactive Contract 可以通过 **Callback** 在其他链执行交易。

### Callback Event

```
event Callback(
    uint256 indexed chain_id,
    address indexed _contract,
    uint64 indexed gas_limit,
    bytes payload
);
```

参数：

| 参数 | 说明 |
| --- | --- |
| chain_id | 目标链 |
| _contract | 目标合约 |
| gas_limit | gas 限制 |
| payload | ABI 编码的函数调用 |

* * *

## 触发 Callback

```
emit Callback(chain_id, contractAddr, gasLimit, payload);
```

流程：

```
Reactive Contract
      ↓ emit Callback
Reactive Network
      ↓
目标链发送交易
      ↓
调用目标合约
```

* * *

# 6\. 安全机制

Reactive Network 会自动：

-   将 **payload 第一个参数**
    
-   替换为 **ReactVM 地址**
    

所以 callback 函数：

```
第一个参数 = ReactVM address
```

* * *

# 7\. 整体流程

```
1 用户交易
2 合约 emit Event
3 Reactive Network 监听
4 调用 react()
5 emit Callback
6 跨链执行交易
```
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->









## 睿应层（Reactive Network）与传统 EVM 的区别

### 交互模式：被动（Passive）vs 主动（Reactive）

-   **传统 EVM（被动型）：** 智能合约本质上是“死代码”。除非外部账户（EOA）发起一笔交易并支付 Gas，否则合约永远不会执行。即使价格触发了清算线，如果没有“清算人”机器人主动发起交易，合约也不会自发运行。
    
-   **Reactive Network（主动型）：** 它引入了**状态监听与自动触发**机制。合约可以“订阅”其他链上的事件。一旦预设条件达成（如某个 DeFi 协议产生了一笔大额交易），Reactive 合约会自动在目标链上执行预定义的逻辑，无需人工干预。
    

### 跨链能力：孤岛 vs 全局观察者

-   **传统 EVM：** 每个 EVM 链都是一个独立的状态机。由于“预言机问题”和跨链桥的限制，一个合约很难实时、原生且无感地获知另一条链上的状态变化。
    
-   **Reactive Network：** 它的架构设计使其充当**跨链编排层**。它可以并行监控多个链（如 Ethereum, Polygon, BSC）的日志（Logs）和事件。这使其不仅仅是一个计算引擎，更像是一个跨链的“神经中枢”。
    

### 计算架构：同步 vs 异步流水线

-   **传统 EVM：** 采用同步执行模型。所有的交易都在一个区块内按顺序打包、执行，并改变状态。这种模式限制了复杂逻辑的处理效率。
    
-   **Reactive Network：** 采用**异步反应式编程模型**。它的执行逻辑是解耦的：
    
    1.  **捕获（Capture）：** 实时捕获源链事件。
        
    2.  **反应（React）：** 在 Reactive 层进行逻辑计算。
        
    3.  **执行（Execute）：** 将结果异步反馈回目标链。 这使得它能够处理极为复杂的自动化策略，而不会阻塞主链的共识。
        

### 经济模型与 Gas 消耗

-   **传统 EVM：** 用户为单次交互支付 Gas。对于需要频繁监控的操作（如网格交易），用户必须不断运行链下机器人，并支付高昂的维护和重复交易成本。
    
-   **Reactive Network：** 通过在底层协议级别整合自动化，它优化了\*\*“条件触发式”任务\*\*的成本。它减少了对第三方中心化机器人（Keeper）的依赖，从而降低了滑点风险和MEV攻击的可能性。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
