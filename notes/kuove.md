---
timezone: UTC+8
---

# kuove

**GitHub ID:** kuove

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
根据第二阶段notion笔记自学
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

Reactive Network 的核心本质是：**它不是在处理“状态”，而是在处理“状态的增量（Events）”。**

* * *

### 1\. 核心架构：Reactive Node 的运作逻辑

传统的区块链节点只验证交易，而 Reactive 节点（Reactive Nodes）在运行 **ReactVM**，它的工作流程分为三个阶段：

-   **捕获阶段 (Capture):** 节点通过低延迟的索引器（Indexer）实时监听源链（如 Ethereum, BSC）的事件日志。
    
-   **计算阶段 (Compute):** 触发 Reactive Smart Contract (RSC)。这里不只是简单的 `if-else`，RSC 拥有自己的状态存储，可以根据历史事件进行复杂逻辑计算。
    
-   **输出阶段 (Output):** 根据计算结果，RSC 生成一个 **Outbound Transaction**。这个交易通过跨链中继发送回目标链。
    

* * *

### 2\. 深度特性：Reactive Smart Contracts (RSC)

RSC 与普通的 Solidity 合约在编写逻辑上有显著区别。在 Reactive Network 上，合约通常需要实现 `IReactive` 接口：

-   **订阅机制 (**`subscribe`**):** 合约在构造时会指定它要“听”谁。比如：监听 Uniswap V3 在以太坊上的所有 `Swap` 事件。
    
-   **原子性与异步性:** RSC 的执行在 Reactive 网络上是原子的，但它对外部链的影响是异步的（通过回调）。
    
-   **无 Gas 消耗的监听:** 用户不需要为“监听”支付 Gas，只有当 RSC 逻辑触发并向主链发送回调交易时，才需要支付目标链的 Gas。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->


# Reative NetWork

### 1\. 核心定义：什么是 Reactive Network？

Reactive Network 是一个 **EVM 兼容的执行层**，它的核心创新在于引入了 **Reactive Smart Contracts (RSC，响应式智能合约)**。

-   **传统合约的局限：** 传统的智能合约（如以太坊上的合约）是“被动”的。除非用户或机器人（Bot）发起一笔交易来触发它，否则合约逻辑永远不会主动执行。
    
-   **Reactive Network 的突破：** 它让智能合约具备了“主动监听”和“自动响应”的能力。合约可以跨链监听特定事件（Event），并在条件满足时自动触发逻辑，无需外部的链下脚本或中心化预言机（Oracle）干预。
    

### 2\. 核心技术组件

-   **Reactive Smart Contracts (RSC):** 这种合约采用了“控制反转”（Inversion of Control）的设计模式。开发者可以订阅其他链（如 Ethereum, Polygon, Arbitrum 等）的事件日志，一旦发生匹配事件，RSC 会在 Reactive Network 上自动运行逻辑。
    
-   **ReactVM:** 这是一个专门优化的并行执行引擎。不同于以太坊的串行处理，ReactVM 支持并行处理交易，从而实现极高的吞吐量和极低的延迟。
    
-   **跨链协同 (Callbacks):** 当 Reactive 网络上的 RSC 执行完逻辑后，它可以向目标链（Destination Chain）发送“回调（Callback）”交易，从而实现真正的全链自动化。
    

### 3\. 主要应用场景

Reactive Network 实际上充当了 Web3 的\*\*“链上自动化大脑”\*\*，常见的用途包括：

-   **去中心化止损/清算保护：** 当 Uniswap 上的价格跌破某个值，合约自动在 Aave 执行清算或补仓逻辑，无需依赖中心化的清算机器人。
    
-   **全链资产管理：** 监控多条链上的收益率，自动进行调仓和复利操作。
    
-   **动态 NFT：** 例如，当某支球队在现实比赛中进球（数据上链后），对应的 NFT 属性自动发生改变。
    
-   **自动化跨链桥：** 替代传统的验证人模型，通过监听源链事件直接触发目标链的铸造逻辑。
    

### 4\. 项目背景与代币

-   **前身：** 该项目由 **PARSIQ** 团队发起。PARSIQ 多年来一直深耕区块链数据索引和监控，Reactive Network 是他们从“数据监控”向“数据驱动执行”的重大战略升级。
    
-   **代币 ($REACT):** 随着项目的发展，原有的 PARSIQ 代币 ($PRQ) 正在逐步过渡/集成到新的生态中。$REACT 是网络的原生代币，用于支付处理费、节点激励和治理。
    

### 5\. 总结

可以把 **Reactive Network** 理解为 **Web3 世界的 IFTTT (If This Then That)**。

它打破了区块链“孤岛”和“被动触发”的限制，为开发者提供了一种完全去中心化、无需链下干预的方式来构建复杂的自动化流。如果你是开发者，它能让你用 Solidity 编写出能够“感知”全链动态并自主运行的程序。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
