---
timezone: UTC+8
---

# Tofu

**GitHub ID:** tofudfy

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-21
<!-- DAILY_CHECKIN_2026-03-21_START -->
## **Reactive Network 潜在方向与生态现状**

| 方向 | 具体描述 | 已有实践（项目 + 链接） | 链接内公开的挑战 / 局限 |
| --- | --- | --- | --- |
| 自动化 DeFi 策略 / 资产管理 | 自动止盈止损、收益优化、策略执行 | Reactor（no-code automation）https://cryptoslate.com/press-releases/reactor-launches-on-reactive-network-pioneering-the-future-of-no-code-defi-automation/ | 需要用户定义触发条件；复杂策略设计门槛仍存在；自动化依赖链上事件触发，执行粒度受限 |
| 自动套利 / MEV 系统 | 跨链套利、价格差捕获、自动交易执行 | Flash Profit Extractorhttps://reactive.network/ecosystem | 依赖价格事件触发，存在延迟；无法完全避免外部竞争（链外 bot）；执行顺序受链上确认影响 |
| 跨链交易 / Gasless Swap | 无 gas 费用跨链交易、自动撮合 | DexTrade（gasless swap）https://reactive.network/ecosystem | 跨链交易仍依赖事件同步；执行路径复杂；用户体验与多链状态管理仍是挑战 |
| 自动化清算 / 风险管理 | 自动 liquidation、抵押监控、风险控制 | Reactive 自动执行逻辑https://www.gate.com/learn/articles/what-is-reactive-network-react/7309 | 依赖外部价格数据；链上事件触发存在延迟；极端市场条件下执行可靠性问题 |
| 跨链借贷 / 流动性整合 | 多链抵押借贷、统一流动性市场 | ReactiveFlow Lender（生态项目）https://reactive.network/ecosystem | 不同链资产标准不统一；跨链状态同步复杂；风险评估模型尚未成熟 |
| Flash Loan / 组合金融 | 无锁闪电贷、组合交易执行 | Non-locking FlashLoanhttps://reactive.network/ecosystem | 安全性依赖协议设计；组合交易复杂性高；跨链执行一致性问题 |
| NFT / 动态资产 | 动态 NFT、收益自动分配、链上逻辑触发 | Dynamic NFT Royalty Systemhttps://www.gate.com/learn/articles/what-is-reactive-network-react/7309 | NFT 状态跨链同步复杂；执行成本（gas）较高；逻辑复杂度限制应用范围 |
| DAO / 治理自动执行 | 投票触发执行、资金管理自动化 | Reactive governance automationhttps://reactive.network | 自动执行不可回滚；治理错误风险放大；跨链治理一致性问题 |
| RWA / 现实资产 | 房地产、债券、票据等资产上链并自动结算 | RWA automation use caseshttps://wealthandfinance.digital/real-world-assets-on-the-blockchain-how-reactive-network-is-transforming-financial-markets/ | 法律与链上执行不完全一致；数据真实性依赖外部输入；合规问题仍未解决 |
| AI + DeFi 自动系统 | AI agent 自动交易、策略执行 | AI-driven DeFi automationhttps://www.gate.com/learn/articles/what-is-reactive-network-react/7309 | AI 决策不可验证；策略透明性不足；链上执行与 AI 决策之间存在信任问题 |
<!-- DAILY_CHECKIN_2026-03-21_END -->

# 2026-03-20
<!-- DAILY_CHECKIN_2026-03-20_START -->

**Reactive Contract 不是单实例、不是用户主动调用、也不是单链事件监听器** 这三个前提。Reactive Contract 部署后会同时存在于 **Reactive Network（RNK）** 和 **私有 ReactVM（RVM）** 两个环境里，两份实例**代码相同但状态隔离**；RNK 侧主要负责订阅和管理，RVM 侧主要负责接收事件并执行 react(LogRecord) 逻辑，再通过 Callback 事件把结果送到目标链。这个双状态、事件驱动、跨链回调的执行模型，是前端、后端、合约三部分全部设计的起点。  
  
对前端来说，最重要的不是页面本身，而是**链路可观测性**。Lasna Testnet 的链 ID 是 5318007，系统合约地址在 Mainnet 和 Lasna 都是固定的 0x0000000000000000000000000000000000fffFfF。Reactscan 也明确把“我的 RVM Address”作为一个单独观察入口，且文档说明它应与部署地址映射相关联；这正好对应你要把“部署地址 → RVM 地址 → reactive 交易状态”回传给前端或日志。

对后端来说，最有用的几个 **RNK 专用 JSON-RPC**是：

-   rnk\_getRnkAddressMapping：把 RNK 合约地址映射到 RVM ID；
    
-   rnk\_getVm / rnk\_getVms / rnk\_getStat：看 RVM 活跃情况；
    
-   rnk\_getSubscribers 和 rnk\_getFilters：看订阅是否真的注册成功；
    
-   rnk\_getTransactionByHash / ByNumber：查某次 Reactive 交易是否执行、由哪个 Origin 交易触发、状态是否成功；
    
-   rnk\_call / rnk\_getCode / rnk\_getStorageAt：做模拟调用、代码和状态排错。
    

这些接口正好支撑“后端统一监听 + 结构化返回 + 调试信息回传”。

对智能合约来说，最关键的是三个接口面：

-   第一，订阅靠 ISubscriptionService.subscribe(chain\_id, contract, topic\_0, topic\_1, topic\_2, topic\_3)；
    
-   第二，事件到来后，Reactive Network 会调用 react(LogRecord calldata log)，而 LogRecord 里除了 chain\_id、\_contract、topic\_0...topic\_3、data，还带 block\_number、op\_code、block\_hash、tx\_hash、log\_index；
    
-   第三，跨链动作不是直接发交易，而是 emit Callback(chain\_id, _contract, gas_limit, payload)，由网络把回调提交到目标链。 
    

第一个难点是 **双状态不是概念题，而是代码组织题**。 Reactive Contract 会在 RNK 与 RVM 两边各有一份实例，状态不共享；同时 AbstractReactive 通过 detectVm() 检查系统合约地址 0x...fffFfF 是否有代码来判断当前运行环境，并提供 rnOnly / vmOnly 两个执行上下文修饰器。如果没有把“网络侧变量”和“RVM 侧变量”分开设计，就会很容易写出构造正常、运行错位的代码。

第二个难点是 **constructor 里订阅要“只在 RNK 侧做”**。官方反复强调，订阅通常在 constructor 中调用 subscribe() 完成，但因为同一份字节码也会部署到 RVM，而 RVM 里并不存在系统合约，所以构造函数必须避免在 ReactVM 里调用 subscribe()；示例代码就是 if (!vm) { service.subscribe(...) }。这正好对应“在 constructor 里调用系统合约 subscribe(…) 建立订阅”——这句话要补上一半：**只能在 !vm 时调**。

第三个难点是 **订阅过滤是等值匹配，不是模糊检索**。文档给出的过滤维度就是 chain ID + contract address + topics 0~3，并支持用 REACTIVE\_IGNORE 作为通配；因此要满足“事件签名稳定、参数里至少包含一个 indexed”的要求，因为只有进入 topic 的内容，订阅层才能直接高效过滤。若 Origin 事件字段没进 topic，而全放进 data，那就只能在 react(LogRecord) 里做二次解析和判断。

第四个难点是 **回调入口的第一个参数必须是 address**。文档在 Events & Callbacks 和 Debugging 两处都写得很明确，Reactive Network 会自动把回调 payload 的前 160 bits 替换为 RVM ID（即部署者地址），因此回调函数的第一个参数必须预留为地址类型；漏掉它，调用就会失败。

第五个难点是 **在 RVM 里只能“看日志、做判断、发回调”，不能直接连外部世界**。官方文档明确说 ReactVM 内部不能访问外部 RPC、不能连 off-chain 服务；它能做的是接收日志、执行 Solidity 逻辑、然后请求 RNK 把 callback 发去目标链。所以后端的 SSE/WebSocket、统一事件结构、调试聚合，必须放在链外服务层，不要指望在 Reactive 合约里完成。

第六个难点是 **资金与活跃状态**。Reactive Contract 在 RVM 执行前要先有 REACT 余额；callback 合约在目标链侧也要有足够资金，否则会因债务或余额问题变成 inactive。文档还给了两个容易踩坑的参数，RVM 交易最大 gas limit 是 900,000，callback 最低 gas limit 是 100,000，低于这个最小值会被忽略。没触发最后不是订阅问题，而是资金或 gas 约束没满足。
<!-- DAILY_CHECKIN_2026-03-20_END -->

# 2026-03-19
<!-- DAILY_CHECKIN_2026-03-19_START -->


Reactive Network 经济模型总结

一、整体概念

Reactive Network 的经济模型是一种“先执行、后付费”的机制。

合约在 Reactive VM（RVM）中执行时不需要立即支付 gas，而是在执行后累积费用（debt），再由合约或用户后续统一偿还。

本质上是一个基于债务（debt-based）的执行与结算体系。

二、执行与计费分离

1.  执行环境分为两类：
    

-   RVM（Reactive VM）：用于响应事件执行 reactive contract，不即时收费
    
-   RNK（普通交易）：类似传统 EVM，正常 gas 计费
    

2.  执行（execution）与付费（payment）解耦
    

执行发生时：

-   不立即扣费
    
-   记录 gas 使用量
    

后续：

-   根据 BaseFee × GasUsed 计算费用
    
-   在后续区块统一结算
    

→ 费用不是 per-transaction 精确绑定，而是延迟聚合计算

三、债务模型（核心机制）

1.  执行产生 debt：
    
    每次 reactive execution 都会产生未支付费用
    
2.  偿还方式：
    

-   手动：调用 coverDebt() 进行偿还
    
-   自动：通过 depositTo() 或 pay() 自动扣除
    

3.  惩罚机制：
    

-   若长期不偿还 debt
    
-   合约会被 blocklist（禁止继续执行）
    

→ 执行权限由“是否偿债”决定

四、跨链回调（callback）与定价

Reactive Network 的跨链能力通过 callback 实现：

-   Reactive chain 触发 → 在目标链执行
    

callback 的费用为：p\_callback = p\_base × C × (g\_callback + K)，其中：

-   p\_base：基础 gas 价格
    
-   C：目标链系数
    
-   g\_callback：回调执行 gas
    
-   K：固定开销
    

→ 跨链执行是显式定价的，不是隐式成本

五、自动支付机制

在 callback 执行时：

-   系统可以自动调用支付函数
    
-   自动偿还对应 debt
    

→ 执行逻辑与支付逻辑是耦合的

执行约束：

-   callback 有最低 gas 限制（如 100,000）
    
-   确保跨链调用具备最小可执行性
    

六、与传统区块链模型的区别

对比 EVM：

1.  付费方式
    

-   EVM：交易前支付（upfront）
    
-   Reactive：执行后支付（post-factum）
    

2.  gas 计费
    

-   EVM：逐交易计费
    
-   Reactive：延迟聚合计费
    

3.  执行触发
    

-   EVM：用户主动触发
    
-   Reactive：事件驱动触发
    

4.  支付责任
    

-   EVM：交易发送者
    
-   Reactive：合约承担
    

→ Reactive 将“执行责任”从用户转移到合约

* * *

总结

Reactive Network 的经济模型可以抽象为：

1.  延迟计费（Deferred Fee）
    
2.  债务驱动执行（Debt-based Execution）
    
3.  自动结算（Auto Settlement）
    
4.  执行与支付耦合（Execution-Payment Coupling）
<!-- DAILY_CHECKIN_2026-03-19_END -->

# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->




第三阶段进展汇报（初步实践）

本阶段主要开始上手 Reactive 跨链机制的实际开发，目标是打通基础链路并熟悉整体流程。

前端方面，已完成钱包连接，并能够在 Origin 链与 Lasna（Reactive）之间切换。目前可以通过前端触发 Origin 合约事件，正在尝试搭建简单的跨链状态展示。

后端方面，初步实现了对链上事件的监听（以 Origin 为主），并在尝试将事件结构化，作为后续跨链处理与前端展示的基础。

智能合约方面，完成了基础事件合约（emit Event）的编写，并在学习 Reactive 合约的订阅与 react(LogRecord) 处理逻辑，逐步理解其执行机制。

整体仍处于熟悉框架与打通最小流程阶段，下一步将重点放在链路联通（Origin → Reactive）以及简单的端到端验证上。
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->





Reactive Network 学习总结

一、学习主题与目标

\------------------------------------------------

结合：

Reactive Dev Docs

[https://dev.reactive.network/](https://dev.reactive.network/)

Reactive Smart Contract Demos

[https://github.com/Reactive-Network/reactive-smart-contract-demos](https://github.com/Reactive-Network/reactive-smart-contract-demos)

Uniswap Stop Order 示例

[https://github.com/Reactive-Network/reactive-smart-contract-demos/tree/main/src/demos/uniswap-v2-stop-order](https://github.com/Reactive-Network/reactive-smart-contract-demos/tree/main/src/demos/uniswap-v2-stop-order)

重点理解：

1 Reactive contract 如何定义事件订阅

2 ReactVM 如何检测并触发事件

3 Trigger 逻辑如何过滤事件

4 Callback 如何执行链上操作

5 Reactive Node 如何连接多链状态

核心问题：

Reactive contract 与传统 EVM contract 的本质区别是什么？

二、Reactive 的核心设计思想

\------------------------------------------------

Reactive Network 的设计目标是：

让智能合约从“被调用执行”变为“自动触发执行”。

传统 EVM 模型：User Transaction → Contract Execution

Reactive 模型：External Event → Trigger Condition → Contract Execution

Reactive contract 本质上是一个事件驱动自动化程序。

三、Subscription：事件订阅机制

\------------------------------------------------

Subscription 是 Reactive contract 的第一层逻辑，其作用是定义需要监听的链上事件。

Reactive Network 允许订阅以下信息：

1 chain

2 contract address

3 event signature

4 topic filter

5 event parameters

Subscription 的作用类似：区块链上的 “event indexer + filter”。

Subscription 的一般结构：

Subscription {

chain

contract

event

filter

}

例如：

监听 Uniswap V2 Swap 事件：

Swap(

address sender,

uint amount0In,

uint amount1In,

uint amount0Out,

uint amount1Out,

address to

)

Reactive Node 会持续扫描新区块：Block → Log → Event → Match Subscription

匹配成功后：Event → Trigger evaluation

四、Trigger：事件触发条件

\------------------------------------------------

Subscription 仅表示监听事件，但并不是所有事件都需要触发 callback。因此需要 Trigger，即对事件进行逻辑判断。

Trigger 的输入：

1 event parameters

2 contract state

3 reactive state

Trigger 的典型形式：

if condition(event) == true

execute callback

例如 Stop Order 场景：

Swap Event -> Calculate Price -> Price < StopPrice ? -> Trigger callback

Trigger 可以依赖：

1 event data

2 external chain state

3 reactive contract internal state

Trigger 的执行由 ReactVM 负责。

五、Callback：自动执行逻辑

\------------------------------------------------

Callback 是 Reactive contract 的执行部分。

当 Trigger 满足时，ReactVM 调用 Callback，执行：

1 on-chain transaction

2 contract call

3 cross-chain operation

Callback 可以调用：DEX、ERC20、Bridge、Arbitrage contract等等

六、ReactVM 的执行模型

\------------------------------------------------

ReactVM 是 Reactive Network 的执行环境。

其作用类似：

EVM → transaction execution

ReactVM → event-driven execution

ReactVM 的主要模块：

1 Event Listener

2 Subscription Matcher

3 Trigger Engine

4 Callback Executor

执行流程：

New Block

|

Extract Logs

|

Subscription Match

|

Trigger Evaluation

|

Callback Execution

ReactVM 的关键特点：

1 非交易驱动

2 自动执行

3 支持多链监听

七、ReactVM 的双状态模型

\------------------------------------------------

Reactive Network 使用Dual State Model，即Reactive State (contract data) + External State (blockchain data)

External State 来源：Ethereum、Arbitrum、BNB Chain、Polygon等等

Reactive State 包括：

1 contract parameters

2 trigger thresholds

3 order status

4 user configuration

Trigger 计算时需要同时读取两种状态。

八、Reactive 模型与传统 DeFi 的差异

\------------------------------------------------

传统 DeFi：User Bot → Monitor Events → Send Transaction

Reactive DeFi：Reactive Node → Monitor Events → Auto Execute

传统模式：Off-chain bot，为中心化

Reactive 模式：Protocol-level automation，实现去中心化
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->






Reactive Network 学习总结

第二阶段 Day 1

主题：Reactive 技术结构与开发模型

\==================================================

一、Reactive 技术的核心目标

\==================================================

Reactive Network 的目标是：

让智能合约具备“自动响应能力”。

传统 EVM 合约：

事件发生

↓

用户 / Bot 发送交易

↓

合约执行

Reactive 模型：

事件发生

↓

Reactive Network 监听

↓

自动触发 Reactive Contract

↓

执行 callback

核心思想：

Event → Trigger → Callback

\==================================================

二、Reactive Contract 的基本结构

\==================================================

Reactive Contract 是一种特殊的智能合约，

其核心功能是：订阅事件并自动执行逻辑。

基本组成：

+-------------------+

| Reactive Contract |

+-------------------+

| subscribe() | 订阅链上事件

| trigger() | 事件触发逻辑

| callback() | 执行响应操作

+-------------------+

结构流程：

subscribe

│

▼

链上事件发生

│

▼

trigger

│

▼

callback

\==================================================

三、Reactive 的开发模型

\==================================================

Reactive 开发模式核心为：

Subscribe / Trigger / Callback

三个组件职责如下：

+-----------+--------------------------------------+

| 模块 | 作用 |

+-----------+--------------------------------------+

| Subscribe | 订阅链上事件 |

| Trigger | 判断是否触发 Reactive Contract |

| Callback | 执行自动化逻辑 |

+-----------+--------------------------------------+

\==================================================

四、ReactVM 执行逻辑

\==================================================

Reactive Network 引入 ReactVM，

用于执行 Reactive Contract。

ReactVM 的核心特点：

双状态执行模型

ReactVM State Model：

+-------------------+----------------------------------+

| 状态类型 | 作用 |

+-------------------+----------------------------------+

| Reactive State | 存储订阅关系 |

| Execution State | 执行 callback |

+-------------------+----------------------------------+

执行流程：

Event

│

▼

Reactive State

│

▼

Trigger Check

│

▼

Execution State

│

▼

Callback Execution

\==================================================

五、Reactive 的跨链机制

\==================================================

Reactive Network 支持跨链自动执行。

基本流程：

Chain A 事件触发

│

▼

Reactive Network 监听

│

▼

Reactive Contract Callback

│

▼

Chain B 执行交易

\==================================================

六、Reactive Library

\==================================================

Reactive Library 提供开发接口：

主要组件：

+--------------------+--------------------------+

| 组件 | 功能 |

+--------------------+--------------------------+

| subscription API | 订阅事件 |

| callback API | 回调执行 |

| trigger interface | 触发逻辑 |

+--------------------+--------------------------+

开发者通常通过Reactive Library，实现 Subscribe / Callback。

\==================================================

七、Reactive 的自动化能力

\==================================================

Reactive Network 可以实现：

无需 Bot 的自动化执行。

传统 DeFi 自动化：

用户

↓

Bot

↓

合约

Reactive 模型：

用户

↓

Reactive Contract

↓

自动执行

\==================================================

八、Uniswap Stop Order 示例理解

\==================================================

Stop Order 示例说明：

Reactive 可以实现自动交易。

流程示意：

Uniswap Price Event

│

▼

Reactive Subscribe

│

▼

Trigger Check

│

▼

Execute Swap
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->







在本次作业中，我主要围绕 Reactive Network 的跨链事件与回调机制进行了开发实践。首先梳理了系统整体架构，理解了 **Origin Contract、Reactive Contract 和 Destination Contract** 在跨链事件流程中的角色划分。Origin Contract 部署在源链上，用于触发事件；Reactive Contract 运行在 Reactive Network 上，用于监听指定事件并执行响应逻辑；Destination Contract 部署在目标链上，用于接收回调交易并执行最终操作。

在开发过程中，我重点学习了 Reactive Smart Contract 的基本结构以及事件监听与回调的触发方式，并理解了 Reactive Network 的事件驱动执行模式。相比传统依赖 off-chain bot 监听事件再手动发送交易的方式，Reactive Network 将事件监听和自动执行逻辑通过合约形式进行编程，实现了更自动化的跨链响应机制。通过这一过程，我对事件驱动智能合约以及跨链自动化执行的设计思路有了更直观的认识。

在实际操作中，我完成了合约结构设计以及事件触发逻辑的实现，并尝试配置 Reactive Contract 对指定链上事件进行监听，同时设置回调目标合约。在部署和调试过程中，我也对 Reactive Network 的开发环境、测试网资源以及跨链回调流程有了初步了解。

目前已经完成了整体架构理解和主要开发步骤的实现，但三类合约的完整联调与跨链回调的最终验证仍在进行中，后续计划进一步完成部署测试并验证从源链事件触发到目标链回调执行的完整流程。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->








\# Reactive Network 学习总结

本阶段重点关注 Reactive Network 的 \*\*运行机制\*\*：即链上事件如何被监听、`react()` 函数如何执行，以及 callback 交易是如何生成的。

\---

\## 1. Reactive 核心执行流程

Reactive Network 的\*\*详细执行步骤：\*\*

1\. \*\*事件触发\*\*：某条源链上的智能合约产生并发出事件（event log）。

2\. \*\*链上监听\*\*：Reactive Network 的节点捕获到该事件。

3\. \*\*VM 调用\*\*：ReactVM 启动，调用对应 reactive contract 的 `react()` 函数。

4\. \*\*逻辑计算\*\*`react()` 函数根据接收到的事件内容执行预设的响应逻辑。

5\. \*\*交易构造\*\*：Reactive Network 根据执行结果构建 callback 交易。

6\. \*\*跨链执行\*\*：callback 交易被发送到目标链并执行。

\---

\## 2. ReactVM 的核心作用

ReactVM 是 Reactive Network 的执行环境，其核心职责包括：

\- \*\*解析链上事件（Log）\*\*

\- \*\*调用 Reactive Contract\*\*

\- \*\*执行 `react()` 函数逻辑\*\*

\- \*\*构造 Callback Transaction\*\*

可以将 ReactVM 理解为以下三个组件的结合体：

| 组件 | 功能 |

| :--------------- | :--------------------------------- |

| \*\*事件解析器\*\* | 解析从链上监听到的原始事件数据。 |

| \*\*合约执行环境\*\* | 安全地执行 reactive contract 的业务逻辑。 |

| \*\*交易生成器\*\* | 根据执行结果生成最终的 callback 交易。 |

因此，ReactVM 本质上是一个 \*\*事件驱动的、专门用于执行合约逻辑的轻量级运行时（Runtime）\*\*。

\---

\## 3. Callback Transaction 详解

\*\*Callback Transaction\*\* 是 Reactive Network 实现自动化交互的核心机制。

\*\*定义：\*\*

当 Reactive Network 检测到预先设定的链上事件后，由系统自动构造并发送到目标链的交易。

\*\*主要特点：\*\*

| 特性 | 说明 |

| :----------- | :--------------------------------------- |

| \*\*自动触发\*\* | 完全自动化，无需用户手动发送交易。 |

| \*\*跨链执行\*\* | 可以在与事件源链不同的另一条链上执行。 |

| \*\*可编程\*\* | 执行的具体逻辑完全由 `react()` 函数决定。 |

\*\*典型应用场景：\*\*

| 触发事件 | Callback 行为 |

| :------------------- | :-------------------------------- |

| DEX 发生大额 Swap | 在另一条链上执行套利交易。 |

| 借贷协议达到清算条件 | 自动执行 liquidation（清算）。 |

| 资产在源链被锁定 | 在目标链上触发等额资产的释放（跨链转移）。 |

\---

\## 4. Reactive Contract 生命周期

一个 Reactive Contract 的完整运行生命周期如下：

| 阶段 | 说明 |

| :------- | :----------------------------------------------- |

| \*\*1. 部署\*\* | 将写好的 reactive contract 部署到 Reactive Network 上。 |

| \*\*2. 监听\*\* | 合约通过代码指定需要监听的特定链、特定合约及特定事件。 |

| \*\*3. 触发\*\* | 当监听的链上事件发生时，系统自动触发该合约的 `react()` 函数。 |

| \*\*4. 执行\*\* | `react()` 函数执行，并根据逻辑生成 callback 交易。 |

| \*\*5. 完成\*\* | callback 交易被发送至目标链并成功执行，整个响应流程结束。 |

\*\*Reactive Contract 的核心接口：\*\*

\`\`\`solidity

react(LogRecord log)

\`\`\`

\- \*`LogRecord`\*\*：一个数据结构，封装了从源链监听到的完整事件信息。

\- \*`react()`\*\*：开发者实现的核心函数，在此定义系统对特定事件的响应逻辑。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->









# **Reactive Network 学习总结**

## **1\. Reactive Network 是什么**

**Reactive Network** 是一个用于事件驱动自动执行（event-driven automation）的区块链网络。

它通过监听链上事件，在 Reactive 网络中执行开发者编写的逻辑，并自动在目标链上触发交易。

开发者编写 **Reactive Contract**，Reactive 节点执行其中的 react() 逻辑。

## **2\. 工作流程**

Chain A

↓ 1. 事件产生（event log）

Reactive Network

↓ 2. 事件订阅触发

ReactVM

↓ 3. 执行 react(LogRecord)

生成 callback

↓

Chain B

↓ 4. 调用目标合约

完成自动执行

## **3\. Reactive Network 对比**

| 系统 | 核心定位 | 主要功能 | 触发方式 | 执行逻辑位置 | 信任模型 | 典型用途 |
| --- | --- | --- | --- | --- | --- | --- |
| Reactive Network | 自动化执行网络 | 监听事件并自动执行交易 | 链上事件订阅 | ReactVM | 信任 Reactive 网络 | 自动清算、套利、跨链触发 |
| Bot | 自动化程序 | 监听事件并发送交易 | 本地监听 | off-chain 程序 | 无需额外信任 | MEV、清算、套利 |
| LayerZero | 跨链消息协议 | 跨链消息传递 | 合约主动发送消息 | 目标链合约 | Oracle + Relayer | Omnichain 应用 |
| Chainlink | Oracle 网络 | 数据预言机 / 自动化 | 数据请求或条件触发 | Oracle 节点 | Oracle 网络 | 价格预言机、自动执行 |
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
