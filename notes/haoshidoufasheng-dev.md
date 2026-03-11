---
timezone: UTC+8
---

# haoshidoufasheng-dev

**GitHub ID:** haoshidoufasheng-dev

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
1️⃣ 今日学习内容

今天主要学习了关于 Reactive Network / Web3 基础概念 的一些内容，比如：

什么是 Reactive（反应式网络）

Web3 中数据、智能合约之间如何自动触发

区块链生态的一些基本组成

2️⃣ 我的理解（用自己的话说）

我现在的理解是：

Reactive Network 有点像一个“自动响应系统”。

就像我们在现实生活中设置自动提醒一样，当链上发生某个事件时，系统会自动触发对应的操作，而不需要人工去执行。

3️⃣ 今天的收获

对 Web3 的运行逻辑有了更直观的理解

明白了很多项目其实是在做“自动化的链上应用”

发现这个领域需要慢慢积累知识，不能急
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

今日Web3学习打卡：Reactive Network

今天主要了解了Reactive Network的基本概念以及它在区块链生态中的作用。

Reactive Network是一种强调“事件驱动”的区块链架构。简单理解，就是当链上发生某个事件时，系统可以自动触发相应的操作或响应，而不需要人工干预。这种模式有点像现实生活中的自动化系统，例如当账户发生转账、智能合约状态变化等情况时，网络可以自动执行预设逻辑。

这种设计的意义在于提升区块链应用的自动化程度。传统区块链很多操作需要用户手动触发，而Reactive Network希望通过“监听事件 + 自动执行”的方式，让应用变得更加智能和高效。

在Web3生态中，这种架构未来可能会被用于很多场景，例如：

-   DeFi自动策略执行
    
-   自动化交易
    
-   链上数据响应系统
    
-   智能合约之间的协同操作
    

通过今天的学习，我对Web3基础架构又多了解了一点，也意识到区块链不仅仅是转账和代币，其实背后还有很多关于网络结构和系统设计的内容。

接下来我打算继续学习Reactive Network的实际应用场景，以及它和其他区块链网络之间的区别。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->


```
📒 Reactive 共学第1次学习笔记

今天主要学习了 Reactive Network 的基础概念以及跨链事件与回调合约的实现逻辑，对 Reactive 的核心机制有了初步理解。

一、Reactive Network 的核心理念
Reactive Network 的核心特点是“事件驱动”。在传统区块链中，智能合约通常需要用户主动调用才会执行，而 Reactive 的设计理念是让合约能够监听链上事件，并在事件发生后自动触发相应的操作。
简单来说就是：
链上事件发生 → Reactive 监听 → 自动执行逻辑。

这种机制可以实现更加自动化的链上应用，例如自动奖励、自动交易或安全监控等。

二、跨链事件与回调机制
本次作业的重点是理解跨链事件系统。跨链事件系统的基本流程如下：

1. 在一条链上触发事件
2. Reactive Contract 监听该事件
3. 在另一条链上调用回调函数执行逻辑

通过这种方式，可以实现不同区块链之间的自动化交互。

三、三个核心合约的作用

1. Origin Contract（事件源合约）
   该合约负责触发事件。当用户调用函数时，合约会 emit 一个事件，作为整个流程的起点。

2. Reactive Contract
   该合约负责监听 Origin Contract 产生的事件。当检测到指定事件后，会触发跨链回调逻辑。

3. Destination Contract（目标回调合约）
   该合约位于另一条链上，用于接收 Reactive Contract 的回调并执行具体逻辑，例如更新状态或执行某个函数。

整体流程可以总结为：
用户调用 Origin Contract → 触发事件 → Reactive Contract 监听事件 → 触发跨链回调 → Destination Contract 执行操作。

四、学习收获
通过今天的学习，我理解了 Reactive Network 与传统区块链交互方式的区别。Reactive 通过事件驱动的机制，可以让智能合约自动响应链上变化，从而实现更加自动化的 Web3 应用。这种设计在跨链交互、自动化执行等场景中具有很大的潜力。

后续我希望继续深入学习 Reactive Smart Contracts 的实现方式，并尝试理解更多跨链交互的应用场景。
```
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
