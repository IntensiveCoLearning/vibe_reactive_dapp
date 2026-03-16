---
timezone: UTC+8
---

# klizz

**GitHub ID:** klizz111

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->
## Economy

### 1\. 交易执行与计费模型

\* **RVM 交易 (Reactive Virtual Machine)**：

\* **后付费机制**：执行时不收取 Gas 费，费用在后续区块（通常是下一个）根据当时的 `BaseFee` 结算。

\* **计算公式**`费用 = BaseFee × GasUsed`。

\* **限制**：最大 Gas Limit 为 **900,000**。

\* **特性**：由于是区块级聚合记账，区块链浏览器（Reactscan）无法将费用关联到单笔 RVM 交易。

\* **RNK 交易 (Reactive Network Transactions)**：

\* 遵循标准的 EVM Gas 模型（即传统的先付费/即时扣费模式）。

### 2\. 合约状态与资金管理

\* **资金要求**：合约在执行 RVM 交易前必须持有足够的 **REACT** 代币。

\* **合约状态**：

\* **Active (活跃)**：正常执行。

\* **Inactive (非活跃)**：存在未结清的债务，需充值并调用 `coverDebt()` 偿还后才能恢复。

\* **充值方式**：

1\. **直接转账**：向合约地址发送 REACT，然后手动调用 `coverDebt()` 结算债务。

2\. **系统合约存款**：向系统合约 `0x0000...fffFfF`) 调用 `depositTo(address)`，可\*\*自动结算\*\*未结债务。

### 3\. 跨链回调 (Cross-Chain Callbacks)

\* **定价公式**：

$$p\_{callback} = p\_{base} \\times C \\times (g\_{callback} + K)$$

\* $p\_{base}$: 基础 Gas 价格。

\* $C$: 目标网络的定价系数。

\* $g\_{callback}$: 回调消耗的 Gas。

\* $K$: 固定 Gas 附加费。

\* **Gas 限制**：强制执行\*\*最小 100,000 Gas\*\* 的限制（低于此值的请求会被忽略），以确保内部审计和计算的安全。

\* **支付模式**：与 RVM 交易类似（后付费）。资金不足的合约会被列入黑名单，无法执行交易或回调。

\* **自动结算 (On-The-Spot)**：通过实现 `pay()` 函数或继承 `AbstractPayer`，可在回调产生债务时由代理合约自动调用并结算。

### 4\. 关键系统地址与查询

\* **系统合约 / 回调代理地址**`0x0000000000000000000000000000000000fffFfF` (在 Reactive Network 上两者共用此地址)。

\* **关键查询命令**：

\* **余额**：使用 `cast balance` 查询。

\* **债务 (Debt)**：调用系统/代理合约的 `debts(address)` 视图函数。

\* **储备金 (Reserves)**：调用系统/代理合约的 `reserves(address)` 视图函数。

**核心逻辑总结**：Reactive Network 采用“先执行，后算账”的模式来优化用户体验和跨链交互，但要求开发者必须密切关注合约的 **REACT 余额** 和 **未结债务**，一旦资不抵债，合约将立即停止服务直到债务被清偿。
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->

## Subscription 的工作原理

### 1\. 核心概念

\* **自动响应**：订阅允许智能合约在特定事件发生时，自动执行预定义的逻辑（通过 `react` 函数）。

\* **双环境架构**：订阅管理涉及两个环境：

\* **Reactive Network**：拥有系统合约，负责实际注册和管理订阅。

\* **ReactVM**：合约的本地副本，用于处理逻辑和触发回调，但无法直接访问系统合约。

### 2\. 如何实现订阅

\* **接口**：

\* `ISubscriptionService`：用于调用 `subscribe` 和 `unsubscribe` 方法。

\* `IReactive`：定义了接收通知的标准接口，包含 `react(LogRecord log)` 函数。

\* **订阅参数**：需要指定 `chain_id`（链ID）`_contract`（目标合约地址）以及最多4个 `topics`（事件主题）。

\* **初始化方式**：

\* **静态订阅**：通常在构造函数中完成。代码需判断当前是否在 ReactVM 环境中`if (!vm)`），只有在 Reactive Network 上才调用系统合约进行订阅。

\* **动态订阅**：合约可以在运行时根据接收到的事件动态添加或移除订阅（通常通过回调机制触发）。

### 3\. 订阅规则与最佳实践

\* **通配符使用**：

\* 使用 `address(0)` 代表任意合约地址。

\* 使用 `uint256(0)` 代表任意链 ID。

\* 使用 `REACTIVE_IGNORE` 代表任意事件主题。

\* **限制条件**：

\* 至少需要一个具体的过滤条件（不能全为通配符）。

\* **禁止**使用范围操作（如 `<`, `>`）或位运算，仅支持严格相等匹配。

\* **禁止**单次订阅覆盖所有链或所有合约的所有事件。

\* **重复订阅**是允许的（视为一个），但在系统合约层面去重成本高昂，因此由客户端自行确保幂等性。

### 4\. 事件处理流程 `react` 函数)

当订阅的事件被触发时，Reactive Network 会调用合约的 `react` 函数，传入 `LogRecord` 结构体，其中包含：

\* 事件来源（链ID、合约地址）。

\* 事件主题（Topics）和数据（Data）。

\* 区块和交易信息。

\* **回调机制**`react` 函数内部通常会根据事件类型构造 payload 并发出 `Callback` 事件，从而触发后续的业务逻辑（如在另一个链上执行操作）。

### 5\. 动态订阅示例

文章提供了一个“Approval Magic”演示案例，展示了合约如何监听特定的“订阅/取消订阅”事件，进而动态地管理对第三方合约（如 Uniswap）事件的订阅状态。这体现了 Reactive Contracts 的高度灵活性。

**总结**：订阅机制是 Reactive Network 实现跨链自动化和事件驱动架构的基石，开发者需正确配置过滤条件并处理好双环境下的逻辑分支。
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->


### **1\. 事件的本质**

-   **定义**：在区块链上下文中，事件是智能合约执行过程中产生的日志记录（Logs）。
    
-   **结构**：每个事件包含特定的数据字段，其中最重要的是 **Topic 0**（事件的唯一标识符/签名哈希），它决定了事件的类型（例如 `Transfer`、`Swap` 等）。
    
-   **作用**：事件是链上状态变化的“信号”，反应式合约正是通过监听这些信号来触发自动化逻辑。
    

### **2\. 监控与过滤机制**

Reactive Network 并不盲目处理所有链上数据，而是通过高效的过滤机制工作：

-   **基于 Topic 0 的订阅**：用户在创建反应式合约时，必须指定要监听的 **Topic 0**。网络节点只关注匹配该特定哈希值的事件。
    
-   **多链支持**：系统可以同时监控多条区块链（如 Ethereum, Arbitrum, Optimism 等）上的事件。
    
-   **精准捕获**：一旦目标链上发生了匹配 Topic 0 的交易，该事件会被立即捕获并传递给对应的反应式合约进行处理。
    

### **3\. 从事件到执行的流程**

文章描述了事件触发自动化操作的完整生命周期：

1.  **发生**：用户在某条链上发起交易（如在 Uniswap 上进行 Swap），合约 emit 一个事件。
    
2.  **捕获**：Reactive Network 的节点扫描新区块，发现匹配的 Topic 0。
    
3.  **验证与排序**：网络验证事件的有效性，并将其放入执行队列。
    
4.  **触发执行**：系统自动调用已部署的反应式合约，将事件数据作为输入参数传入。
    
5.  **动作**：反应式合约根据预设逻辑执行操作（如发起新的交易、更新状态等）。
    

### **4\. 关键优势**

-   **实时性**：相比传统的外部轮询（Polling）或中心化机器人，这种基于事件驱动的架构能更快速地响应链上变化。
    
-   **去中心化**：事件的监听和处理由 Reactive Network 的去中心化节点网络完成，无需依赖单一的可信第三方监控服务。
    
-   **灵活性**：开发者可以针对任意标准 ERC-20、ERC-721 或自定义合约的事件编写逻辑，只要知道其 Topic 0 即可。
    

### **总结**

**事件（Events）** 是 Reactive Network 的“感官”。通过监听特定的 **Topic 0**，反应式合约能够实时感知链上发生的任何相关活动，并立即做出反应。这种机制将原本被动的智能合约转变为能够主动响应环境变化的自动化代理，是实现无需人工干预的复杂 DeFi 策略（如止损、套利、再平衡）的技术基石。
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->



-   ReactVM: ReactiveNetwork中的核心组件，负责执行Reactive Contract，并发送回调信息到目标链
    
-   归属权：Reactive COntract会部署到基于同一个地址的EOA的独有的ReactVM，并且同一个ReactVM中的合约可以进行状态共享
    
-   每个合约具有两种状态：ReactVM，负责emit event；Reactive Network State，响应EOA的function call，Administrative actions such as `pause()` may exist in the Reactive Network state
    
-   ![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/klizz111/images/2026-03-13-1773405875112-image.png)
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->




-   参加了Workshop
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->






-   完成 **Reactive 挑战「第一关」**
    
-   了解了REACTIVE的调用和部署流程
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->







## Day 01

-   set up dev env at [https://github.com/klizz111/Reactive](https://github.com/klizz111/Reactive/pull/1)
    
-   looking through [https://dev.reactive.network/#step-1--reactive-basics](https://dev.reactive.network/#step-1--reactive-basics)
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
