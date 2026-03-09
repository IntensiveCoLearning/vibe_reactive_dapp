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
