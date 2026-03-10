---
timezone: UTC+8
---

# W5W8L9jlu

**GitHub ID:** W5W8L9jlu

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->
# 导论

> # **Why Reactive Contracts 为什么选择反应式合同**
> 
> In the Ethereum world, smart contracts have revolutionized how we conceive of executing trustless agreements. Traditionally, these contracts spring into action only upon a user-initiated transaction. This presents inherent limitations. For one, smart contracts can't autonomously initiate actions or respond to blockchain events without an external prompt — either from a user or an automated script like a trading bot. This requires holding private keys and introducing a centralized point of control.在以太坊世界中，智能合约彻底改变了我们对执行无信任协议的认知。传统上，这些合同只有在用户发起的交易时才会生效。这带来了固有的局限性。首先，智能合约无法在没有外部提示的情况下自主发起动作或响应区块链事件——无论是来自用户还是像交易机器人这样的自动脚本。这需要持有私钥并引入集中控制点。
> 
> Reactive Contracts (RCs) emerge as a solution to this constraint. RCs are designed to autonomously react to events in the Ethereum Virtual Machine (EVM) and trigger subsequent actions across the blockchain ecosystem. This capability for the implementation of complex logic that can source information from multiple chains and enact changes or transactions across various platforms without a central oversight.反应式合约（RCs）作为解决这一限制的方案而出现。RCs 设计为自主响应以太坊虚拟机（EVM）中的事件，并触发区块链生态系统内的后续作。这种能力实现了复杂逻辑，能够从多条链中获取信息，并在不同平台上执行变更或交易，而无需中央监督

1.  智能合约是什么
    

以前做交易需要**信任第三方**

比如：

-   信任银行
    
-   信任中介
    
-   信任律师
    

通过第三方的背书完成交易

**智能合约可以自动执行规则（执行无信任协议）**。

比如：

写一段代码

```
if 如果 A 给钱 : 
	就自动把 NFT 给 A
```

整个过程：

-   没人能作弊
    
-   不需要中间人
    
-   代码自动执行
    

1.  智能合约的局限
    

智能合约是非自动的，需要**靠用户/机器人 bot 调用执行**。

举个例子（Uniswap 交换代币）：

点击 swap → 签名交易 → 发送交易 → 合约才开始运行。如果没有人为触发，合约就一直睡觉。

智能合约的触发，必须有人持有**私钥**，并且产生一个中心化的控制点。

比如： 机器人监听链上事件 → 发现机会 → 用**私钥**发交易。这个机器人控制私钥并决定什么时候触发 = 一个中心控制点。

而这个中心控制点**违背了去中心化**

1.  **Reactive Contracts**
    

**Reactive Contracts，“无需别’人‘触发，会自己反应的合约” =** 监听链上的事件，满足特定条件时，自动对 EVM（以太坊虚拟机）中的事件做出反应。

RCs能从**多条区块链获取信息**，在**不同平台执行**操作或交易

举例：

Reactive Contract 可以同时看很多链（ETH链/BSC/Arbitrum/Polygon）的信息，然后做判断——如果A链价格低、B链价格高，就套利

RCs 不需要中央监管，因此不会造成中心控制点。

RCs 不需要：

-   中心服务器
    
-   执行策略
    
-   管理员
    
-   私钥控制
    

1.  RCs 的优势
    

-   去中心化：RC 在区块链上独立运行，消除集中控制点，通过降低作或失败的风险提升安全性。
    
-   自动化：RC 自动响应链上事件执行智能合约逻辑，减少人工干预需求，实现高效、实时的响应。
    
-   跨链互作性：RC 可以与多个区块链交互，实现复杂的跨链交互，提升多样性并弥合网络间的差距。
    
-   提升效率与功能性： 通过对实时数据的响应，RC 提升了智能合约的效率，支持复杂金融工具、动态 NFT 和创新 DeFi 应用等先进功能。
    
-   DeFi 及其他领域的创新：RC 为 DeFi 及其他区块链应用（如自动化交易和动态治理）带来了新可能，打造了一个更具响应性和互联互通的区块链生态系统。
    

* * *

# lesson 1

> Reactive vs. Traditional Contracts: Unlike traditional smart contracts, RCs autonomously monitor blockchain events and execute actions without user intervention, providing a more dynamic and responsive system. 反应式合约与传统合约： 与传统的智能合约不同，反应式合约能够自主监控区块链事件并执行操作，无需用户干预，从而提供更动态、响应更迅速的系统。
> 
> Inversion of Control: RCs invert the traditional execution model by allowing the contract itself to decide when to execute based on predefined events, eliminating the need for external triggers like bots or users. 控制反转： RC 合约通过允许合约本身根据预定义的事件决定何时执行，从而颠覆了传统的执行模型，消除了对机器人或用户等外部触发器的需求。
> 
> Decentralized Automation: RCs enable fully decentralized operations, automating processes like data collection, DEX trading, and liquidity management without centralized intermediaries. 去中心化自动化： RC 实现完全去中心化的运营，无需中心化的中介机构即可自动执行数据收集、DEX 交易和流动性管理等流程。
> 
> Cross-Chain Interactions: RCs can interact with multiple blockchains and sources, enabling sophisticated use cases like cross-chain arbitrage and multi-oracle data aggregation. 跨链交互： RC 可以与多个区块链和数据源进行交互，从而实现复杂的用例，例如跨链套利和多预言机数据聚合。
> 
> Practical Applications: RCs have diverse applications, including collecting data from oracles, implementing UniSwap stop orders, executing DEX arbitrage, and automatically rebalancing pools across exchanges. 实际应用： RC 有多种应用，包括从预言机收集数据、实施 UniSwap 止损单、执行 DEX 套利以及自动在交易所之间重新平衡资金池。

1.  控制反转（IoC）
    

传统智能合约是：机器人/用户 → 调用合约。依赖机器人/用户

Reactive Contract ：链上事件 → 自动触发合约。事件驱动，合约内部执行。

1.  Reactive Contract 是怎么工作的
    

创建 RC 时，需要先告诉它三件事：

### 1 要监听哪些链

比如

```
Ethereum
Arbitrum
Polygon
```

### 2 要监听哪些合约

例如

```
Uniswap pool
某个借贷协议
某个预言机
```

### 3 要监听哪些事件

例如

```
Swap
Transfer
Loan
Vote
Whale交易
```

然后系统就会：

```
监听事件
↓
事件发生
↓
触发 RC
↓
执行逻辑
↓
发交易
↓
更新**状态**

```

**状态包括：**

-   **记录历史数据**
    
-   **积累数据**
    
-   **根据条件触发操作**
    

1.  Reactive Contract 能做什么
    

## 1 预言机数据整合

RC 可以监听多个预言机：

例如：

```
Chainlink
Pyth
其他oracle
```

然后：

```
取平均价格
↓
执行操作
```

比如：

```
比赛结果 → 自动派奖
```

## 2 Uniswap 自动止损

RC 可以监听：

```
Uniswap swap事件
```

然后计算：

```
当前价格
```

如果价格达到：

```
止损价
```

就自动：

```
执行 swap
```

## 3 DEX 套利

RC 可以：

同时监听多个池子

例如：

```
Uniswap
SushiSwap
Balancer
```

如果发现：

```
价格差
```

就自动：

```
套利交易
```

可以：

-   单链套利（flash loan）
    
-   跨链套利
    

## 4 流动性池自动再平衡

RC 还可以：

监控多个交易所的流动性。

例如：

```
A链池子太多资金
B链池子太少
```

RC 就会：

```
自动转移资金
```

实现：

**自动再平衡 liquidity pools**

* * *
<!-- DAILY_CHECKIN_2026-03-10_END -->
<!-- Content_END -->
