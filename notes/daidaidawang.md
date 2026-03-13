---
timezone: UTC+8
---

# daidaidawang

**GitHub ID:** daidaidawang

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->
花费了近一周的时间，今天完成了任务一。从学习部署合约，向地址转账，到最后回调成功。感觉收获很多，印象最深的是最后的回调一直在回滚问了AI了解到是`BasicDemoL1Callback` 构造函数接收了 `_callback_sender`。由于之前在Callback合约中开启了 `authorizedSenderOnly`，合约会执行： `require(msg.sender == _callback_sender)`

如果之前传入的 `$env:DESTINATION_CALLBACK_PROXY_ADDR` **不等于** `0xc9f36411C9897e7F959D99ffca2a0Ba7ee0D7bDA`，那么合约就会 Revert，回调回滚

稍微修改了一下原Callback合约不过并不太安全，后续仍需要再完善

-   重新加上 `authorizedSenderOnly` 修饰符
    
-   **精确部署：** 重新部署时，确保 `--constructor-args` 传入的是这次抓到的这个地址：`0xc9f36411C9897e7F959D99ffca2a0Ba7ee0D7bDA`（Reactive Network 官方在 Sepolia 测试网上部署的回调代理合约）
    

```solidity
// SPDX-License-Identifier: GPL-2.0-or-later

pragma solidity >=0.8.0;

import "reactive-lib/abstract-base/AbstractCallback.sol";

contract BasicDemoL1Callback is AbstractCallback {
    event CallbackReceived(
        address indexed origin,
        address indexed sender,
        address indexed reactive_sender
    );

   constructor(address _callback_sender) AbstractCallback(_callback_sender) payable {}

    function callback(address sender) external {
        emit CallbackReceived(
            tx.origin,
            msg.sender,
            sender
        );
    }
}
```

同时今天学习总结了下Reactive的笔记

# **Reactive Contracts**

## **区别传统合约**

传统合约被动，只在直接EOA交易时执行

RC持续监控事件并自动执行预定义的区块链动作响应

## **优点**

-   **控制倒置：**RC通过允许合同本身根据预定义事件决定何时执行，从而反转了传统执行模型，无需外部触发器如机器人或用户。
    
-   **去中心化自动化：**RC实现了完全去中心化的运营，自动化数据收集、DEX交易和流动性管理等流程，无需集中式中介。
    
-   **跨链交互作用：**RC可以与多个区块链和多个源进行交互，实现跨链套利和多预言机数据聚合等复杂用例。
    

## **实际应用**

包括从预言机收集数据、实现UniSwap止损单、执行DEX套利以及自动在交易所间重新平衡池。

# **跨链自动化系统**

> 源链: 触发事件 ↓ 中继器: 监听并转发 ↓ 目标链: cast send $CALLBACK\_ADDR (您当前命令) ↓ 回调合约: 确认跨链消息完成

源合约

目标合约

反应式合约

# **RVM交易**

在REACT中获得资金

## **Payment**

### **源合约直接转账**

资助源合约

```
cast send $env:CONTRACT_ADDR `
  --rpc-url $env:REACTIVE_RPC `
  --private-key $env:REACTIVE_PRIVATE_KEY `
  --value 0.1ether
```

### **回调付款**

**直接转账**

资助回调合同

```
cast send $env:CALLBACK_ADDR `
  --rpc-url $env:DESTINATION_RPC `
  --private-key $env:DESTINATION_PRIVATE_KEY `
  --value 0.1ether
```

**结清未偿还债务**

```
cast send `
  --rpc-url $DESTINATION_RPC `
  --private-key $DESTINATION_PRIVATE_KEY `
  $CALLBACK_ADDR "coverDebt()"
```

## **Deposits**

### **系统合约存款**

> 存入资金 → 检查债务 → 如有债务则自动回拨偿还 → 剩余资金存入

**主动触发执行**

```
cast send `
  --rpc-url $env:REACTIVE_RPC `
  --private-key $env:REACTIVE_PRIVATE_KEY `
  $env:SYSTEM_CONTRACT_ADDR "depositTo(address)" `
  $env:CONTRACT_ADDR `
  --value 0.1ether
```

> 系统合同和回调代理共享相同地址
> 
> 0x0000000000000000000000000000000000fffFfF

### **回调代理存款**

> 用户存款 1 ETH ↓ 系统检查合约A的债务 0.3 ETH ↓ 自动回拨 0.3 ETH 偿还债务 ↓ 剩余 0.7 ETH 存入余额

> 被动，触发协议实现 债务自动偿还扣除

```
cast send `
  --rpc-url $DESTINATION_RPC `
  --private-key $DESTINATION_PRIVATE_KEY `
  $CALLBACK_PROXY_ADDR "depositTo(address)" `
  $CALLBACK_ADDR `
  --value 0.1ether
```

## **Balance**

### **回调合约余额**

检索回调合约余额

```
cast balance $CONTRACT_ADDR --rpc-url $DESTINATION_RPC
```

### **反应式合约余额**

检索反应式合约余额

```
cast balance $CONTRACT_ADDR --rpc-url $REACTIVE_RPC
```

## **Debt**

### **系统合约债务**

```
cast call $SYSTEM_CONTRACT_ADDR "debts(address)" $CONTRACT_ADDR --rpc-url $REACTIVE_RPC | cast to-dec
```

### **回调合约债务**

```
cast call $CALLBACK_PROXY_ADDR "debts(address)" $CONTRACT_ADDR --rpc-url $DESTINATION_RPC | cast to-dec
```

## **Reserves**

### **取回系统合约储备**

```
cast call $SYSTEM_CONTRACT_ADDR "reserves(address)" $CONTRACT_ADDR --rpc-url $REACTIVE_RPC | cast to-dec
```

### **查询回调代理储备余额**

```
cast call $CALLBACK_PROXY_ADDR "reserves(address)" $CONTRACT_ADDR --rpc-url $DESTINATION_RPC | cast to-dec
```
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->

这几天主要学习了如何部署reactive合约，包括设置地址，转账等一系列操作，也对reactive合约的实现流程有了个大致了解

**\[源链（如Sepolia）\] -> \[Reactive Network\] -> \[目标链（如Sepolia）\]**

**(事件发生地) (自动化处理中心) (动作执行地)**

最关键的一步也是reactive合约特色的流程

**event  
↓**

**Reactive监听**

**↓**

**REACT（）**

**↓**

**emitCallback**

**↓**

**目标链 Callback（）**

下面是我部署例题的最后一个的过程，其中涉及了对地址网络的配置，合约部署完成后对合约生成的地址进行转账，产生日志触发回调。

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/daidaidawang/images/2026-03-12-1773331085178-image.png)![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/daidaidawang/images/2026-03-12-1773331114395-image.png)
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->


作为web3新手，第一次正式的跟着学习。今天的任务只完整了部署前两个阶段，卡在了第三个阶段，因为缺少kREACT还没有部署上。目前正在转换，明天继续部署。

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/daidaidawang/images/2026-03-09-1773064550959-image.png)![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/daidaidawang/images/2026-03-09-1773064484606-image.png)![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/daidaidawang/images/2026-03-09-1773064359719-image.png)
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
