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
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
**ReactVM and Reactive Network As a Dual-State Environment**

**1\. 学习目标（Lesson Objectives）**

-   区分 Reactive 合约在两个执行环境中的实例与状态。
    
-   识别当前执行上下文（Reactive Network vs ReactVM）。
    
-   掌握双状态（dual-state）管理机制。
    
-   理解 Reactive 合约处理的交易类型及其在双环境中的流动。
    

**2\. 核心概念：Dual-State Environment（双状态环境）Reactive 合约并非单一实例，而是同时存在于两个独立环境中，每个环境维护独立的存储状态：**

-   Reactive Network（主链环境）：
    
    -   这是合约的“真实部署”位置（用户交互、状态持久化、交易发起）。
        
    -   合约代码与状态存储在此链上，类似于传统 EVM 合约。
        
    -   这里处理：用户直接调用、订阅设置、回调交易的最终广播与执行。
        
-   ReactVM（隔离执行环境）：
    
    -   专为 Reactive 逻辑设计的沙箱/虚拟机。
        
    -   合约在此环境被“镜像”执行，用于处理事件触发后的反应逻辑。
        
    -   状态独立：ReactVM 中的存储（storage）与 Reactive Network 中的分离，避免事件处理干扰主链状态。
        
    -   这里处理：事件日志输入、回调函数执行、条件评估。
        

双状态的必要性：

-   事件处理可能高频、计算密集，若在主链直接执行会阻塞或 gas 爆炸。
    
-   ReactVM 提供隔离、可扩展的执行空间，确保主链状态安全、一致。
    
-   事件 → ReactVM 计算 → 若需变更主链状态 → 发起回调交易回传 Reactive Network。
    

**3\. 执行上下文识别与状态管理**

-   上下文区分：合约代码需判断当前运行在哪个环境。
    
    -   使用系统接口（如 isReactVM() 或类似标志）识别。
        
    -   示例逻辑：
        
        -   在 ReactVM 中：只读事件数据、更新本地状态、决定是否触发回调。
            
        -   在 Reactive Network 中：处理用户 tx、接收回调结果、更新持久状态。
            
-   状态管理原则：
    
    -   分离存储：两个环境各有独立 storage slot。
        
    -   同步机制：回调交易从 ReactVM 发出，携带必要数据回传 Reactive Network，实现状态最终一致性。
        
    -   数据流向：事件数据 → ReactVM（输入） → 计算/决策 → 回调 tx → Reactive Network（输出/更新）。
        

**4\. 交易类型与流动Reactive 合约涉及三种主要交易：**

-   用户发起交易（User-initiated tx）：直接调用合约函数（e.g. 配置订阅、设置参数），在 Reactive Network 执行。
    
-   回调交易（Callback tx）：ReactVM 评估后自动生成，从 ReactVM 环境发起，目标通常是 Reactive Network 中的合约自身或其他链，实现自动化动作。
    
-   事件驱动交易：由 Reactive Network 中继的事件触发的间接 tx，最终落地在主链。
    

关键：回调 tx 由系统 gas 支付（或预付费机制），开发者无需手动资助，确保自治性。

**5\. 关键 takeaway 与开发启发**

-   双状态是 Reactive 架构的核心创新：隔离事件处理与主链状态，兼顾性能、安全与去中心化。
    
-   开发时必须显式处理上下文切换，避免状态混淆（e.g. 在 ReactVM 中写只读逻辑，在 Network 中写写操作）。
    
-   与传统合约对比：传统合约单环境、无需上下文判断；Reactive 合约需“双脑”设计，但换来自治与跨链能力。
    
-   后续关注：订阅设置（Lesson 4）如何在双环境中协调。
    

Reactive 合约的双状态本质：Reactive Network 作为持久主环境，ReactVM 作为反应隔离层，二者通过回调交易桥接，形成高效的自治闭环。这一设计有效解决了高频事件处理对主链的压力，同时保持全 on-chain 特性。理解 dual-state 是编写可靠 Reactive 合约的前提，后续订阅与回调实现将基于此展开。相比前两课的概念引入，本课提供了更底层的架构视角。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

**Events and Callbacks 工作原理**

**1\. 学习目标（Lesson Objectives**）

-   理解 EVM 事件（Events）在 Reactive 体系中的核心作用。
    
-   掌握回调（Callbacks）的执行机制及其与事件触发的关系。
    
-   认识 Reactive 如何将传统事件日志转化为自治执行流。
    
-   通过实际示例（如 Chainlink 价格 oracle 集成）理解事件 → 回调的闭环。
    

**2\. 核心概念：EVM Events 在 Reactive 中的角色**

-   传统 EVM Events：合约通过 emit Event(...) 记录日志（logs），外部可通过 RPC/filter 监听，但合约本身无法主动响应。
    
-   Reactive 中的 Events：事件成为触发源。Reactive 合约订阅特定日志（chainId + contract + topics），事件发生时日志被 Reactive Network 捕获并推送至 ReactVM。
    
-   关键转变：事件从“被动可查询记录” → “主动触发信号”，驱动合约自治执行。
    

**3\. Callbacks 机制详解**

-   回调定义：事件触发 + 条件满足后，ReactVM 自动调用 Reactive 合约中预定义的回调函数（callback）。
    
-   执行流程：
    
    1.  订阅事件（订阅参数：链 ID、源合约地址、event signature/topics）。
        
    2.  事件 emit → Reactive Network 检测并中继日志数据。
        
    3.  ReactVM 接收日志 → 解码为 Solidity 可处理参数 → 调用合约的 react() 或指定回调函数。
        
    4.  合约内部执行逻辑（状态更新、条件判断）。
        
    5.  若条件成立 → 发起回调交易（可本链或跨链）。
        
-   原子性与可靠性：回调在 ReactVM 内原子执行，支持重试机制，确保“事件 → 动作”不丢失。
    
-   与传统区别：无需外部 keeper 轮询或 gas 支付回调，全部 on-chain 自动化。
    

**4\. 实际示例：Chainlink 价格 Oracle 集成**

-   场景：Reactive 合约订阅 Chainlink 的 AnswerUpdated(int256,uint256,uint256) 事件。
    
-   流程：
    
    -   订阅 Chainlink Aggregator 合约的 price update 事件。
        
    -   价格更新 emit → ReactVM 接收新价格。
        
    -   合约逻辑：检查价格是否超过阈值 → 若超过，自动触发止损/套利/再平衡等回调。
        
-   价值：实现纯 on-chain 的价格敏感自动化，无需 off-chain oracle caller 或 bot。
    

**5\. 关键 takeaway 与技术启发**

-   Events + Callbacks 是 Reactive 的“神经 + 肌肉”：事件提供感知，回调实现行动。
    
-   这一机制使 Reactive 合约真正成为“活的代理”（autonomous agents），适用于任何需要实时响应外部状态变化的场景。
    
-   开发视角：后续需掌握订阅参数的精确构造（topics 编码）和回调函数的 gas 优化。
    

今日心得：

深入剖析了 Reactive 的触发-执行闭环：事件作为输入信号，回调作为输出动作，彻底消除了传统合约对外部触发器的依赖。通过 Chainlink 示例可见，Reactive 可无缝集成现有 oracle 基础设施，实现更高效的 DeFi 自动化。相比 Lesson 1 的概念理解，本课提供了可操作的机制视角，为后续订阅与 ReactVM 细节打下基础。
<!-- DAILY_CHECKIN_2026-03-10_END -->

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
