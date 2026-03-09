---
timezone: UTC+8
---

# Thomas

**GitHub ID:** Thomas-YHS

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->
终于又开始了新的残酷共学，这次一定要坚持下来，这次想看看能不能做一个简单的链上套利机器人。  
今天看了看文档，把demo的代码拉下来，基础的配置调试了一下，明天部署到测试链上，下面是今天的笔记：

##   
Reactive Network

Reactive 的核心可以引用文档中的一句话来描述：

> Reactive Contracts monitor event logs across EVM chains and execute Solidity logic automatically when conditions are met. Instead of waiting for users or bots to trigger transactions, RCs run continuously and decide when to send cross-chain callback transactions — essentially providing **if-this-then-that automation for smart contracts**.

Reactive实现的最重要的一点是可以在链上持续监控并且发送callback到指定链，传统区块链必须有人发起交易，而Reactive Contracts **持续运行并监听事件**，当条件满足时会自动执行操作。

* * *

Reactive Network 的执行模型本质上是：

**监听 Origin 上的事件 → 在 Destination 上发送 callback → 通过 Callback Proxy 保证安全与可验证性**

Event on Origin → Reactive listens → Callback sent to Destination → Destination verifies through Callback Proxy

### Reactive 可以实现的场景

1.  自动止盈/止损
    
2.  清算保护
    
3.  自动切换投资组合
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
