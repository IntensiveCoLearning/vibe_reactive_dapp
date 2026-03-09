---
timezone: UTC+8
---

# Macy

**GitHub ID:** L-Macy

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->
**Reactive**

1\. Reactive 与传统 EVM 的根本区别传统 EVM 智能合约采用被动执行模型（imperative / transaction-driven）：合约仅在接收到外部交易（EOA 或其他合约调用）时执行，逻辑依赖外部主动触发，易引入中心化依赖（如 keepers、bots、oracles）。Reactive 智能合约采用事件驱动架构（event-driven）与控制反转（Inversion of Control, IoC）：合约自主订阅任意 EVM 链上的事件日志（logs，包括 topic0），事件发生后由 ReactVM 自动评估 Solidity 条件逻辑，满足则触发回调执行或跨链交易。执行流从“外部调用合约”转变为“合约自主响应事件”，实现完全 on-chain 的去中心化自动化。2. 核心架构：ReactVM + Reactive Network

-   ReactVM：隔离的执行环境，负责 Reactive 合约的事件处理、状态更新、条件评估及回调执行，支持多链事件输入。
    
-   Reactive Network：去中心化事件中继与执行层，捕获订阅事件、转发至 ReactVM，并确保跨链回调的可靠交付与执行。
    
-   整体形成闭环：事件订阅 → 检测 → on-chain 评估 → 自动交易发起（可跨链）。
    

3\. Reactive Smart Contracts 定义与机制Reactive Smart Contracts（RCs）是事件驱动的自治智能合约，支持：

-   事件订阅模型：部署时指定链、合约地址、事件签名（topics），实现对链上/跨链事件的持续监控。
    
-   状态积累与条件评估：事件触发后更新内部状态（如历史数据聚合），并在 Solidity 中执行条件判断。
    
-   回调执行：条件满足时自动发起 EVM 交易（本链或跨链），支持复杂多步逻辑组合。
    
-   多源集成：原生支持链上事件 + oracle 数据，无需外部中间件。
    

关键特性：全 on-chain 反应性（on-chain reactivity）、无信任自动化、跨链原生支持。4. 适用问题域Reactive 针对传统 EVM 难以高效实现的场景：

-   跨链自动化协议（cross-chain flash loans、escrow、bridging without intermediaries）。
    
-   DeFi 自动化策略（limit/stop orders、arbitrage、TWAP、auto-rebalancing）。
    
-   多 oracle 聚合与去中心化决策。
    
-   实时事件响应（如行为触发奖励、动态 NFT）。
    
-   取代 off-chain keepers 的全链上自动化执行。
    

一句话概括：Reactive 将事件驱动范式引入智能合约层，实现去中心化、跨链的“If-This-Then-That”原语。

**个人技术心得**

通过 Reactive，智能合约从被动响应转向主动观察与自治执行，显著降低了自动化方案的中心化风险与运维复杂度。尤其在跨链 DeFi 与复杂条件触发场景中，其原生**事件订阅**与**回调机制**提供了比 Chainlink Automation / Gelato 更彻底的去中心化替代方案。后续开发中，可优先考虑将 keeper 依赖逻辑迁移至 Reactive 模型。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
