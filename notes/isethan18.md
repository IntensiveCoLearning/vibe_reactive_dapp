---
timezone: UTC+8
---

# isethan18

**GitHub ID:** isethan18

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->
## 2026.03.10

## Day 2：核心组件 RSC 与开发准备

**核心点：搞定水龙头，看懂合约长什么样**

### 1\. 什么是 Reactive Smart Contract (RSC)？

它不是普通的 Solidity 合约。它必须实现 `IReactive` 接口，关键在于 `onEvent` 函数：

Solidity

// 伪代码：RSC 的核心逻辑

function onEvent(

uint256 chainId,

address \_contract,

uint256 topic0,

bytes calldata data

) external {

// 1. 这里接收到了“偷听”到的事件数据

// 2. 根据数据做判断（比如价格是否达标）

// 3. 如果达标，构造一个 Callback 交易发出去

}

### 2\. 准备工作（现在就做）：

-   **添加网络：** 将 **Lasna Testnet** 添加到 MetaMask。
    
-   **领水：** \* 前往 [Reactive Faucet](https://www.google.com/search?q=https://dev.reactive.network/faucet&authuser=1) 领 REACT。
    
    -   准备一些 **Sepolia ETH**（作为源链 Gas）。
        
-   **学习材料速读：** 重点看 [Reactive Contracts 文档](https://dev.reactive.network/education/module-1/reactive-smart-contracts) 里的 `AbstractReactive` 类，这是代码的模板。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->

## 2026.03.09

## Day 1：理解“睿应层”与传统 EVM 的根本区别

**核心点：从“拉取（Pull）”到“推送（Push）”**

-   **传统合约（被动）：** 逻辑像死水，必须有外部交易（EOA）来“踢”它一脚（调用函数），它才会动。
    
-   **Reactive 合约（主动）：** 逻辑像雷达。你部署它时，会给它一个“订阅清单”。一旦目标链上出现了清单里的 `Log`，Reactive Network 会主动把数据推给它，触发执行。
    

### 必须要知道的两个概念：

1.  **ReactVM：** 这是一个不保存状态资产、只跑逻辑的“大脑”。它能跨链读取，但不会直接改变原链状态，除非通过 **Callback（回调）**。
    
2.  **Inbound (入金) / Outbound (出金)：** \* **Inbound：** 监听其他链（如 Sepolia）的事件。
    
    -   **Outbound：** 逻辑跑完后，发出一笔交易去影响目标链。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
