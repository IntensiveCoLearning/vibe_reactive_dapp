---
timezone: UTC+8
---

# Eddie-534

**GitHub ID:** Eddie-534

**Telegram:** 

## Self-introduction

Let's vibe Reactive dApp！

## Notes

<!-- Content_START -->
# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->
26.3.9 今天对 reactive network 进行学习和理解：

1\. Reactive Network 与传统EVM的根本区别

传统的 EVM 只能被动执行交易：你必须先发起一笔交易，EVM 才会更新状态。

而 Reactive Network 可以主动监听和响应事件。它与 EVM 的根本区别在于：

Reactive Network 则是 “事件自触发”（当链上或跨链事件发生条件满足时，合约自动运行）。

在 Reactive Network 中，合约可以订阅和响应来自外部的链上事件。

2\. 核心架构：ReactVM + Reactive Network 环境

ReactVM（反应式虚拟机）

专门为运行 Reactive Smart Contracts 而设计的执行环境。

Reactive Network 环境

当监听到的条件满足时，这个环境会唤醒 ReactVM 并执行对应的反应式合约。

3\. 理解“事件驱动智能合约”与 Reactive Smart Contracts

事件驱动智能合约

· 这是一种新的编程范式。传统的合约是“函数调用驱动”的，你必须调用它的函数。事件驱动的合约则是“订阅驱动”的，它会一直保持待命状态，一旦监听到特定的事件（比如某个地址转了一笔账，或者某个价格突破了阈值），就会自动执行预定义的回调逻辑。

Reactive Smart Contracts（睿应式智能合约）

特性：

1\. 可订阅性：

2\. 自动执行：不需要用户支付 Gas 来触发，只要事件发生且条件满足，代码就自动运行。

3\. 跨链能力：一个反应式合约可以同时监听不同链上的事件，并在一条链上执行操作，或者在多条链上同时执行操作。

4\. Reactive Network 适合解决什么问题？

自动化跨链操作：

· 例如，你在以太坊主网上看到一笔巨额的 USDC 转账，自动在 Arbitrum 上执行一笔交易。这可以通过一个反应式合约来实现，而无需手动监控和操作。

即时清算与套利：

· DeFi 协议可以使用反应式合约，在某个链上的价格跌破某个水平或抵押率不足时，毫秒级自动触发清算或套利，降低延迟风险。

事件驱动的自动化策略：

· 比如，当某个 DAO 的投票提案通过时，自动执行资金拨付；或者当某个 NFT 以地板价成交时，自动触发购买。

跨链通知与同步：

· 当主网上的关键事件发生时，自动在其他链上更新状态或发送通知。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
