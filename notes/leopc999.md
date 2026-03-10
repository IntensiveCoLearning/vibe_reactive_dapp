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
