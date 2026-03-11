---
timezone: UTC+8
---

# David Zhou

**GitHub ID:** ShihaoZhou-NEU

**Telegram:**

## Self-introduction

Reactive 探索者

## Notes

<!-- Content_START -->
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
# **Lesson 2: How Events and Callbacks Work**

本课重点讲解事件和回调在智能合约中的作用。通过学习如何发出、处理和监听事件，开发者可以创建能够实时响应区块链变化的动态去中心化应用（dApp）。我们还将探讨响应式合约如何使用 `react()` 方法处理事件，并通过回调发起跨链交易，从而增强响应式网络的功能。

## **EVM 事件如何运作**

当智能合约发出事件时，事件数据会存储在交易日志中。这些日志附加到区块链的区块上，但不会直接影响区块链的状态。相反，它们提供了一种基于事件参数记录和检索信息的方法。

开发者在智能合约中使用 `event` 关键字定义事件，后跟事件名称和要记录的信息的数据类型。要发出事件，智能合约使用 `emit` 关键字，后跟事件名称和要记录的数据。

外部应用程序（例如去中心化应用或后端服务）可以监听这些事件。通过指定事件签名以及（可选的）过滤参数，这些应用程序可以在事件发生时订阅实时更新。这种机制对于创建响应迅速且交互性强的区块链应用程序至关重要。

```
 pragma solidity >=0.8.0;
 ​
 import './IPayer.sol';
 ​
 interface IReactive is IPayer {
     struct LogRecord { // 定义了一种结构化数据类型 LogRecord ，用于包含有关事件日志的详细信息：
         uint256 chain_id; // 事件发生的区块链的 ID
         address _contract; // 发出事件的合约地址
         uint256 topic_0; // 日志的索引主题
         uint256 topic_1;
         uint256 topic_2;
         uint256 topic_3;
         bytes data; // 事件日志中未建立索引的数据
         uint256 block_number; // 事件发生的区块编号
         uint256 op_code; // Potentially denotes an operation code.
         uint256 block_hash; // 用于追踪事件来源和上下文的附加标识符。
         uint256 tx_hash; // 用于追踪事件来源和上下文的附加标识符。
         uint256 log_index; // 用于追踪事件来源和上下文的附加标识符。
     }
 ​
     event Callback( // 回调事件 ：用于通知订阅者特定事件发生的事件：
         uint256 indexed chain_id, // 事件的区块链 ID
         address indexed _contract, // 发出合约的地址
         uint64 indexed gas_limit, // 回调函数分配的最大 gas 量
         bytes payload // 随回调一起返回的编码数据
     );
     
     function react(LogRecord calldata log) external; // 处理传入事件通知的关键函数
 }
```

响应式网络持续监控事件日志，并将其与响应式合约中定义的订阅条件进行匹配。当检测到符合条件的事件时，网络会触发 `react()` 方法，并传递相关详细信息。

响应式合约可以访问所有标准的 EVM 功能。但是，它们运行在私有的 ReactVM 环境中，这限制了它们只能与同一部署者部署的合约进行交互。这种隔离机制确保响应式合约在处理来自响应式网络的事件时，能够维持一个受控且安全的环境。

## **Callbacks to Destination Chains**

响应式合约可以通过以特定格式发出日志记录，在目标链上发起交易。这些记录会被响应式网络接收，然后响应式网络会在相关的链上执行所需的交易。

### **Emitting Callback Events 发出回调事件**

event Callback( // 回调事件 ：用于通知订阅者特定事件发生的事件： <- 这个事件被 emit

### **Processing the Callback 处理回调**

当 `Callback` 事件发出时，响应式网络会检测到该事件并处理 `payload` ，该有效载荷以特定格式编码交易详情。然后，响应式网络使用提供的 `chain_id` 和 `gas_limit` 将交易提交到目标链上指定的合约。

### **Important Note on Authorization**

For security and authorization purposes, the Reactive Network automatically replaces the first 160 bits of the call arguments within the `payload` with the RVM ID (equivalent to the ReactVM address) of the calling reactive contract. This RVM ID is identical to the contract deployer's address. As a result, the first argument in your callback will always be the ReactVM address (of type `address`), regardless of the variable name you use in your Solidity code.

### **Encoding and Emitting the Callback Event**

要在目标链上发起操作，您可以将交易详情编码到 `payload` 中并发出 `Callback` 事件。例如，在 Uniswap 止损单演示中，此过程用于通过目标链合约触发代币销售：

```
 bytes memory payload = abi.encodeWithSignature(
    "stop(address,address,address,bool,uint256,uint256)",
    address(0),  // The ReactVM address
    pair,        // The Uniswap pair address involved in the transaction
    client,      // The address of the client initiating the stop order
    token0,      // The address of the first token in the pair
    coefficient, // A coefficient determining the sale price
    threshold    // The price threshold at which the sale should occur
 );
 emit Callback(chain_id, stop_order, CALLBACK_GAS_LIMIT, payload);
```
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

# 学习计划

1.  学习：[https://dev.reactive.network/education/module-1/reactive-contracts](https://dev.reactive.network/education/module-1/reactive-contracts)
    
2.  看一下 Workshop 视频
    

# **Lesson 1: Reactive Contracts**

## **RCs 与传统合约有何不同**

一个被动一个主动，RCs 接收到链上事件的信号，自主执行预定义的区块链操作

## **Inversion of Control 控制反转**

RCs 的关键概念是控制反转（IoC）。

传统 -> EOA 用户或机器人发起。

RC -> 根据预定义事件的发生自主决定何时执行。

如果没有响应式合约，需要设置一个独立的实体（例如机器人）来监控区块链，并使用现有的、很可能是中心化的数据解决方案。该机器人将持有管理资金的私钥，并通过其 EOA 地址在 EVM 链上发起交易。虽然此类系统确实有用，但对于某些用例来说可能并非最佳选择，而对于另一些用例则完全不适用。

控制反转使我们能够避免托管模拟人类签署交易的额外实体。如果您预先定义了一个场景，概述了链上事件发生后的交易序列，那么您应该能够以完全去中心化的方式运行此逻辑，因为您的输入和输出都保留在区块链上。响应式网络赋予智能合约一项它们从一开始就缺失的特性——无需人（或机器人）签署交易，仅基于其他链上事件即可自动执行。

## **What Happens Inside a Reactive Contract 反应式合约内部发生了什么**

1.  指定链，合约，事件。
    
2.  持续监控，当检测到指定事件的时候启动执行（这些事件可以包括简单的货币或代币转账、去中心化交易所 (DEX) 兑换、贷款、闪电贷、投票、巨鲸操作或任何其他智能合约活动）。
    
    1.  一旦检测到感兴趣的事件，响应式网络会自动执行您在响应式合约中实现的逻辑。这可能涉及基于事件数据进行计算。响应式合约是有状态的，这意味着它们拥有一个状态，用于存储和更新值。您可以随着时间的推移在状态中积累数据，然后在历史数据和新的区块链事件的组合满足指定条件时执行操作。
        
    2.  事件发生后，RC 会更新其状态，保持最新状态，并可在 EVM 区块链上发起交易。整个过程在响应式网络内以无需信任的方式运行，确保自动、快速且可靠地执行。
        

## **用例**

### **从多个预言机收集数据**

为了使响应能力能够应对更广泛的事件，包括链下事件，RC 会与预言机集成。预言机是第三方服务，可将可信的外部数据输入区块链。

RC 可以监控来自不同智能合约、兼容 EVM 的区块链的数据，它可以整合来自多个预言机的数据，从而获得更精确、更去中心化的信息。在这种情况下，RC 监控的事件是相应预言机更新后的事件。RC 内部的计算将涉及整合来自不同预言机的数据（例如，取平均值）。最终结果可能是一个基于篮球比赛结果的无需信任的支付。

### **止损单**

区块链上另一个可靠的数据源是交易池，例如 Uniswap 交易池。它甚至比预言机更可靠，因为它完全由链上数据构成，不依赖第三方。

在这种架构下，一个响应式合约会监控指定 UniSwap 资金池中的互换交易，计算流动性和汇率。当汇率达到预设价格时，响应式合约会执行互换交易，从而在现有去中心化交易所（DEX）的基础上实现无需信任的止损单。

### **DEX 套利**

响应式合约将监控多个不同的资金池，寻找价格差异并从中获利。单链和跨链两种方式都可行。单链方式可以使用闪电贷；跨链方式则需要多条链的流动性，但可以获得更多的套利机会。

这种解决方案的优势在于它的去中心化特性，这与传统的机器人方法截然不同。

### **Pools Rebalancing 资金池重新平衡**

从头就设计响应式的系统，并利用响应式网络的功能来构建以太坊智能合约。

这种方法使我们能够创建流动性池，并在多个交易所之间自动进行再平衡。RC 将监控所有相关链上的流动性，并根据需要通过增加或减少资金来重新平衡它们。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->



# **Overview**

> 事件发生在一条链上，而驱动这些事件的逻辑却存在于另一条链上。标准合约无法弥合这种鸿沟。

单一链上自动化层取代

1.  跨链延伸，Cross-Chain Reach：订阅任何受支持的 EVM 链上的事件。通过一个合约做出响应
    
2.  Auto-Execution 自动执行：没有调用者，没有持有者，被动合约在满足条件时触发
    
3.  Composable Logic 可组合逻辑：将链式响应式合约组合在一起，构建多步骤、事件驱动的工作流。
    
4.  Built to Scale 按规模建造：事件处理在独立的 ReactVM 实例中运行，无需复杂的协调开销。
    
5.  Direct Log Access 直接日志访问：响应式合约直接从日志订阅中读取区块链事件。无需预言机，也无需中间件。
    

[https://blog.reactive.network/reactive-x-rogues-studio-bringing-seamless-on-chain-incentives-to-web3-gaming/](https://blog.reactive.network/reactive-x-rogues-studio-bringing-seamless-on-chain-incentives-to-web3-gaming/)

可以作为一个 idea，我们的 EIP Playground 也可以参考这个来做一些有趣的动态 NFT

## **第一步：Reactive Basics**

1.  Origins and Destinations：了解始发地和目的地链及其回调代理地址（Callback Proxy addresses）。
    
2.  Hyperlane：传输跨链回调
    
3.  Reactive Contracts：响应式合约如何订阅事件并触发操作
    
4.  ReactVM：响应式执行的工作原理
    
5.  经济性：了解回调支付和 Reactive 的经济性
    

首先，智能合约无法在没有外部指令（无论是来自用户还是像交易机器人这样的自动化脚本）的情况下自主发起操作或响应区块链事件。这就需要持有私钥并引入中心化的控制点。响应式合约（RC）的出现正是为了解决这一限制。RC 旨在自主响应以太坊虚拟机（EVM）中的事件，并触发区块链生态系统中的后续操作。这种能力使得复杂的逻辑得以实现，它可以从多个链获取信息，并在无需中央监管的情况下跨平台执行更改或交易。

听起来很有用，虽然 EVM 已经是图灵完备，但是只是在合约内部，让合约与合约之间也上一个条件判断，但是这跟我单独写一个合约把这部分逻辑放进去差不多吧，或者说这个响应式更方便的是跨链传递消息

Gemini：

1.  **突破传统合约的“被动”限制（告别链下机器人）** EVM 确实是图灵完备的，但传统智能合约有一个致命弱点：它们是被动的，只有在用户或外部脚本（如自动交易机器人）发起交易时才会执行。如果你只是单独写一个包含条件判断的传统合约，它仍然无法自主发起操作或对区块链事件做出反应，必须依赖外部的唤醒。为了让普通合约实现自动化，开发者通常需要依赖链下计算、运行脚本机器人并保管私钥，这引入了中心化控制的风险。 相反，**RSCs 是主动的、事件驱动的**。它们能够不间断地持续监控 EVM 链上的事件日志，并在满足特定条件时自动决定发送跨链回调交易并触发后续逻辑，完全不需要等待用户或机器人来触发。例如，在 Uniswap 的止损/止盈应用中，RSC 可以自主监听交易对的汇率变动事件（Sync events），并在达到阈值时直接自动触发代币交换，无需用户盯盘或运行机器人。
    
2.  **赋予合约“时间观念”（内置定时任务 CRON）** 单独写的传统合约本身是没有主动的“时间观念”的，只能等待被调用。而 Reactive 网络引入了内置的定时任务（CRON）机制。系统的智能合约会在固定的区块间隔发出 cron 事件，相当于给智能合约提供了一个“内置时钟”。这意味着合约可以自行按照时间表定期执行操作，比如定期更新数据、分发奖励，或者在 Aave 上定期自动检查用户的借贷健康度以防止爆仓（自动存入抵押品或偿还债务），这一切都不需要任何外部的触发。
    
3.  **不仅是传递消息，而是真正的“跨链自动化”** 正如你所说，跨链是 RSC 的极大优势，但它的作用远不止“传递消息”，而是构建**无缝的条件式跨链工作流**。 例如在 Web3 游戏《World of Rogues》中，RSC 被用来构建自动化的玩家奖励系统：系统在一条低成本链上记录玩家的连续活跃度，然后自动在玩家首选的另一条链上为他们铸造 NFT 奖励。玩家无需繁琐地去进行手动领取（Claim）的仪式，NFT 会自动发送到他们的钱包中，降低了摩擦和 gas 费用开销，实现了类似 Web2 的丝滑体验。
    

-   **去中心化：** RC 在区块链上独立运行，消除了中心化的控制点，并通过降低操纵或故障的风险来提高安全性。
    
-   **自动化：** RC 会根据链上事件自动执行智能合约逻辑，从而减少人工干预的需要，并实现高效的实时响应。
    
-   **跨链互操作性：** RC 可以与多个区块链进行交互，从而实现复杂的跨链交互，增强多功能性并弥合网络之间的差距。
    
-   **增强效率和功能：** 通过对实时数据做出反应，RC 提高了智能合约的效率，支持复杂金融工具、动态 NFT 和创新 DeFi 应用等高级功能。
    
-   **DeFi 及其他领域的创新：** RC 为 DeFi 和其他区块链应用带来了新的可能性，例如自动化交易和动态治理，从而创建了一个更具响应性和互联性的区块链生态系统。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
