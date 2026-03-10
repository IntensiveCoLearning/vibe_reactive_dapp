---
timezone: UTC+8
---

# @0xMrByte

**GitHub ID:** panrui1984

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->
今天完成练习任务一，

以下是对

[Origins & Destinations | Reactive Network](https://dev.reactive.network/origins-and-destinations)

的笔记

RCs 通过“源链监听 -> 智能合约逻辑处理 -> 代理合约/Hyperlane传输 -> 目标链验证并执行”的流程，实现了安全、灵活的跨链自动化操作

响应式合约 (RCs) 能够跨链工作：它可以监听一条链上的事件日志（Event Logs），并在另一条链上触发相应的操作（Actions）。

-   **源链 (Origin):** 也就是**事件源**。事件在这里发生，RCs 从这里读取事件日志。
    
-   **目标链 (Destination):** **状态改变**发生的地方。Reactive Network 会将回调交易 (Callback Transaction) 发送到这条链上。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->

# Reactive Network 概要笔记

**Reactive Network** 是一个去中心化的、基于事件驱动（Event-Driven）的智能合约执行平台,Web3 从传统的“被动触发”模式转变为“主动响应”模式。

## 三大角色

1.  **Origin Contract（源合约）**
    
    -   **位置**：部署在以太坊主网或任意 L1/L2 目标网络（如 Sepolia 测试网）。
        
    -   **作用**：业务的发生地。当状态改变时，负责**抛出事件（Emit Event）**。它是触发动作的“引线”。
        
2.  **Reactive Contract（响应式合约 / RSC）**
    
    -   **位置**：部署在专属的 **Reactive Network**（目前为 Kopli 测试网）。
        
    -   **作用**：网络的大脑和监听器。它订阅特定的 Origin 事件，一旦网络捕获到事件，该合约会被**自动唤醒**执行内部逻辑，并为下一步动作支付跨链中继的 Gas。
        
3.  **Destination Contract（目标合约）**
    
    -   **位置**：部署在任意目标网络（可以和 Origin 在同一条链，也可以在另一条链）。
        
    -   **作用**：动作的执行地。接收来自 Reactive 合约的指令，改变最终的链上状态。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
