---
timezone: UTC+8
---

# hwish39-byte

**GitHub ID:** hwish39-byte

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->
# 打卡
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->

# 打卡
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->


# 打卡
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->



# 继续学习reactive，完成挑战第一、二关
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->




# 继续学习reactive，参与workshop
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->





# 今天继续阅读Reactive Contracts 文档，理解 ReactVM 的执行逻辑

## ReactVM 是 Reactive Network 的心脏，它的执行逻辑与传统 EVM 有本质区别：

-   **双状态模型 (Dual-state Model)：**
    
    -   **Reactive Network 状态：** 全局共享，负责管理订阅（Subscriptions）和跨链协同。
        
    -   **私有 ReactVM 状态：** 每个合约拥有独立的运行环境，状态隔离。这意味着 Reactive 合约在处理逻辑时非常安全，互不干扰。
        
-   **逻辑流：**
    
    1.  **输入：** 监听到的 Origin 链事件日志（Event Logs）。
        
    2.  **处理：** react() 函数在私有 ReactVM 中执行，进行条件判断（如：检查金额、白名单）。
        
    3.  **输出：** 如果条件满足，生成一个指向 Destination 链的异步交易调用（Callback）。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->






# 今天主要阅读官网 Overview 和文档，理解 Reactive Network 与传统 EVM 的根本区别（主动推送 vs 被动调用）。研究“事件驱动智能合约”的概念。

## 1\. 核心概念：

### 在今天的学习中，我最深刻的体会是：**区块链正在从“自动机”进化为“反应堆”。**

-   **传统 EVM (被动调用模型)：**  
    智能合约是“死”的。通过外部账户 (EOA) 发起交易，来触发智能合约。它是**交互式**的。
    
-   **Reactive Network (主动推送模型)：**  
    智能合约是“活”的。能够实时监听（Subscribe）区块链上的底层日志事件，并根据预设条件自动做出反应（React）。它是**事件驱动**的。
    

## 2\. 主要特征：

### 睿应式智能合约不再仅仅处理本链的状态，还具备以下独特属性：

-   **全链监听：** 能够监听来自以太坊主网、L2 甚至其他异构链的事件。
    
-   **零延迟反应：** 通过 ReactVM 在共识层捕捉事件，无需等待中继器处理。
    
-   **自主性：** 逻辑触发不再依赖于用户手动签名，而是由链上发生的“事实”（Event Log）驱动。
    

## 3\. 实用场景：

### 根据今日的学习内容，分析睿应层（Reactive Network）适合解决什么问题，我总结了 3 个 Reactive Network 能够适用、而传统 EVM 难以实现的场景：

场景1：跨链自动补仓

-   **痛点：** 用户的抵押资产在 A 链（如 Ethereum），但稳定币在 B 链（如 Arbitrum）。当 A 链面临清算风险时，传统做法需要用户手动跨链并补仓，极易因延迟导致爆仓。
    
-   **Reactive 方案：** 部署一个 Reactive 合约，订阅 A 链借贷协议的 UpdateHealthFactor 事件。一旦健康系数低于阈值，合约自动触发 B 链的跨链转账并完成补仓。
    

场景2：多链动态 NFT 同步

-   **痛点：** 一个 NFT 资产在主网，但其代表的游戏角色在 L2 进行战斗升级。要实时反映游戏数据（如等级、装备）到主网 NFT 的元数据，通常需要复杂的预言机架构。
    
-   **Reactive 方案：** Reactive 合约实时监听 L2 游戏合约的 LevelUp 事件，并在事件发生的瞬间，自动向主网发起交易，更新 NFT 的链上属性。
    

场景3：自动化多链 DAO 治理执行

-   **痛点：** 许多 DAO 在以太坊主网投票，但其协议部署在多个 L2。目前执行投票结果需要通过中继器或多签手动在各链操作，存在信任风险和滞后。
    
-   **Reactive 方案：** 治理合约在主网抛出 ProposalPassed 事件，Reactive 网络捕捉到该信号后，自动触发所有目标链（Polygon, Optimism, Base 等）上的预设执行函数，实现“一处投票，全链生效”。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->







# 今天来不及学习了，先打卡
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
