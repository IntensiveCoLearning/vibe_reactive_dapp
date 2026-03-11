---
timezone: UTC+8
---

# shell

**GitHub ID:** emptyshell424

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
## 1\. 根本区别：被动调用 vs 主动反应

在传统的 **EVM** 架构中，智能合约是“死的”。除非用户（或外部脚本）发送一笔交易来调用它，否则它永远不会主动执行任务。

-   **传统 EVM (Passive)：** 像一个自动售货机。你不投币（发交易）并按下按钮，它永远不会动。
    
-   **Reactive Network (Active)：** 像一个带传感器的恒温器。它不断“观察”外部环境，一旦满足条件（如气温低于 20°C），它无需人为干预，立即自动调节。
    

* * *

## 2\. 核心架构：ReactVM + 睿应层环境

睿应层并不是要取代现有的区块链（如 Ethereum 或 BSC），它是作为\*\*元层（Meta-layer）\*\*存在。

### ReactVM (Reactive Virtual Machine)

ReactVM 是一种专门为**处理事件流**而优化的环境。

-   **非独占执行：** 它不仅处理自己的状态，还能“跨链”监听。
    
-   **逻辑分离：** 你可以将业务逻辑留在 EVM 链上，而将“监控与触发逻辑”部署在 ReactVM 中。
    

### 运行环境

睿应层通过 **Relayers（中继器）** 实时获取上游链（源链）的日志和状态。当检测到特定事件时，ReactVM 会根据预设逻辑产生一个“睿应”，并将其推送到下游链（目标链）。

* * *

## 3\. 什么是“睿应式智能合约”（Reactive Smart Contracts）？

如果说普通合约是 `Function call`，那么睿应式合约就是 `Event listener + Callback`。

**睿应式智能合约（RSC）** 的三个核心特征：

1.  **订阅性 (Subscription)：** 它定义了要监听哪些链、哪些合约、哪些 Topic（日志）。
    
2.  **异步性 (Asynchrony)：** 事件发生到合约执行是解耦的，不阻塞源链。
    
3.  **跨链原子性逻辑：** 虽然物理上不在同一条链，但在逻辑上它能把 A 链的输出作为 B 链的输入。
    

> **逻辑挑战：** 很多人会把这和“预言机（Oracle）”或“链下脚本（Keepers）”搞混。
> 
> -   **区别在于：** 睿应层是在**共识层**处理这些事件，而不是靠一个中心化的机器人去跑脚本。它实现了“链上原生的自动化”。
>     

* * *

## 4\. 睿应层（Reactive Network）适合解决什么问题？

如果你还在靠后端服务器去轮询（Polling）区块链状态，那这就是睿应层发挥作用的地方。

| 场景 | 传统方案的痛点 | 睿应层的解决方式 | | 全链收益聚合 | 需要用户手动在 A 链提取，再跨桥到 B 链。 | 自动监控 A 链收益，达标后自动触发跨链复投。 | | 动态 NFT (dNFT) | 需要中心化服务器监控链下数据并修改 metadata。 | 直接监听链上成就/事件，自动触发 NFT 状态升级。 | | 复杂的止损逻辑 | 依赖 Keeper 节点，存在延迟或失效风险。 | 直接在睿应层部署“价格触发器”，一旦满足条件立即执行清算。 | | 跨链治理 | 多链提案统计极其繁琐且不透明。 | 自动汇聚各链投票事件，汇总结果并自动在主链执行提案。 |
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->


## 合约设计逻辑

**Origin (**`BasicDemoL1Contract.sol`**)** — 部署在 Sepolia

-   `receive()` 接收 ETH，emit `Received(from, sender, value, counter)` 然后退款
    
-   `value` 是 indexed 的 topic\_3，RSC 用它来做条件过滤
    

**Destination (**`BasicDemoL1Callback.sol`**)** — 部署在 Sepolia

-   构造时传入 callback proxy 地址，做 `msg.sender` 校验，防止任意人调用
    
-   部署时要附带 0.01 ETH 作为 gas 储备
    

**Reactive (**`BasicDemoReactiveContract.sol`**)** — 部署在 Reactive Lasna

-   构造函数调用 `service.subscribe()`；如果 revert（在 ReactVM 中），则 `vm = true`
    
-   `react()` 只在 ReactVM 环境执行，检查 `topic_3 >= 0.01 ETH` 后 emit `Callback`
    
-   `address(0)` 是占位符，callback proxy 执行时会替换为实际 RSC 地址
<!-- DAILY_CHECKIN_2026-03-10_END -->
<!-- Content_END -->
