---
timezone: UTC+8
---

# Leot (leopc999)

**GitHub ID:** leopc999

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
### **实时监控问题解答**

**监控原理**：市场波动与Reactive实时监控无关，其监控每条链的每个区块，并对合约订阅的事件做出反应。

**特殊情况说明**：在极端波动如黑客攻击或调试时，一个区块内的反应可能不足，但总体仍有时间监控数据，需确保交易及时并支付足够Gas费用。

### **应用场景讨论**

**跨链操作痛点**：Reactive可解决跨链操作繁琐、用户体验差及安全性隐患问题，目前跨链操作低效，合约间缺乏通讯机制，跨链桥存在中心化问题。

**潜在解决方案**：Reactive可弥补通讯机制空缺，避免因不同链合约缺乏沟通导致的清算不及时问题。

**全链上方案补充**：全链上跨链方案在链下较脆弱且无法自动化，跨链桥和预言机存在中心化问题。

### **经济生态问题探讨**

**币价影响**：有同学提出Reactive部署节点需质押代币，币价下跌会降低作恶成本，可能导致应用层出现问题。

**POS机制疑问**：大家讨论了POS机制能否保障安全，认为以太坊因市值高作恶成本高，但Reactive代币市值低时存在作恶可能。

**与预言机对比**：该同学认为Reactive与Chainlink机制类似，都需质押代币成为节点，且都面临共识安全问题。

**中心化解决方案**：提出可通过中心化控制部分节点解决问题，但会导致中心化程度过高，影响使用意愿。
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->

1\. **Transaction Rollback and Reorg Handling**

**Network Failure** ：When a node encounters a network failure, transactions will not be rolled back. The state of non - failed nodes is considered correct.

**Chain Reorg** ：If a reorg occurs on the origin chain after a callback is sent, it does not affect the execution of the reactive contract. Handling such scenarios should be done at the application (smart contract) level. For example, waiting for several blocks on the origin chain to ensure no reorgs before sending a callback.

2\. **Low - Latency Achievement**

**Latency Factors** ：The main latency from event trigger to reactive response comes from the block time of the React network (average 7 seconds) and the destination chain. For example, for Ethereum, the average block time is 10 seconds, and the reaction time is about half of the block time on average.

**Application Scenarios and Risks**

**Not Recommended for High-Frequency Use Cases** ：Reactive contracts are not recommended for time - sensitive, high - frequency use cases due to the added delay from the separate chain (React network) for decentralization. They are more suitable for use cases where decentralization is more important than high speed.

4\. **Demo Introduction**

**Starting Points** ：You can go to the official website, then either read the docs for more information on working with reactive contracts or go to the GitHub repository (reactive smart contract demos). Start with the basic demo in the src demos folder and follow the steps in the readme to deploy contracts.

5\. **Documentation Information**

**Availability** ：Translation versions of relevant information can be found in the notion document in the learning resource library, but currently only the first chapter is available.

* * *

### 中文解释：

1.  网络回退与链重组处理
    

网络故障：当某个节点遇到网络故障时，交易不会被回滚。未发生故障的节点状态被视为正确。

链重组：如果在发送回调之后，源链发生链重组，这不会影响睿应式合约的执行。此类情形的处理应在应用（智能合约）层面完成。例如，可在源链上等待若干个区块以确保没有重组再发送回调。

2.  低延迟实现
    

延迟因素：从事件触发到睿应响应的主要延迟来自睿应层的出块时间（平均约 7 秒）以及目标链的出块时间。例如，对于以太坊，平均出块时间约为 10 秒，平均反应时间约为区块时间的一半。

3.  应用场景与风险
    

不建议用于高频场景：由于为实现去中心化而引入了独立链（睿应层），会增加延迟，因此睿应式合约不建议用于对时间高度敏感的高频用例。它们更适合那些去中心化比极高速度更重要的场景。

4.  演示说明
    

入门：可访问官方网站，阅读文档以获取关于使用睿应式合约的更多信息，或访问 GitHub 仓库（睿应式合约示例）。从 `src` 下的 `demos` 文件夹中的基础示例开始，按照 README 中的步骤部署合约。

5.  文档信息
    

可用性：相关信息的中文翻译可在学习资源库的 Notion 文档中找到，但目前仅开放第一章。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->


# **Basic Reactive Demo：理解“监听-反应”闭环**

这个 Demo 是 Reactive 的 "Hello World"。流程很简单：在 Sepolia 上转账 -> Reactive 监听到 -> 自动通知 Sepolia 上的回调合约。

**部署流程速览**

1.  **Origin (Sepolia)**: 部署 BasicDemoL1Contract。这是被监听的对象。
    
2.  **Destination (Sepolia)**: 部署 BasicDemoL1Callback。这是接收通知的对象。
    
3.  **Reactive (Reactive Lasna)**: 部署监听合约，连接 Origin 和 Destination。
    
4.  **触发测试**: 向 Origin 转账 > 0.001 ETH。
    

**具体执行命令**

**Step 1**：部署源链合约 (Origin) 负责接收转账并发出事件。

```bash
forge create --broadcast --rpc-url $ORIGIN_RPC --private-key $ORIGIN_PRIVATE_KEY src/demos/basic/BasicDemoL1Contract.sol:BasicDemoL1Contract
```

**Deployed to:** **0x1fe40FbA975c1B0f8069737dB32B4b74C6dD01F8**

**Step 2**：部署目标链合约 (Destination) 负责接收回调。这里预充值 0.001 ETH 是为了模拟支付 Relayer 的 Gas 费。

```bash
forge create --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY src/demos/basic/BasicDemoL1Callback.sol:BasicDemoL1Callback --value 0.001ether --constructor-args $DESTINATION_CALLBACK_PROXY_ADDR
```

**Deployed to:** **0xf2Dd031f9a732a3f6e3097bB626eAA9627cDE4cc**

**Step 3**：部署反应式合约 (Reactive) 连接 Origin 和 Destination。注意中间的长 Hash 是 Received 事件的 Topic 0。

```bash
forge create --rpc-url $REACTIVE_RPC --private-key $PRIVATE_KEY src/demos/basic/BasicDemoReactiveContract.sol:BasicDemoReactiveContract --value 0.001ether --constructor-args $SYSTEM_CONTRACT_ADDR $ORIGIN_CHAIN_ID $DESTINATION_CHAIN_ID $ORIGIN_ADDR 0x8cabf31d2b1b11ba52dbb302817a3c9c83e4b2a5194d35121ab1354d69f6a4cb $CALLBACK_ADDR
```

**Step 4**：触发与验证 向 Origin 合约转账 **0.0015 ETH**（大于代码要求的 0.001 ETH）。

```bash
cast send $ORIGIN_ADDR --rpc-url $ORIGIN_RPC --private-key $ORIGIN_PRIVATE_KEY --value 0.0015ether
```

**Trigger Tx Hash:** _0xe002ce016018c9387aa5444634eaded33801d30f0175b994cd97d0ec40b14567_ **结果**: 几分钟后，在 Sepolia Etherscan 查看 Callback 合约，成功收到 CallbackReceived 事件。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->



# **我所理解的睿应层（Reactive Network）**

对于刚入门的初级技术开发来说，可以将睿应层理解为区块链世界的**“自动化触发器”或“智能中枢”**。

![图像](https://pbs.twimg.com/media/HBDarTpakAENKqM?format=jpg&name=medium)

## **核心概念：从“被动等待”到“主动响应”**

要理解睿应层，首先要明白它解决了什么问题。

-   **传统智能合约（旧模式）：** 就像一台**自动售货机**。它很被动，必须等待用户（你）去投币、按按钮，它才会动一下。如果没有人操作，它就永远静止。
    
-   **睿应合约（Reactive Smart Contracts，新模式）：** 就像一套**智能家居系统**。它不需要你每次都去按开关。它会自己“观察”环境——比如监测到“天黑了”（事件），它就会自动“开灯”（执行操作）。
    

在文档中，这被称为**“控制反转”（Inversion of Control）。**意思就是：**不再由**用户**手动驱动每一步，而是由链上的**数据流（事件）来驱动合约自动运行。

## **睿应层的运行逻辑（三步）**

整个睿应层的工作流程可以简化为“听”、“想”、“做”三个环节：

**第一步：听（订阅与源链）**

-   **源链 (Origins)：** 事情发生的地点（比如以太坊、Polygon 等区块链）。
    
-   **订阅 (Subscriptions)：** 睿应式合约会提前在这些链上通过“订阅”功能布置“耳目”。它会告诉网络：如果是关于“Uniswap 价格变动”的消息，记得第一时间通知我。
    

**第二步：想（ReactVM 与处理）**

-   **ReactVM：** 这是睿应层的大脑和处理中心。
    
-   **低成本处理：** 当源链上发生了订阅的事件（比如有人买入了代币），这个信息会被传送到睿应层。睿应层会用一种非常快且便宜的方式（并行化 EVM）来计算该怎么做。这比直接在昂贵的主链上计算要划算得多。
    

**第三步：做（回调与目标链）**

-   **目标链 (Destinations)：** 需要产生结果的地方。
    
-   **回调与执行：** 计算完成后，睿应式合约会向目标链发送指令（Callback），执行相应的操作。
    

**例子：** 比如在源链监测到币价跌到了某个位置，睿应层计算后，自动向目标链发送指令：“卖出这个币”（比如官方文档提到的 Uniswap V2 止损单的用例）。

## **为什么需要它？（核心价值）**

对于开发者来说，睿应层最大的意义在于**“跨链自动化”**：

1.  **省心（自动化）：** 开发者可以写出能自己根据情况做决定的程序，不需要用户时刻盯着屏幕操作或者额外部署监控机器人程序。
    
2.  **省钱（经济模型）：** 它把复杂的计算搬到了专门优化的睿应层上做，减少了在昂贵主链上的花费。
    
3.  **打通孤岛：** 它可以监听一条链的事，去影响另一条链，把不同的区块链连接了起来。
    

## **总结**

**睿应层（Reactive Network）既能听懂各条链上的“风吹草动”（事件），又能通过低成本计算迅速做出反应，最后指挥其他链进行操作的“自动化指挥官”。**
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
