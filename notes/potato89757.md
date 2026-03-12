---
timezone: UTC+8
---

# potato89757

**GitHub ID:** potato89757

**Telegram:** 

## Self-introduction

Let's vibe Reactive dApp！

## Notes

<!-- Content_START -->
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
# **今日笔记**

在 Reactive Network 中，一切都基于 **“事件驱动”** 的理念：合约可以自己监听链上事件，并在条件满足时自动执行逻辑，而无需外部 Bot 或用户触发。我把 Reactive Contract 看作两部分组成：**订阅逻辑**（设置要监听的链、合约和事件）和 **触发回调逻辑**（事件发生后在 ReactVM 中执行 `react()` 函数，可能产生跨链回调）。这个模型可以概括为：**订阅 → 事件触发 → react() 执行 → 发射回调**。下面的学习笔记，我先解释关键概念，然后通过组件列表、时序图、示例代码等，详细讲解 Reactive Contract 的结构和“Subscribe/Trigger/Callback”模型，并比较与 Chainlink/Gelato 的区别，最后给出几个实战 Demo 想法。

**引用:** Reactive Contracts 是 _事件驱动的智能合约_，监控跨链的事件日志，在订阅事件发生时自动执行逻辑，并可触发跨链回调。这是典型的 “事件驱动 on-chain 自动化” 模型，不依赖外部 Bot。

## **关键概念定义**

-   **Reactive Contract (RC)**：一种特殊的智能合约，部署在 Reactive Network 上，并在其私有的 ReactVM 环境中执行。RC 可以在一个或多个原始链（Origin）上 **订阅事件**，当事件发生时，ReactVM 会自动调用该合约的 `react()` 方法执行逻辑。RC 由两份相同的字节码组成，一份在公共链（Reactive Network, RNK）上处理订阅和普通交互，另一份在专用的 ReactVM 上处理事件触发逻辑。
    
-   **订阅 (Subscribe)**：RC 通常在构造函数中通过系统合约调用 `subscribe()` 方法，指定需要监听的链ID、合约地址和事件主题（Topic）。订阅告诉 Reactive Network：当满足过滤条件的事件发生时，要将该事件传递给对应的 ReactVM。例如：`service.subscribe(chainId, tokenContract, topic0, REACTIVE_IGNORE, REACTIVE_IGNORE, REACTIVE_IGNORE)` 会订阅某链上指定合约的所有事件。
    
-   **触发 (Trigger/React)**：当被订阅的事件（Event Log）在原始链上发生后，Reactive Network 会把这个事件记录传入合约对应的 ReactVM 环境，并调用合约的 `react(LogRecord log)` 方法。`LogRecord` 结构体里包含了事件的链ID、合约地址、Topics、数据、区块号等信息。在 `react()` 函数中，合约可以读取 `log.data` 或 `log.topic_x` 等字段，进行条件判断，并决定是否要执行回调交易。
    
-   **回调 (Callback)**：如果 `react()` 里判断条件满足，需要在目标链执行操作，合约就通过 `emit Callback(destinationChainId, targetContract, gasLimit, payload)` 发射一个特殊事件。Reactive Network 监听到这个事件后，会在目标链发起一笔交易，将 `payload` 作为调用数据，发送到指定的目标合约。这种事件实际上是向目标链发起回调请求。第一个回调参数会被网络替换成该 RC 的 ReactVM 地址，用于身份验证。
    
-   **原始链/目标链 (Origin/Destination)**：**原始链**是发生事件的链，**目标链**是接收回调的链。Reactive Contract 可以同时订阅多个原始链的事件，也可根据需要在不同目标链上发起交易。所有回调都通过 Callback Proxy（回调代理合约）中转，以保证安全性。目标合约可以验证回调调用的发送者必须是 Callback Proxy，并且回调里嵌入的 RVM 地址与 RC 匹配。
    
-   **ReactVM**：Reactive Network 的私有执行环境。每个部署的 RC 都有对应的 ReactVM，当订阅事件发生时，对应的 ReactVM 被激活并执行 `react()` 逻辑。ReactVM 内的状态与 RNK 链上的状态分离，互不影响。ReactVM 执行环境是并行的、确定性的，允许多合约同时响应事件。
    
    > _补充：ReactVM 运行时不能调用外部链或服务，只能接收事件并发起回调。_
    
-   **系统合约 (System Contract)**：Reactive Network 提供的链上合约，用于处理订阅管理和支付等功能。RC 在 RNK 环境中通过 `ISystemContract` 接口调用 `subscribe()`、`unsubscribe()`、支付费用等操作。在 ReactVM 环境中，`service.subscribe()` 调用会失败，因此订阅逻辑必须在 RNK 部署时完成。
    
-   **LogRecord**：当事件触发 `react()` 时，Reactive Network 会传入一个 `LogRecord` 结构体，包含了事件的上下文信息（链ID、地址、Topic0-3、数据、块号、日志索引等）。这是 `react()` 方法的参数，合约可以从中提取原始事件数据。
    
-   **回调代理 (Callback Proxy)**：在目标链上，一种中间合约，用于转发 Reactive Network 的回调交易到实际目标合约。它确保只有授权的 ReactVM 地址能调用目标合约，并嵌入了 ReactVM 标识用于验证。目标合约在收到回调时，通常要验证：调用者是 Callback Proxy，并且第一个参数是自己的 ReactVM 地址。
    
-   **REACTIVE\_IGNORE（通配符）**：Reactive Network 定义了一个特殊常量 `REACTIVE_IGNORE`，用于在订阅时忽略某个 Topic。这个值是一串固定的 uint256，表示任何值都匹配。例如 `topic_0 = REACTIVE_IGNORE` 表示匹配所有事件类型。其它字段也可以用 `0` 作为通配符（如地址0匹配所有合约）。
    
-   **Cron 事件**：Reactive Network 提供系统级的周期事件。系统合约可以按区块号间隔触发 `Cron` 事件，RC 可以订阅这些事件来实现定时触发逻辑。例如订阅每 10 个区块触发一次，可以用作定时器，无需外部定时任务。
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->

# 三、ReactiveVM的执行逻辑

监听条件 -> 条件判断 -> 执行动作

典型的Event-Condition-Action (ECA) 模型。

# 四、跨链和自动化机制

Reactive Network的另一个重要能力是：自动化+跨链执行。也就是说系统不仅可以监听事件，还可以在另一条链上执行操作。他的跨链逻辑是：

Chain A 事件发生 -> Reactive Network 监听 -> ReactVM执行逻辑 -> Chain B调用合约

**Reactive Network = Layer1 自动化执行网络**

**ReactVM = 这条链上的执行虚拟机**
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->


# **一、Reactive Network 与传统 EVM 的区别**

## **1️⃣ 传统 EVM：必须有人触发交易**

在EVM当中，智能合约本身是不会主动执行交易的。他的逻辑是：用户发送交易 → 区块链接收交易 → 执行智能合约 → 状态改变。也就是说智能合约=被动程序。只有当某人发送transaction的时候，他才会运行。

假如我写了一个合约：当ETH<$2000就卖出

合约不会自己去检查价格，必须依赖Bot, Keeper, Oracle, Cron Job来不停地检查条件，不然的话合约永远都不会执行。

## **2️⃣ Reactive Network：合约可以“自动响应事件”**

而Reactive Network的核心理念就是Event-driven blockchain。也就是：当某个事件发生 → 自动触发合约逻辑。不需要Bot, keeper那些。逻辑编程：事件发生 → Reactive Network监听 → 自动执行合约。

当某个链上事件发生某个地址转账/某代币价格更新/某合约emit event的时候，Reactive Network就会：监听 → 触发逻辑 → 自动调用目标合约

所以他们的关键区别就是：

| EVM | Reactive Network |
| --- | --- |
| 交易驱动 | 事件驱动 |
| 必须有人发送交易 | 系统自动相应 |
| 依赖Bot | 内置自动执行 |
| 被动合约 | 主动反应 |

# **二、Reactive Network的核心架构**

## ReactVM

ReactVM 可以理解为 Reactive Network 的执行引擎。作用就是执行 reactive contract。与 EVM 的不同点就是ReactVM支持事件触发执行逻辑。甚至可以直接运行「监听式逻辑」。他负责：

1.  监听链上事件
    

例如监听Ethereum logs, contract events, token transfer, price update etc.

2.  触发ReactVM执行逻辑
    

当听到某个事件的时候，会调用目标链合约。例如：监听：

Uniswap price update

↓

检测条件

↓

自动执行套利交易

3.  跨链执行
    

Reactive Network还能做到监听A链事件然后在B链执行交易。例如：

监听 Ethereum event

↓

在 Arbitrum执行交易

这样就可以实现自动套利、自动清算、自动对冲等
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->



**第一步：Git Clone**

· git clone [https://github.com/Reactive-Network/reactive-smart-contract-demos.git](https://github.com/Reactive-Network/reactive-smart-contract-demos.git)

· git clone --recurse-submodules [https://github.com/Reactive-Network/reactive-smart-contract-demos.git](https://github.com/Reactive-Network/reactive-smart-contract-demos.git)

· cd reactive-smart-contract-demos

**第二步：Foundry Environment**

· curl -L [https://foundry.paradigm.xyz](https://foundry.paradigm.xyz) | bash

source ~/.bashrc

foundryup

[https://github.com/Reactive-Network/reactive-smart-contract-demos/tree/main](https://github.com/Reactive-Network/reactive-smart-contract-demos/tree/main)

**第三步：Build**

· forge build

[https://github.com/Reactive-Network/reactive-smart-contract-demos/tree/main/src/demos/basic](https://github.com/Reactive-Network/reactive-smart-contract-demos/tree/main/src/demos/basic)

**第一天任务：**

1\. Origin Contract

· Deployer: 0x2C165514fBF30a120d50540Edabcf88495607142

· Deployed to: 0x88aAF8C2CC77A94c8EB5388D8de8Fe3E214Fca24

· Transaction hash: 0x27b6bd730fe1a46542b75fc53fa9156c945a9209a8d9ba73e091898f5ca7ca24

2\. Destination Contract

· Deployer: 0x2C165514fBF30a120d50540Edabcf88495607142

· Deployed to: 0xE8eBCF3Aa113a1880859041390E8A8438D9B6Ec5

· Transaction hash: 0x2f9f18536464dbf4ad85838104927f38cb6da8037d9ec56e0bd08116f2ff2c39

3\. Reactive Contract

· Deployer: 0x2C165514fBF30a120d50540Edabcf88495607142

· Deployed to: 0xAD0b22F7F5B4dE2997601A7368e40B75414603A5

· Transaction hash: 0xbfcd63f680aacb03764cec8b9f365f9acf210f2224ad947b682f0d103e9a9552
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
