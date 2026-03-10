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
