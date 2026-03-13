---
timezone: UTC+8
---

# goodperson888

**GitHub ID:** goodperson888

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->
## **1\. 跨链事件与回调合约架构概述**

### **整体数据流**

```
源链（Sepolia）                Reactive Network（Kopli）        目标链（Sepolia）
─────────────────             ──────────────────────────       ─────────────────
                              
用户发送 ETH                                                    
    │                                                           
    ▼                                                           
BasicDemoL1Contract                                             
  receive()                                                     
    │                                                           
    ├─ emit Received(origin, sender, value)                     
    │         │                                                 
    │         │  Reactive Network 监听到事件                    
    │         ▼                                                 
    │   BasicDemoReactiveContract                               
    │     react(log)                                            
    │         │                                                 
    │         │ if log.topic_3 >= 0.001 ether                   
    │         │                                                 
    │         ├─ emit Callback(chainId, callbackAddr, gas, payload)
    │         │         │                                       
    │         │         │  Reactive Network 执行跨链调用        
    │         │         ▼                                       
    │         │   BasicDemoL1Callback                           
    │         │     callback(rsc_address)                       
    │         │         │                                       
    │         │         └─ emit CallbackReceived(origin, sender, reactive_sender)
    │         │                                                 
    └─ 退还 ETH 给 tx.origin                                    
```

### **三合约角色**

| 合约 | 部署链 | 角色 | 核心功能 |
| --- | --- | --- | --- |
| BasicDemoL1Contract | 源链（Sepolia） | 事件触发器 | 接收 ETH，发出 Received 事件 |
| BasicDemoReactiveContract | Reactive Network（Kopli） | 事件监听器 + 决策者 | 订阅事件，满足条件时触发回调 |
| BasicDemoL1Callback | 目标链（Sepolia） | 回调接收器 | 接收跨链回调，记录 CallbackReceived 事件 |

* * *

## **2\. 核心概念**

### **2.1 Reactive Network 是什么**

Reactive Network 是一个专门用于跨链事件响应的区块链基础设施。它：

-   **监听**其他链上的智能合约事件
    
-   **执行**用户定义的响应逻辑（RSC）
    
-   **触发**目标链上的合约调用
    

类比：Reactive Network 就像一个"链上的 IFTTT"（If This Then That）。

### **2.2 RSC（Reactive Smart Contract）**

RSC 是部署在 Reactive Network 上的特殊合约，具备：

-   **订阅能力**：通过 `service.subscribe()` 注册对其他链事件的监听
    
-   **响应能力**：实现 `react()` 函数处理接收到的事件
    
-   **触发能力**：通过 `emit Callback()` 触发目标链上的函数调用
    

### **2.3 双重部署机制（vm 变量）**

这是 Reactive Network 最重要的设计之一：

```
同一份合约代码 → 部署到 Reactive Network
                        │
              ┌─────────┴─────────┐
              │                   │
        主网实例               RVM 副本
        vm = false             vm = true
              │                   │
        执行 subscribe()      执行 react()
        注册事件订阅          处理事件通知
```

**检测原理**：

```solidity
function _detectVm() internal {
    uint256 size;
    assembly { size := extcodesize(0x0000000000000000000000000000000000fffFfF) }
    vm = (size == 0);
    // 系统合约在主网有代码（size > 0，vm = false）
    // 系统合约在 RVM 沙箱无代码（size = 0，vm = true）
}
```

### **2.4 EVM 日志结构**

每个 EVM 事件日志包含：

```
┌─────────────────────────────────────────────────────────────┐
│ topic_0  │ 事件签名哈希 keccak256("EventName(type1,type2)") │
├─────────────────────────────────────────────────────────────┤
│ topic_1  │ 第1个 indexed 参数（最多32字节）                  │
├─────────────────────────────────────────────────────────────┤
│ topic_2  │ 第2个 indexed 参数                               │
├─────────────────────────────────────────────────────────────┤
│ topic_3  │ 第3个 indexed 参数                               │
├─────────────────────────────────────────────────────────────┤
│ data     │ 非 indexed 参数的 ABI 编码                       │
└─────────────────────────────────────────────────────────────┘
```

`Received(address indexed origin, address indexed sender, uint256 indexed value)` 的 topic\_0：

```
keccak256("Received(address,address,uint256)")
= 0x8cabf31d2b1b11ba52dbb302817a3c9c83e4b2a5194d35121ab1354d69f6a4cb
```

### **2.5 REACTIVE\_IGNORE**

订阅时用于"不过滤"某个 topic 的特殊魔法值：

```
REACTIVE_IGNORE = 0xa65f96fc951c35ead38878e0f0b7a3c744a6f5ccc1476b313353ce31712313ad
```

传入此值表示"我不关心这个 topic 的具体值，匹配所有"。
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->

# 一、Reactive Contracts 的真正架构

Reactive Network 的核心模型就是：

```
event → RC → callback transaction
```

完整系统：

```
Origin Chain
   │
   │ event
   ▼
Reactive Network
   │
   │ react()
   ▼
Destination Chain
   │
   │ callback transaction
   ▼
Destination Contract
```

换一种更工程化理解：

```
Event Listener
    ↓
Compute Logic
    ↓
Transaction Executor
```

对应：

| Reactive 概念 | 实际角色 |
| --- | --- |
| event | 数据源 |
| RC | 自动执行逻辑 |
| callback | 交易执行 |

* * *

# 二、Reactive Network 的 4 个核心组件

文档里的关键概念其实只有 4 个。

## 1 ReactVM

ReactVM = Reactive Virtual Machine

对比：

| EVM | ReactVM |
| --- | --- |
| 函数调用 | 事件触发 |
| 用户触发 | 自动触发 |

本质：

```
ReactVM = EVM + Event Engine
```

* * *

## 2 Subscriptions（订阅）

Reactive Contract 必须 **订阅事件**。

类似：

```
subscribe(event)
```

例如：

监听：

```
Uniswap Pair.swap
```

* * *

## 3 Oracle

Reactive Network 通过 **Oracle 节点**监听链。

逻辑：

```
链上事件
↓
Oracle 捕获
↓
ReactVM 执行 RC
```

所以 Reactive 其实是：

**Event Oracle Network**

* * *

## 4 Callback Transaction

Reactive Contract 最终会：

```
发送交易
```

目标链：

```
Destination Chain
```

调用：

```
DestinationContract.callback()
```

* * *

# 三、Reactive Contract 核心代码结构

Reactive Contract 的结构非常固定。

核心函数只有一个：

```
react()
```

示例结构：

```
contract MyReactiveContract is Reactive {

    function react(
        uint256 chainId,
        address emitter,
        bytes calldata data
    ) external {

        // 1 解析事件
        // 2 执行逻辑
        // 3 callback
    }

}
```

react() 的参数：

| 参数 | 作用 |
| --- | --- |
| chainId | 事件来源链 |
| emitter | 事件合约 |
| data | 事件数据 |

* * *

# 四、最核心代码解析

### 1 监听事件

Reactive Contract 会监听：

```
OriginContract.Event
```

例如：

```
event Swap(address user, uint amount);
```

* * *

### 2 react() 被触发

Reactive Network 自动调用：

```
function react(
    uint256 chainId,
    address emitter,
    bytes calldata data
) external {
```

逻辑：

```
if event happened:
    run logic
```

* * *

### 3 解码事件数据

```
(address user, uint amount) =
    abi.decode(data,(address,uint));
```

作用：

解析 event 参数。

* * *

### 4 执行逻辑

例如：

```
if(amount > threshold){
```

例如：

```
触发止损
```

* * *

### 5 callback

Reactive Contract 会发送交易：

```
DestinationContract(dest).execute(user,amount);
```

这一步是：

**跨链执行**

* * *

# 五、Uniswap V2 Stop Order Demo 原理

逻辑：

**价格触发止损**

* * *

传统方式：

```
price bot
↓
monitor pool
↓
send tx
```

Reactive 方式：

```
Swap event
↓
Reactive Contract
↓
trigger stop order
```

* * *

执行流程：

```
Uniswap Swap
↓
emit Swap
↓
Reactive Network
↓
RC react()
↓
execute trade
```

* * *

核心逻辑：

```
if(price < stopPrice){
    sell()
}
```

* * *

# 六、Liquidation Protection Demo

**自动清算保护**

例如：

Aave / lending protocol

* * *

流程：

```
Health factor < threshold
↓
event
↓
Reactive Contract
↓
repay loan
```

作用：

```
防止爆仓
```

* * *

# 七、Foundry 构建流程

Demo 使用：

```
Foundry
```

安装：

```
curl -L https://foundry.paradigm.xyz | bash
foundryup
```

* * *

构建项目：

```
forge build
```

测试：

```
forge test
```

部署：

```
forge script script/Deploy.s.sol --broadcast
```

* * *

# 八、环境变量（必须配置）

Reactive Demo 需要 3 条链。

环境变量：

```
ORIGIN_RPC
DESTINATION_RPC
REACTIVE_RPC
```

还有：

```
PRIVATE_KEY
```

示例：

```
export ORIGIN_RPC=https://rpc.origin
export DESTINATION_RPC=https://rpc.dest
export REACTIVE_RPC=https://rpc.reactive
```

* * *

# 九、部署顺序（非常关键）

正确顺序：

### 第一步

部署：

```
Origin Contract
```

* * *

### 第二步

部署：

```
Destination Contract
```

* * *

### 第三步

部署：

```
Reactive Contract
```

因为 RC 需要：

```
origin address
destination address
```

* * *

# 十、如何证明 Demo 成功

作业需要提交：

### 1 部署地址

```
Origin contract
Reactive contract
Destination contract
```

* * *

### 2 transaction hash

例如：

```
0xabc123...
```

* * *

### 3 event 证明

浏览器：

```
Event Logs
```

* * *

### 4 callback 交易

目标链：

```
Destination contract call
```

* * *

# 十一、解释 Reactive Network：

> Reactive Network 是一个自动监听区块链事件的系统。
> 
> 当某个合约 emit event 时，Reactive Contract 会自动运行 react() 函数。
> 
> 然后它可以在另一条链上发送交易，完成跨链操作。

* * *

# 十二、Reactive Network 最容易被忽视的核心机制

Reactive Contract **不是直接监听链**。

真实流程：

```
Chain Event
↓
Reactive Oracle
↓
ReactVM
↓
react()
```

所以 Reactive Network 是：

```
Event Oracle Network
```

* * *
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->


**Reactive Network 最经典的三合约架构**。  
理解了这一个模型，等于理解了 **Reactive Network 的 80% 工作方式**。

* * *

# 一、基本流程

**在 A 链发生事件 → Reactive Network 监听 → 在 B 链执行合约**

流程：

```
Origin Contract (链A)
     │
     │ emit event
     ↓
Reactive Contract (Reactive Network)
     │
     │ cross-chain callback
     ↓
Destination Contract (链B)
```

核心就是：

**Event → Reactive → Callback**

* * *

# 二、三种合约各自干什么

## 1 Origin Contract（事件源）

作用：

**制造事件**

例如：

```
contract Origin {

    event Trigger(address user, uint amount);

    function trigger(uint amount) external {
        emit Trigger(msg.sender, amount);
    }

}
```

逻辑：

```
用户调用
↓
合约 emit event
```

Reactive Network 会监听这个 event。

* * *

## 2 Reactive Contract（反应合约）

这是整个系统的 **核心**

作用：

**监听事件并触发跨链执行**

逻辑：

```
监听 Origin Contract 的 event
↓
触发 callback
↓
调用 Destination Contract
```

概念：

```
Reactive Smart Contract = Event Listener
```

* * *

## 3 Destination Contract（目标合约）

作用：

**接收回调**

例如：

```
contract Destination {

    function onCallback(address user, uint amount) external {
        // 执行逻辑
    }

}
```

流程：

```
Reactive Contract
↓
call
↓
Destination Contract
```

* * *

# 三、完整流程

```
用户
 ↓
Origin Contract
 ↓ emit event
Reactive Network
 ↓
Reactive Contract
 ↓
Destination Contract
```

或者写成程序逻辑：

```
if OriginContract.emitEvent:
    ReactiveContract.execute()
    DestinationContract.callback()
```

* * *

# 四、和传统跨链的区别

传统跨链：

```
Chain A
 ↓
Bridge
 ↓
Oracle
 ↓
Chain B
```

Reactive Network：

```
Chain A event
↓
Reactive Network
↓
Chain B callback
```

特点：

| 传统跨链 | Reactive |
| --- | --- |
| bridge | event |
| relay | reactive contract |
| message | callback |

* * *

# 五、Reactive Contract 的核心逻辑

Reactive 合约本质有三个步骤：

### 1 注册监听

类似：

```
listen event
```

监听：

```
OriginContract.Trigger
```

* * *

### 2 事件触发

Reactive Network 检测：

```
event happened
```

* * *

### 3 执行回调

```
call DestinationContract
```

* * *

# 六、真实应用例子

假设：

**链A：NFT mint**

**链B：自动发奖励**

流程：

```
用户 mint NFT (Chain A)
↓
Origin emit event
↓
Reactive Contract
↓
Chain B
↓
奖励 token
```

完全自动。

* * *

# 七、基本能力

### 1 Event 设计

例如：

```
event OrderPlaced(address user,uint amount)
```

* * *

### 2 Reactive 监听

监听：

```
OriginContract event
```

* * *

### 3 Callback

执行：

```
DestinationContract function
```

* * *

# 八、最简单的系统理解图

```
┌──────────────┐
│ Origin       │
│ Contract     │
│ emit event   │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Reactive     │
│ Contract     │
│ listen event │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Destination  │
│ Contract     │
│ callback     │
└──────────────┘
```

* * *

# 九、简单解释

> Reactive Network 就像一个自动化系统。
> 
> 当链 A 的合约触发事件时，Reactive Network 会监听这个事件。
> 
> 然后自动调用链 B 的合约。

# 十、最小可运行结构

### Origin

```
emit event
```

* * *

### Reactive

```
listen event
call destination
```

* * *

### Destination

```
receive callback
update state
```

* * *

# 十一、关键点

Reactive Network 的本质是：

**链上自动化系统**

非常像：

```
AWS Lambda
```

或者：

```
if event:
   run function
```

只是：

**发生在区块链上。**
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->



# Reactive Network 本质是什么

一句话：

**Reactive Network = 事件驱动的智能合约网络**

传统链是：

```
用户 → 调用合约 → 合约执行
```

Reactive Network 是：

```
事件发生 → 自动触发合约
```

所以它叫：

**Reactive Smart Contracts（睿应式合约）**

简单说：

| 传统 EVM | Reactive Network |
| --- | --- |
| 用户触发 | 事件触发 |
| pull 模式 | push 模式 |
| 手动调用 | 自动执行 |
| dApp 逻辑在前端 | dApp 逻辑在链上 |

* * *

# 二、对比传统 EVM

## 1 传统 EVM

例如：

DEX 自动清算

流程：

```
价格变化
↓
bot 监控
↓
bot 调用合约
↓
清算
```

所以需要：

-   机器人
    
-   监控程序
    
-   服务器
    

* * *

## 2 Reactive Network

流程变成：

```
价格变化
↓
触发事件
↓
合约自动执行
```

不需要：

-   bot
    
-   后端
    
-   cron job
    

因为：

**链自己监听事件**

* * *

# 三、Reactive Network 的核心架构

官网的架构其实只有两个核心：

## 1 ReactVM

可以理解为：

```
ReactVM = EVM + Event Engine
```

普通 EVM：

```
函数调用执行
```

ReactVM：

```
监听事件
↓
触发函数
↓
执行逻辑
```

* * *

## 2 Reactive Network

其实是一个：

**跨链事件网络**

它可以监听：

| 来源 | 示例 |
| --- | --- |
| Ethereum | transfer |
| Uniswap | swap |
| Chainlink | price update |
| 任何智能合约 | event |

然后触发：

**Reactive Contract**

* * *

# 四、Reactive Smart Contracts 是什么

传统合约：

```
function execute() external {
}
```

必须调用。

* * *

Reactive 合约：

类似：

```
onEvent(event) {
   执行逻辑
}
```

合约自己监听事件。

你可以理解为：

```
if event happened:
    run contract
```

* * *

# 五、Reactive Network 适合解决什么问题

官网核心场景其实就 4 类：

### 1 自动 DeFi

例如：

-   自动清算
    
-   自动套利
    
-   自动对冲
    

不用 bot。

* * *

### 2 自动交易

例如：

```
ETH > 4000
自动卖出
```

不用服务器。

* * *

### 3 跨链触发

例如：

```
Solana 发生事件
↓
Ethereum 合约执行
```

* * *

### 4 Web3 Automation

例如：

-   DAO 投票结束自动执行
    
-   NFT mint 自动分发
    
-   staking 自动复投
    

* * *

# 六、Reactive Network 和 Chainlink Automation 区别

|   | Reactive Network | Chainlink Automation |
| --- | --- | --- |
| 触发方式 | 事件 | 定时 / 条件 |
| 执行位置 | 链上 | Oracle |
| 架构 | Reactive VM | Keeper |

Reactive 更像：

**操作系统级别自动化**

* * *

# 七、要掌握的 5 个概念

### 1 什么是 Reactive Contract

```
事件触发的智能合约
```

* * *

### 2 ReactVM 是什么

```
支持事件监听执行的 EVM
```

* * *

### 3 Reactive Network 解决什么问题

```
链上自动化
```

* * *

### 4 Reactive Network 和 Bot 区别

```
Bot = offchain
Reactive = onchain
```

* * *

### 5 Reactive Network 的核心思想

```
Event → Trigger → Execute
```

* * *

# 八、**简单解释 Reactive Network**

> Reactive Network 就像手机的通知系统。
> 
> 当事件发生时，系统自动触发程序。
> 
> 不需要用户点击。
<!-- DAILY_CHECKIN_2026-03-10_END -->
<!-- Content_END -->
