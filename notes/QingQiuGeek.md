---
timezone: UTC+8
---

# QingQiu

**GitHub ID:** QingQiuGeek

**Telegram:** @qingqiu

## Self-introduction

本科大四，软件工程专业，已有两段java实习，略懂全栈，有从0到1开发全栈项目经历，前不久参加过一次Permissionless模式的web3冬季实习计划并坚持到最后，目前已经学习完solidity、go基础语法，对web3、ai有深厚兴趣

## Notes

<!-- Content_START -->
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
````markdown

## Reactive 基础概念

- Reactive Network：本质是一条独立的区块链网络，专门用于：监听其他链事件、执行自动逻辑、触发跨链交易。有两种执行环境：

  Reactive Network ：EOA 与合约互动的公链，管理订阅。

  Reactive Network ：私有执行环境，用于事件处理的执行环境。该环境中Reactive Contracts无法直接访问外部系统。它们从反应式网络接收事件日志，并能向目的链发送回调事务，但无法与外部 RPC 端点或链外服务交互。

- Reactive Contract：部署在 Reactive Network 上的智能合约，Reactive Contracts可以在部署期间或部署后通过 Sourcify 端点进行验证。Sourcify 是一个去中心化的验证服务，能够将部署的字节码与源代码匹配，使合同可审计且透明。

- 起源 (Origin)：事件发生的源头链，Reactive Network“监听”源链特定的日志（Logs）。

- 目的地 (Destination)：最终执行动作的目标链。

- 回调 (Callback)：Reactive Network在监听到源链的事件后，向 Destination 发出的“执行指令”。

- 回调地址 (Callback Proxy Address)：部署在目的地链上的一个特殊合约（Proxy），用于接收并校验来自 Reactive Network 的指令。

- Hyperlane：跨链消息传输协议，Reactive Network 的智能合约无法自己直接操作另一条链，这就需要Hyperlane完成跨链
  。

  **Reactive Contracts收到源链消息后，发送回调指令，调用 Reactive Network 上的 Hyperlane Mailbox 合约的 dispatch() 函数，Mailbox 接收指令后给它贴上“目的地地址”、“目标链 ID”和“消息负载”的标签，并将其存入出站队列，发送给目的地链的Hyperlane Mailbox。目的地链的 Hyperlane Mailbox 会触发 handle() 函数，将指令最终送达回调代理地址。**

## Reactive Network的经济机制

**Reactive Network 通过 REACT 代币 + 余额 + 债务系统 来支付智能合约执行和跨链回调的费用。**

Reactive Contract 类似一个钱包账户，必须有REACT TOKEN余额，用于支付计算费用、支付 callback 费用、支付跨链交易，如果余额不足合约将无法运行。

### Reactive Network的合约资金分成三种状态

1. Balance（余额）：合约当前可用资金，可以直接用于执行任务。

2. Debt（债务）：系统记录的未结算费用。产生原因：任务已经执行但费用还没完全结算。

3. Reserves（储备）：系统预留的资金，用来保证callback 可以正常执行、资金安全、系统稳定，相当于押金。

### Reactive Contract运行时需要支付两类费用：

1. RVM 执行费用：执行时不需要用户手动提交 gas price，RVM 执行完策略逻辑再统一结算费用。从余额中扣除。

2. 跨链、回调费用：包含跨链执行费用、callback 交易费用。执行前从余额中预留一部分到 Reserves，从Reserves扣除，确保最后一步的回调不会失败！

```
假设做一个自动策略：当 Ethereum 上 ETH 价格低于 2000 USDC 时，自动在 Arbitrum 上买入 ETH。流程如下：

1 Ethereum发生价格更新
      │
      ▼
2 Reactive Network监听到事件
      │
      ▼
3 Reactive Contract执行代码逻辑，判断需要买ETH
      │
      ▼
4 Reactive Contract通过Hyperlane发送跨链消息
      │
      ▼ dispatch()
5 Hyperlane Mailbox (Reactive Network)
      │
      ▼
6 Hyperlane Mailbox (Destination Chain)
      │
      ▼ handle()
7 Callback Proxy   ← 回调代理
      │
      ▼
8 Target Contract  ← 真正的目标合约
```

## Subscription订阅

Subscription 让 Reactive Contract 监听某条链上某种事件，当事件发生时自动触发合约逻辑。

一个Subscription包含chain_id、contract（监听哪个合约）、topic_0（事件类型）、topic_1（事件参数1）...这些参数来自 EVM event log topics。

一个Reactive Contract 可以监听多个事件。

不能创建“全局订阅”，系统不允许：监听所有链、监听所有合约、监听某链所有事件，必须指定链+合约/事件

### 如何创建 Subscription

通过 Reactive Network system contract 调用：service.subscribe(...)

1. 在 constructor 中创建（静态订阅），适合永久监听某事件。

2. Reactive Contract 根据事件动态创建订阅。

### Wildcard 通配符监听

例如：

```
| 参数     | 通配方式         |
| -------- | --------------- |
| contract | address(0)      |
| chain_id | 0               |
| topics   | REACTIVE_IGNORE |

```

监听某合约 所有事件

```
subscribe(
 chainId,
 contractAddress,
 REACTIVE_IGNORE,
 REACTIVE_IGNORE,
 REACTIVE_IGNORE,
 REACTIVE_IGNORE
)
```

## 问题

![](image.png)
````
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->




## Reactive Basics

1.  源链和目标链、回调代理地址
    

起源 (Origin)：事件发生的源头链。Reactive 网络在这里“监听”特定的日志（Logs）。

目的地 (Destination)：最终执行动作的目标链。

回调 (Callback)：Reactive 网络在监听到 Origin 的事件后，向 Destination 发出的“执行指令”，是连接“监听”与“执行”的桥梁。

回调地址 (Callback Proxy Address)：部署在目的地链上的一个特殊合约（Proxy），用于接收并校验来自 Reactive 网络的指令。是安全守门员，确保只有合法的 Reactive 合约能触发目标动作。

有些网络还不能作为目的地链，因为回到代理合约未部署。在这种情况下，使用 Hyperlane 作为跨链回调的传输工具。

2.  Hyperlane跨链消息传输协议
    
3.  Reactive Contracts
    

事件驱动的智能合约，订阅链上事件并自动触发逻辑.

4.  ReactVM
    

Reactive Network 的执行环境

5.  Economy（经济机制）
    

-   Callback 的费用机制
    
-   Reactive 的支付模型
    

## Reactive Essentials

1.  Reactive Contracts 机制
    
    -   reactive execution
        
    -   自动执行逻辑
        
2.  Events & Callbacks
    
    -   EVM events
        
    -   callback 触发机制
        
3.  ReactVM 与 Reactive Network 环境
    
    -   双状态执行环境
        
    -   状态管理
        
4.  Subscriptions
    
    -   合约订阅链上事件
        
5.  Oracles
    
    -   链下数据接入
        
6.  实践案例
    
    -   部署 Reactive Contract
        
    -   demo：监听日志并触发 callback
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->






用remix跑了basic的三个合约
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->








打卡，今天看来co-learning直播，看了reactive官方文档
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
