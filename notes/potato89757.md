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
