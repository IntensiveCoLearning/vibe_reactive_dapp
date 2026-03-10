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
