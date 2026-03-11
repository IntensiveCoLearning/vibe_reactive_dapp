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
