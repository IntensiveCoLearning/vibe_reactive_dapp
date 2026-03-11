---
timezone: UTC+8
---

# Jade

**GitHub ID:** JadeTwinkle

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
# Reactive Staking Season 的结构

Reactive 的 staking 不是永久池，而是 **Season 机制**：

```
Season 1
Season 2
Season 3
Season 4
Season 5
```

每个 Season：

-   有新的 **reward pool**
    
-   有新的 **lock period**
    
-   用户可以 **restake**
    

例如之前 Season：

-   30天池
    
-   60天池
    
-   90天池
    

奖励来自同一个 pool。

特点：

-   锁仓越久 → 奖励越高
    
-   参与人数越多 → APY下降
    

因为奖励是 **按比例分配**。

* * *

# Season 5 的核心目标

Season 5 的设计主要有 **3个目标**。

### 1 提升网络安全性

Reactive 是 PoS 网络。

更多 staking → 更安全。

```
更多 token 被锁
      ↓
攻击成本更高
      ↓
网络更安全
```

* * *

### 2 提供长期流动性

如果 token 都在交易所：

```
价格波动大
卖压大
```

staking 可以：

```
减少 circulating supply
稳定市场
```

* * *

### 3 激励生态参与

Season staking 是一种：

**早期网络 bootstrap 机制**

很多区块链都这么做。

例如：

-   Avalanche
    
-   Polkadot
    

都会在早期提供较高 APY。

Reactive 初期 staking APY 曾达到 **约 15–35% 区间**
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

# 一、Reactive Network 与传统 EVM 的根本区别

## 1️⃣ 传统 EVM：**请求驱动（Request-driven）**

在 **Ethereum** 及其他 EVM 链上：

**流程是：**

```
用户 → 发送交易 → 调用合约 → 合约执行
```

特点：

-   智能合约 **不会主动运行**
    
-   必须有人 **发送 transaction**
    
-   合约只是 **被动响应调用**
    

例如：

| 场景 | 触发方式 |
| --- | --- |
| Swap Token | 用户调用 DEX |
| Mint NFT | 用户调用 mint() |
| 清算 DeFi | 机器人调用清算函数 |

所以 EVM 世界里大量存在：

-   **bot**
    
-   **keeper**
    
-   **cron job**
    
-   **off-chain automation**
    

例如：

-   Chainlink Keepers
    
-   Gelato automation
    

本质是：

**链下服务在替合约做自动化。**

* * *

## 2️⃣ Reactive Network：**事件驱动（Event-driven）**

在 **Reactive Network** 中：

流程变成：

```
链上事件发生
      ↓
Reactive Contract监听事件
      ↓
自动执行逻辑
      ↓
触发新的交易
```

合约变成 **主动的自动化 agent**。

这就是：

**Reactive Smart Contract**

* * *

## 3️⃣ 关键区别总结

| 维度 | EVM | Reactive Network |
| --- | --- | --- |
| 执行模式 | 调用驱动 | 事件驱动 |
| 触发方式 | 用户 transaction | 链上事件 |
| 自动化 | 需要 bot / keeper | 链上原生自动化 |
| 逻辑位置 | 大部分在链下 | 更多在链上 |
| 合约角色 | 被动函数 | 主动 agent |

一句话：

**EVM 合约 = 被动 API**  
**Reactive 合约 = 自动机器人**
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->


今天是第一天！打算先来了解一下概念！

1.  学习如何使用 **Reactive Network** 构建跨链事件系统。
    
2.  理解如何在一个链上触发事件，并通过 **Reactive Smart Contracts (RSCs)** 在另一链上回调。
    
3.  学习如何利用合约继承、事件和跨链回调机制来创建链上交互。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
