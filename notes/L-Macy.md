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
# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->
Uniswap V2 集成与 Reactive Stop Order 实战

-   今日重点（Module 2 延续）：
    
    -   深入 Uniswap V2 池机制 + Reactive 订阅 Sync 事件，实现止损单（Stop Order）原型。
        
    -   目标：从订阅 → react() 逻辑 → Callback 执行完整闭环。
        
-   Uniswap V2 关键回顾：
    
    -   Sync 事件：event Sync(uint112 reserve0, uint112 reserve1) — 储备更新后 emit，用于实时价格计算。
        
    -   价格计算：price = reserve1 / reserve0（或反向，根据 token 顺序）。
        
    -   Swap 函数：swap(uint amount0Out, uint amount1Out, address to, bytes data) — Callback 中调用实现自动卖出。
        
-   Reactive Stop Order 示例流程（GitHub demo：uniswap-v2-stop-order）：
    
    1.  订阅：构造函数中静态订阅 Uniswap Pair Sync 事件。
        
        -   topics\[0\] = keccak256("Sync(uint112,uint112)")
            
        -   originChainId = 1（Ethereum）或其他。
            
    2.  Trigger & react()：事件触发 → ReactVM 调用 react(LogRecord log)。
        
        -   解码：uint112 reserve0 = abi.decode([log.data](http://log.data), (uint112, uint112));
            
        -   计算当前价格 vs 用户设置阈值（stopPrice）。
            
    3.  Callback：价格 ≤ stopPrice → emit Callback 到执行合约。
        
        -   payload：abi.encodeWithSignature("executeStopLoss()", amount, recipient 等)。
            
        -   执行：swap token → 转出 ETH/稳定币 → emit Stop 事件确认。
            
    4.  可选：订阅 Stop 事件结束循环。
        
-   开发要点：
    
    -   使用 AbstractReactive 基类（或类似）。
        
    -   Callback gas limit ≥ 100k，避免失败。
        
    -   REACT 余额预存支付 Callback fee。
        
    -   测试：Lasna 测试网 + mock Uniswap Pair 。
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->

Uniswap V2 池与合约理解

-   学习目标：
    
    -   掌握 Uniswap V2 流动性池结构、交换机制与事件。
        
    -   理解恒定乘积公式及价格计算。
        
    -   为 Reactive 合约订阅 Uniswap 事件（如 Sync/Swap）奠基，实现 DeFi 自动化（止损、套利、再平衡）。
        
-   Uniswap V2 核心机制：
    
    -   流动性池：每个 token pair 一个 Pair 合约，储备 token0/token1。
        
    -   恒定乘积公式：x × y = k（k 不变，忽略 fee 简化）；实际含 0.3% fee → k 略增。
        
    -   Swap 执行：
        
        -   输入 amountIn → 计算 amountOut = (reserveOut × amountIn × 997) / (reserveIn × 1000 + amountIn × 997)。
            
        -   更新储备 → emit Sync(reserve0, reserve1)。
            
    -   关键事件：
        
        -   Sync(uint112 reserve0, uint112 reserve1)：储备更新，常用于价格监控。
            
        -   Swap(...)：记录输入/输出金额。
            
    -   其他函数：getReserves()、mint()（加流动性）、burn()（移除）。
        
-   Reactive 集成要点：
    
    -   订阅 Pair 的 Sync 事件：topics\[0\] = keccak256("Sync(uint112,uint112)")。
        
    -   在 react(LogRecord) 中：解码 reserves → 计算价格（reserve1 / reserve0 或反之）→ 阈值比较 → 条件满足 emit Callback 执行 swap/转移。
        
    -   示例：Sync 触发 → 价格 < 止损阈值 → Callback 调用 swap + 转出资金。
        
-   一句话总结：Uniswap V2 以恒定乘积 + Sync 事件为核心，提供实时价格/流动性数据；Reactive 通过订阅 Sync 实现 DEX 自动化逻辑。
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->


How Uniswap Works（Uniswap V2 池与合约理解）

-   学习目标：
    
    -   掌握 Uniswap V2 核心机制：恒定乘积公式、流动性池、Swap/Sync 事件。
        
    -   理解关键函数与事件：swap()、mint()、burn()、sync()。
        
    -   为 Reactive 合约订阅 Uniswap 事件（如 Sync/Swap）做准备，实现止损/套利等自动化。
        
-   Uniswap V2 核心概念：
    
    -   恒定乘积公式：x \* y = k（储备量 x、y 不变乘积）。
        
    -   流动性提供：add liquidity → mint LP token；remove → burn LP。
        
    -   Swap 过程：输入 token → 计算输出（含 0.3% fee）→ 更新储备 → emit Sync。
        
    -   关键事件：
        
        -   Swap(address indexed sender, uint amount0In, uint amount1In, uint amount0Out, uint amount1Out, address indexed to)
            
        -   Sync(uint112 reserve0, uint112 reserve1)（储备更新，常用于价格监控）。
            
    -   Pair 合约：每个 token pair 一个池，地址通过 Factory 计算。
        
-   与 Reactive 集成要点：
    
    -   订阅 Uniswap Pair 的 Sync 事件（topic\_0 = keccak256("Sync(uint112,uint112)")）。
        
    -   在 react() 中：解码储备 → 计算当前价格（reserve1/reserve0）→ 比较阈值 → 若触发止损/套利 → emit Callback 执行 swap 或转移。
        
    -   示例场景（Stop Order demo）：监控 Sync → 价格跌破阈值 → 自动 swap token → 转出资金。
        
-   代码示例要点（Uniswap V2 Pair 接口简化）：
    
    solidity
    
    ```solidity
    interface IUniswapV2Pair {
        event Sync(uint112 reserve0, uint112 reserve1);
        function getReserves() external view returns (uint112 reserve0, uint112 reserve1, uint32 blockTimestampLast);
        function swap(uint amount0Out, uint amount1Out, address to, bytes calldata data) external;
    }
    ```
    
    Reactive 订阅：topics\[0\] = keccak256("Sync(uint112,uint112)"); topics\[1/2\] 可忽略或指定。
    

一句话总结：Uniswap V2 通过恒定乘积 + Sync/Swap 事件提供价格与流动性基础，Reactive 合约订阅这些事件实现 DEX 自动化。
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->



How Oracles Work

-   学习目标：
    
    -   理解 oracle 桥接区块链与现实世界数据的角色。
        
    -   解决 oracle problem（如何信任地将 off-chain 数据引入链上）。
        
    -   掌握 oracle 在智能合约中的集成（以 Chainlink 为例）。
        
    -   认识 Reactive Contracts + oracle 的协同优势：实现 off-chain 事件的实时 on-chain 响应。
        
-   核心概念：
    
    -   Oracle 作用：从外部源（API、金融市场、政府数据库、IoT 等）获取数据，验证后以去中心化方式 relay 到链上。
        
    -   Oracle Problem：区块链确定性环境无法直接访问外部数据；需信任最小化解决。
        
    -   机制：多源聚合 + Multisig 协议（多签名验证，防止单点操纵）；主流提供者：Chainlink、Band Protocol。
        
-   与 Reactive Contracts 集成：
    
    -   Reactive 合约订阅 oracle 合约的事件（如 Chainlink 的 AnswerUpdated）。
        
    -   数据更新 emit → ReactVM 接收日志 → 评估条件 → 自动触发回调（e.g. 价格阈值 → DeFi 动作）。
        
    -   优势：克服传统合约需外部轮询的局限，实现 Inversion of Control + 实时响应 off-chain 事件。
        
-   代码示例（Chainlink 价格喂价）：
    
    solidity
    
    ```solidity
    pragma solidity ^0.8.0;
    import "@chainlink/contracts/src/v0.6/interfaces/AggregatorV3Interface.sol";
    
    contract PriceConsumerV3 {
        AggregatorV3Interface internal priceFeed;
        constructor() { priceFeed = AggregatorV3Interface(0x...); }  // ETH/USD feed
        function getLatestPrice() public view returns (int) {
            (,int price,,,) = priceFeed.latestRoundData();
            return price;
        }
    }
    ```
    
    Reactive 版：订阅事件，回调中比较价格并执行自动化逻辑。
    
-   实际应用：
    
    -   DeFi：价格喂价驱动借贷利率、清算、swap。
        
    -   保险：自然灾害等事件触发自动赔付。
        
    -   在线投注：体育结果上链结算。
        

一句话总结：Oracle 为 Reactive 提供 off-chain “感知”，事件驱动 + oracle 集成 = 连接现实世界的自治合约。打卡心得（简版）：
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->




How Subscriptions Work（订阅机制详解）

**1\. 学习目标（Lesson Objectives）**

-   理解订阅（Subscriptions）是 Reactive 合约实现事件监听的核心机制。
    
-   掌握订阅的设置、参数构造、管理与生命周期。
    
-   学会如何通过订阅将外部事件转化为合约的自治执行。
    
-   认识到订阅在 dual-state 环境（ReactVM vs Reactive Network）中的协调作用。
    

**2\. 订阅机制核心概念**

-   订阅定义：Reactive 合约在部署或运行时，向 Reactive Network 注册对特定事件的持续监控。事件发生 → Network 中继日志 → ReactVM 执行回调。
    
-   订阅参数（关键字段）：
    
    -   chainId：源事件所在链的 ID（支持多链，包括 Ethereum、Base、Lasna 等）。
        
    -   contract：源合约地址（发出事件的合约）。
        
    -   topics：事件签名哈希数组（topics\[0\] 为 event signature keccak256，topics\[1..\] 为 indexed 参数）。
        
    -   可选：fromBlock / toBlock（历史回溯范围）、过滤条件。
        
-   订阅类型：
    
    -   静态订阅：部署时硬编码（常见于简单场景）。
        
    -   动态订阅：通过用户调用函数动态添加/移除/更新（灵活，支持治理或用户自定义）。
        
-   生命周期：
    
    -   创建（subscribe）：gas 消耗，注册到 Network。
        
    -   激活：事件匹配后推送至 ReactVM。
        
    -   取消（unsubscribe）：移除监控，释放资源。
        
    -   管理：查询订阅状态、更新过滤器。
        

**3\. 订阅在 Dual-State 中的协调**

-   Reactive Network：订阅注册与管理发生在此环境（持久存储订阅元数据）。
    
-   ReactVM：接收订阅匹配的事件日志，解码为 Solidity 参数，传入回调函数执行。
    
-   数据流：外部合约 emit Event → Network 检测匹配订阅 → 推送日志数据 → ReactVM 调用合约回调 → 若需更新主状态 → 发起回调 tx 回 Reactive Network。
    
-   关键：订阅桥接了“感知”（事件监听）与“行动”（回调执行），是 Reactive 自治的核心原语。
    

**4\. 实际开发要点（从文档推导）**

-   继承 AbstractReactive 或类似基合约，获得订阅接口。
    
-   使用 Solidity 库编码 topics（e.g. keccak256(abi.encodePacked("EventSig(address,uint256)"))）。
    
-   测试：Lasna 测试网 + 水龙头 lREACT，部署后通过 explorer 查看订阅状态。
    
-   注意事项：订阅 gas 成本、topics 精确匹配（indexed 参数顺序）、多链订阅的 chainId 兼容。
    

**5\. 关键 takeaway**

订阅机制将传统的事件监听从 off-chain（The Graph、bots）迁移到 on-chain 原语，实现低延迟、无信任的自动化响应。掌握订阅参数构造是编写实用 Reactive 合约的第一步。今日心得（打卡可用）： 第四课聚焦订阅作为 Reactive 合约的“感官系统”，详细阐述了参数构造、动态管理与 dual-state 协作。订阅机制是连接外部世界与自治执行的桥梁，理解它后即可开始从概念转向实际编码。相比前三课的架构基础，本课提供了最接近开发的接口视角，为后续 oracle 集成与完整合约编写铺路。如何利用 Claude Code 开发 Reactive Contracts（实用指南）Claude Code（Claude 3.5/3.7/4 系列的代码模式，或 Projects + Artifacts 功能）在 Reactive 合约开发中优势明显：Solidity 逻辑模式重复（订阅 + 回调 + 条件判断），Claude 擅长生成 boilerplate、调试 topics 编码、优化 gas，并能基于官方文档迭代。推荐开发流程（2026 年最佳实践）：

1.  准备阶段：
    
    -   打开 [Claude.ai](http://Claude.ai) 或 [Claude.dev](http://Claude.dev)，创建新 Project “Reactive Contract \[你的项目名\]”。
        
    -   上传关键资料：
        
        -   Reactive dev 文档 PDF 或截图（尤其是 AbstractReactive 接口、订阅函数签名）。
            
        -   目标用例描述（e.g. “订阅 Uniswap V3 swap 事件，当价格超过阈值时自动触发跨链止损”）。
            
        -   Solidity ^0.8.20 版本规范。
            
2.  迭代生成代码（Claude Code 核心提示模板）： 用以下 prompt 结构反复对话：
    
    ```text
    你现在是 Reactive Network 专家 Solidity 开发者。
    目标：编写一个 Reactive 合约，继承 AbstractReactive，实现以下功能：
    - 订阅 [链ID: 1] 上 Uniswap V3 Pool 合约的 Swap 事件（topics 精确编码）。
    - 回调函数中：解码 amount0Out/amount1Out，计算价格。
    - 如果价格 > 阈值（可配置），发起回调：调用目标合约的 executeStopLoss()。
    要求：
    - 使用 Solidity 0.8.20
    - 处理 dual-state：ReactVM 中只读/计算，Network 中写状态
    - 包含 subscribe/unsubscribe 函数
    - gas 优化注释
    - 完整可编译代码 + 部署脚本注释
    
    参考文档：[粘贴 Lesson 4 订阅部分关键描述，或官网链接]
    
    先输出合约结构大纲，然后逐步生成完整代码。
    ```
    
3.  常见 Claude 擅长环节：
    
    -   Topics 编码：让 Claude 生成 bytes32\[\] memory topics = new bytes32\[\](3); topics\[0\] = keccak256("Swap(...)"); 并验证。
        
    -   事件解码：自动写 abi.decode([log.data](http://log.data), (int256 amount0, ...))。
        
    -   条件逻辑：复杂 if-then 自动生成并优化。
        
    -   测试用例：生成 Foundry 测试脚本（订阅 mock 事件、断言回调触发）。
        
    -   调试：贴编译错误或 gas 超限，Claude 快速修复。
        
4.  验证与部署：
    
    -   用 Remix 或 Hardhat 编译 Claude 生成的代码。
        
    -   Lasna 测试网部署（RPC + 水龙头已在前笔记）。
        
    -   用 ReactScan explorer 验证订阅注册成功、事件触发后回调执行。
        
    -   如遇问题，回 Claude 贴 tx hash / revert reason，继续迭代。
        
5.  效率提示：
    
    -   用 Claude 的 Artifacts 功能实时预览/编辑合约。
        
    -   保持单次 prompt 专注一个模块（订阅设置 → 回调逻辑 → 配置管理）。
        
    -   结合 ReacDEFI no-code 工具验证思路，再用 Claude 写底层代码。
        

利用 Claude Code，你可以把传统 3–5 天的手写 Reactive 合约压缩到 1–2 小时（概念验证级），尤其适合快速原型、学习与 hackathon。后续 Lesson 5（oracles）集成时，这个流程会更强大。
<!-- DAILY_CHECKIN_2026-03-12_END -->

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
