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
