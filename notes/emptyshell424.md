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
# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->
### 1\. 核心概念：控制反转 (Inversion of Control, IoC)

这是 Reactive 合约与传统合约（如 Ethereum 上的合约）最本质的区别：

-   **传统合约（被动型）：** 必须由外部账户（EOA，如用户钱包或机器人）发起交易并支付 Gas，合约才会执行。
    
-   **Reactive 合约（主动型）：** 采用 **控制反转** 模式。合约不再等待用户调用，而是“订阅”特定区块链上的事件。一旦监测到符合条件的事件，合约会自动触发执行逻辑。
    

### 2\. 响应式工作流程

Reactive 合约的运行遵循“订阅 - 监测 - 反应”的循环：

1.  **配置订阅：** 开发者在合约中指定要监测的**链 ID**、**目标合约地址**以及特定的**事件签名 (Topic 0)**。
    
2.  **事件捕捉：** Reactive Network 持续扫描所选链的日志。
    
3.  **自动执行：** 当匹配的事件发生时，网络会自动在 Reactive 层触发合约逻辑。
    
4.  **跨链反馈：** 合约执行后，可以产生状态变更，或者通过网络的基础设施向其他目标链发送回调消息或触发新交易。
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->

## 1\. Reactive Contract 的组成结构

传统的智能合约是**被动**的（只有外部账户调用它，它才会执行）；反应式合约是**主动**的。其核心结构通常包含三个部分：

-   **监测逻辑 (Detection Logic):** 定义合约关心哪些链上或链下事件（如：当某个池子的价格低于 $X$）。
    
-   **触发条件 (Predicate/Condition):** 一组逻辑判断。只有当监测到的数据满足特定条件时，合约才会激活。
    
-   **动作逻辑 (Action Logic):** 一旦满足条件，合约需要执行的具体事务（如：执行清算、跨链转账）。
    

* * *

## 2\. Subscribe / Trigger / Callback 模型

这是 Reactive Contract 实现异步交互的标准工作流。

-   **Subscribe (订阅):** 合约向底层索引器或中间件注册它感兴趣的事件源（Source）。这就像你在 B 站关注了一个 UP 主并开启了小铃铛。
    
-   **Trigger (触发):** 当源链上发生了匹配的事件（Event）时，底层框架（如 Reactive Network）会捕获这个信号。
    
-   **Callback (回调):** 框架将触发信号和相关数据推送到你的 Reactive Contract 中预定义的函数。合约根据这些数据执行后续逻辑。
    

**本质逻辑：** 它将“状态改变的感知”与“逻辑的执行”解耦了。

* * *

## 3\. ReactVM 执行逻辑

ReactVM（Reactive Virtual Machine）与 EVM 的最大区别在于**并行处理**和**事件敏感性**。

-   **事件驱动流水线:** 不同于 EVM 顺序处理每个区块内的交易，ReactVM 维护着一个事件响应队列。
    
-   **状态隔离:** ReactVM 运行在独立的响应层。它不直接修改源链的状态，而是产生一个“意图（Intent）”，通过发送新的交易来改变目标链状态。
    
-   **确定性响应:** ReactVM 必须保证：对于给定的事件输入，产生的回调动作是确定性的，从而确保分布式节点能够达成共识。
    

* * *

## 4\. 跨链与自动化机制

这是 Reactive Contract 最具杀手锏的应用场景。

### 自动化 (Automation)

传统的自动化需要靠中心化的 Keepers（如 Chainlink Keepers）。

-   **Reactive 做法:** 合约自启动。它监控时间或状态，达到阈值后自动在 ReactVM 中运行逻辑并发出交易。**去中心化程度更高。**
    

### 跨链 (Cross-chain)

目前的跨链多是桥接（Bridge），而 Reactive Contract 实现的是**跨链互操作性**。

-   **监听 A 链:** 订阅 A 链的合约日志。
    
-   **ReactVM 决策:** 在中间层处理逻辑（例如计算汇率或检查库存）。
    
-   **驱动 B 链:** 自动触发 B 链上的合约调用。
    
-   **闭环:** 整个过程不需要前端介入，实现了“A 链发生 A'，B 链自动执行 B'”的无缝衔接。
<!-- DAILY_CHECKIN_2026-03-12_END -->

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
