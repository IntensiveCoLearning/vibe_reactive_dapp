---
timezone: UTC+8
---

# lorstyang

**GitHub ID:** lorstyang

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-19
<!-- DAILY_CHECKIN_2026-03-19_START -->
在构思黑客松的一个idea：基于个人IP体量成长的成长股Reactive合约系统

最近发现了一些很早之前关注的自媒体号主，都是早期看到了质量很高的视频然后关注的，然后果不其然的过了些时间粉丝增长很快，于是想到了成长股。

思路大概是：理论上IP主人可以构建一个合约，用于筹备早期启动/持续资金，并定期对资金的去向做说明，后期再通过一定的方式回馈给早期投资者。吸引方式可以是个人履历，也可以是创造的媒体内容本身。相当于一个微缩版的创业投资结构。
<!-- DAILY_CHECKIN_2026-03-19_END -->

# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->

继续昨天的定时合约，这几天有点忙，学习时间较少
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->


![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/lorstyang/images/2026-03-17-1773750865790-image.png)

研究下定时自动化的代码
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->



回顾一下之前的 worksop：**Reactive Workshop & Ambassador Program**
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->




![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/lorstyang/images/2026-03-14-1773511860120-image.png)

昨天的作业做的有点问题，修改一下  
相关环境变量

```dotenv
UNISWAP_V2_PAIR_ADDR=0xD659534c8ce723e4D70Ff966C6D039219aB726f0
TOKEN0_ADDR=0x3B0c20C1e9bf2f335b0fB91C46A0A78C455f2CE1
TOKEN1_ADDR=0x8Db52449b7A0cf95d74C71e8fb2e29A02406066a
CALLBACK_ADDR=0xE0753E200Be4B33BA988Cec110BDC1570811c96e
DIRECTION_BOOLEAN=true
EXCHANGE_RATE_DENOMINATOR=1000
EXCHANGE_RATE_NUMERATOR=1200
TOKEN_ADDR=0x3B0c20C1e9bf2f335b0fB91C46A0A78C455f2CE1
```

  
[https://lasna.reactscan.net/address/0xe68f43aa36bddef60bbc5dea7610dd24ba22937f/contract/0x0e61fc76ec02f3231b2dfe9efa4ca0cbdb83d53a?screen=transactions](https://lasna.reactscan.net/address/0xe68f43aa36bddef60bbc5dea7610dd24ba22937f/contract/0x0e61fc76ec02f3231b2dfe9efa4ca0cbdb83d53a?screen=transactions)
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->







![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/lorstyang/images/2026-03-14-1773501189541-image.png)![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/lorstyang/images/2026-03-14-1773501219322-image.png)![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/lorstyang/images/2026-03-14-1773501885993-image.png)![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/lorstyang/images/2026-03-14-1773502403263-image.png)

[https://lasna.reactscan.net/tx/0x1c2a227a72318e4322e0ef4353a616d77c32bda25199b93b5e267742205c047b](https://lasna.reactscan.net/tx/0x1c2a227a72318e4322e0ef4353a616d77c32bda25199b93b5e267742205c047b)
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->









![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/lorstyang/images/2026-03-13-1773413636398-image.png)

试试Uniswap V2 Stop Order Demo，没做完明天继续
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->










# ReactVM

Reactive 合约同时存在两个环境：

```
Reactive Network   +   ReactVM
```

* * *

# 整体架构

```
        Origin Chain
     (Ethereum / BNB / Polygon)
              │
              │ Events
              ▼
       Reactive Network
              │
              │ dispatch
              ▼
            ReactVM
              │
              │ reaction logic
              ▼
      Callback → Destination Chain
```

核心思想：

```
Event → React → Callback
```

* * *

# Reactive Contract 双实例

每个 Reactive Contract 实际存在 **两个实例**

```
            Reactive Contract
                    │
        ┌───────────┴───────────┐
        │                       │
Reactive Network Instance   ReactVM Instance
        │                       │
  管理 / 订阅事件              处理事件逻辑
```

-   Same Code
    
-   Different State
    
-   Different Execution Context
    

* * *

# 两个运行环境

## Reactive Network

本质：EVM Blockchain

职责：

-   User interaction
    
-   Event subscription
    
-   System contracts
    
-   Administrative control
    

结构：

```
User
  │
  ▼
Reactive Network
  │
  ├── Event Subscriptions
  ├── Admin Functions
  └── System Contracts
```

* * *

## ReactVM

本质：Event Processing Engine

职责：

-   process events
    
-   execute contract logic
    
-   trigger callbacks
    

特点：

-   Isolated
    
-   Parallel
    
-   Event-driven
    

结构：

```
        Event
          │
          ▼
        ReactVM
          │
          ▼
     Contract Logic
          │
          ▼
      Callback
```

* * *

# ReactVM 分配规则

-   ReactVM 由 **部署地址 (EOA)** 决定。
    
-   如果同一地址部署多个合约会 Shared state 和 Internal interaction allowed
    
-   官方建议 Different logic → Different ReactVM
    

* * *

# ReactVM 执行机制

ReactVM **不会被用户调用**

它只会被 Origin Chain Events 触发

完整流程：

```
Origin Chain Event
        │
        ▼
Reactive Network detects event
        │
        ▼
Event dispatched to ReactVM
        │
        ▼
react() function executed
        │
        ▼
Callback transaction emitted
```

* * *

# Reactive Contract状态分离

```
Reactive Network State
    ≠
ReactVM State
```

原因：

-   Parallel execution
    
-   Event scalability
    
-   Deterministic processing
    

架构：

```
ReactVM A State
ReactVM B State
ReactVM C State
        │
        ▼
Reactive Network Global State
```

* * *

# 🔍 Execution Context

Reactive 合约需要知道当前运行在哪个环境：

示意：

```
        Contract Call
              │
              ▼
      Execution Context
       │             │
       ▼             ▼
Reactive Network   ReactVM
```

* * *

# 交易类型

Reactive 架构中有 **两种交易来源**

-   User Transactions
    
-   Event Transactions
    

* * *

## User Transactions

发生位置：Reactive Network

示意：

```
User
 │
 ▼
Reactive Network
 │
 ├── pause()
 ├── resume()
 └── configuration
```

* * *

## 2️⃣ Event Transactions

发生位置：ReactVM

流程：

```
Origin Chain Event
        │
        ▼
Reactive Network
        │
        ▼
ReactVM
        │
        ▼
react()
        │
        ▼
Callback
```

* * *

# Cross-Chain Callback

ReactVM 可以触发Callback Transaction，执行跨链操作。

结构：

```
ReactVM
  │
  ▼
Callback Request
  │
  ▼
Reactive Network
  │
  ▼
Destination Chain
```

* * *

# 并行执行架构

Reactive Network 通过 **ReactVM 分片执行**

示意：

```
Events
 │ │ │ │
 ▼ ▼ ▼ ▼
VM1 VM2 VM3 VM4
 │   │   │   │
 ▼   ▼   ▼   ▼
Logic Execution
```

优势：

-   High throughput
    
-   High scalability
    
-   Event isolation
    

* * *

# Reactive Contract 生命周期

```
1 Deploy Contract
        │
        ▼
2 Subscribe Events
        │
        ▼
3 Event Emitted
        │
        ▼
4 Event routed to ReactVM
        │
        ▼
5 react() executed
        │
        ▼
6 Callback emitted
        │
        ▼
7 Destination chain action
```

* * *

# 总结

```
Reactive Contract
      │
      ▼
Dual State
      │
 ┌────┴────┐
 │         │
Network   ReactVM
 │         │
Admin     Logic
 │         │
Events → Reaction → Callback
```
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->











![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/lorstyang/images/2026-03-10-1773159556314-image.png)

[https://lasna.reactscan.net/address/0x81a64a537e30eee5d8012886d036e6353013ac08/4](https://lasna.reactscan.net/address/0x81a64a537e30eee5d8012886d036e6353013ac08/4)

# 1\. Reactive Library 概述

**Reactive Library** 是 Reactive Network 提供的一组 **抽象合约 + 接口库**，用于构建 **Reactive Contracts**。

核心能力包括：

-   事件订阅 (Event Subscription)
    
-   回调机制 (Callbacks)
    
-   支付与费用结算 (Payments)
    
-   与 Reactive Network System Contract 交互
    

安装方式（Foundry）：

forge install Reactive-Network/reactive-lib

* * *

# 2\. Reactive Contract 架构

Reactive Library 提供了一套 **基础抽象合约体系**：

```
AbstractPayer
 ├── AbstractReactive
 │     └── AbstractPausableReactive
 │
 └── AbstractCallback
```

* * *

## AbstractPayer

**资金与支付基础设施**

提供：

-   authorized sender
    
-   pay()
    
-   coverDebt()
    
-   receive()
    

核心作用：

> 给 Reactive Contract 提供资金管理能力

* * *

## AbstractReactive

继承：

```
AbstractReactive is AbstractPayer
```

提供：

-   system contract 接入
    
-   subscription service
    
-   VM / RN execution mode
    
-   react() 入口
    

核心作用：

> Reactive Contract 的基础运行框架

* * *

## AbstractPausableReactive

继承：

```
AbstractPausableReactive is AbstractReactive
```

提供：

-   pause()
    
-   resume()
    
-   pausable subscriptions
    

核心作用：

> 给 Reactive Contract 增加暂停订阅功能

* * *

## AbstractCallback

继承：

```
AbstractCallback is AbstractPayer
```

提供：

-   callback authorization
    
-   rvm\_id
    
-   vendor proxy
    

核心作用：

> 保护 callback 交易来源

# 3\. 核心接口

Reactive Library 提供多个 Interface。

* * *

## IPayable

用于资金管理。

功能：

-   接收 ETH
    
-   查询债务
    

debt(contract)

返回某个 Reactive Contract 的欠款。

* * *

## IPayer

简化支付接口：

-   pay()
    
-   receive()
    

* * *

## IReactive

Reactive Contract 的 **核心接口**。

定义：

### LogRecord

Reactive Network 向合约传递的事件数据：

| 字段 | 含义 |
| --- | --- |
| chain_id | 来源链 |
| contract | 触发事件的合约 |
| topic_0~3 | Event topics |
| data | Event data |
| block_number | 区块 |
| tx_hash | 交易 |
| log_index | 日志索引 |

* * *

### Callback Event

Reactive Contract 触发回调时产生：

Callback

包含：

-   chain\_id
    
-   contract
    
-   gas\_limit
    
-   payload
    

* * *

### react()

Reactive Contract 的核心入口函数：

react(log)

Reactive Network 将事件推送给该函数处理。

* * *

## ISubscriptionService

用于 **管理事件订阅**。

功能：

subscribe()

unsubscribe()

订阅条件：

| 参数 | 含义 |
| --- | --- |
| chain_id | 链 |
| contract | 合约 |
| topic_0~3 | Event topics |

支持：

Wildcard 匹配（REACTIVE\_IGNORE）。

* * *

## ISystemContract

SystemContract =

IPayable + ISubscriptionService

用于：

-   支付管理
    
-   订阅管理
    

* * *

# 4\. Reactive Network 系统合约

Reactive Network 运行依赖 **三个核心系统合约**。

* * *

## SystemContract

职责：

-   Reactive Contract 费用管理
    
-   访问控制（白名单 / 黑名单）
    
-   CRON 事件
    

* * *

## CallbackProxy

作用：

负责 **执行回调交易**

功能：

-   发送 callback transaction
    
-   管理 deposits
    
-   计算 gas cost
    
-   处理 kickback
    

* * *

## SubscriptionService

负责：

-   事件订阅管理
    
-   过滤事件
    
-   topic 匹配
    

* * *

# 5\. CRON 机制

Reactive Network 提供 **链上定时任务机制**。

通过 SystemContract 定期触发事件。

Reactive Contract 可以订阅这些事件实现：

-   自动执行
    
-   定时任务
    
-   自动策略
    

* * *

## CRON 事件

| Event | Interval | Approx Time |
| --- | --- | --- |
| Cron1 | 每个区块 | ~7s |
| Cron10 | 每10块 | ~1分钟 |
| Cron100 | 每100块 | ~12分钟 |
| Cron1000 | 每1000块 | ~2小时 |
| Cron10000 | 每10000块 | ~28小时 |

每个事件包含：

number = 当前 block number

* * *

# 10\. Reactive Contract 工作流程

整体流程：

```
External Event
     ↓
Reactive Network Indexer
     ↓
Subscription Filter
     ↓
Reactive Contract react()
     ↓
逻辑处理
     ↓
Callback Transaction
```
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->













![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/lorstyang/images/2026-03-10-1773157873570-image.png)

-   最困难的居然是空钱包搞到Base sepolia ETH，试了挺多只有这个桥接的能用[https://superbridge.app/base-sepolia](https://superbridge.app/base-sepolia)
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->
















# 传统 Solidity 智能合约的问题

-   在以太坊或其他 EVM 链上，传统智能合约不会“自动响应事件”
    
-   现实中很多 dApp 依赖其他方式来监控事件并触发交易：
    
    -   机器人（bots）
        
    -   Keeper 网络
        
    -   后台服务器
        

# Reactive Network 想解决什么问题

-   让智能合约对区块链事件自动反应。
    
-   提出了一种新的智能合约：Reactive Smart Contract (RSC)
    

# Reactive Network 的核心概念

-   Reactive Smart Contract
    
-   Event-driven（事件驱动）
    

```
Event -> Contract Logic -> Transaction
```

-   跨链自动执行
    

```
Ethereum event
↓
触发
↓
Polygon transaction
```

-   ReactVM
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
