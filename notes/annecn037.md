---
timezone: UTC+8
---

# annecn037

**GitHub ID:** annecn037

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
**Subscriptions** 是 Reactive Contracts 感知外部事件的注册机制，通过订阅特定链上日志或状态变化，使合约能够在无需人工干预的情况下自动触发响应；这一机制构成了 Reactive Network **跨链自动化能力**的核心纽带，实现了从**事件监听**到**自主执行**的无缝衔接。

## **一、核心概念与架构定位**

### **1.1 订阅的本质**

订阅（Subscription）是 Reactive Contract（RC）的**核心能力**，使其能够：

-   **被动监听**：自动响应其他合约发出的事件
    
-   **链间感知**：监听不同链（EIP155 标准链ID）上的事件
    
-   **条件触发**：基于特定事件主题（Topics）执行预定义逻辑
    

### **1.2 双环境部署特性**

Reactive Contract 的独特之处在于**同时部署于两个环境**：  

| 环境 | 特性 | 系统合约可用性 |
| --- | --- | --- |
| Reactive Network | 主网络，有完整状态 | ✅ 有系统合约 |
| ReactVM（私有） | 部署者的私有虚拟环境 | ❌ 无系统合约 |

  
**关键影响**：构造函数中必须通过 `if (!vm)` 条件判断，避免在 ReactVM 中调用订阅时 revert

* * *

## **二、核心接口详解**

### **2.1 ISubscriptionService —— 订阅服务接口**  

```
interface ISubscriptionService is IPayable {
    function subscribe(
        uint256 chain_id,      // 源链 EIP155 ID
        address _contract,     // 事件源合约地址
        uint256 topic_0,       // 事件主题0（通常事件签名哈希）
        uint256 topic_1,       // 索引参数1
        uint256 topic_2,       // 索引参数2
        uint256 topic_3        // 索引参数3
    ) external;
    
    function unsubscribe(/* 相同参数 */) external;
}
```

**参数设计哲学**：

-   完全对齐 EVM 日志结构（`chain_id` + `address` + 4 topics）
    
-   使用 `uint256` 统一编码，便于位操作和通配符表示
    

### **2.2 IReactive —— 响应式合约标准**

```
interface IReactive is IPayer {
    struct LogRecord {
        uint256 chain_id;
        address _contract;
        uint256 topic_0-3;
        bytes data;            // 非索引事件数据
        uint256 block_number;
        uint256 op_code;       // 操作码分类
        uint256 block_hash;
        uint256 tx_hash;
        uint256 log_index;     // 交易内日志索引
    }
    
    event Callback(
        uint256 indexed chain_id,
        address indexed _contract,
        uint64 indexed gas_limit,
        bytes payload          // 回调载荷
    );
    
    function react(LogRecord calldata log) external;
}
```

**设计亮点**：

-   `LogRecord` 包含**完整的区块上下文**，支持复杂验证逻辑
    
-   `Callback` 事件是**跨链通信的原语**，ReactVM 通过 emit Callback 触发 Reactive Network 的实际操作
    

* * *

## **三、订阅配置模式**

### **3.1 通配符系统**

| 通配符 | 含义 | 使用场景 |
| --- | --- | --- |
| address(0) | 任意合约地址 | 监听全链特定事件类型 |
| uint256(0) | 任意链ID | 跨链监听同一合约 |
| REACTIVE_IGNORE | 任意主题值 | 忽略该索引位置 |

> **硬性约束**：至少一个条件必须是**具体值**（非通配符），避免无意义的全量订阅

### **3.2 典型订阅模式示例**

```
// 模式1：监听特定合约的所有事件
service.subscribe(CHAIN_ID, 0x7E09...3003, REACTIVE_IGNORE, REACTIVE_IGNORE, REACTIVE_IGNORE, REACTIVE_IGNORE);

// 模式2：监听全链的特定事件类型（如 Uniswap V2 Sync）
service.subscribe(CHAIN_ID, address(0), 0x1c41...bad1, REACTIVE_IGNORE, REACTIVE_IGNORE, REACTIVE_IGNORE);

// 模式3：精确匹配——特定合约 + 特定事件
service.subscribe(CHAIN_ID, 0x7E09...3003, 0x1c41...bad1, REACTIVE_IGNORE, REACTIVE_IGNORE, REACTIVE_IGNORE);
```

* * *

## **四、动态订阅高级模式**

### **4.1 架构必要性**

由于系统合约仅在 Reactive Network 可用，ReactVM 中的合约副本需要通过 **Callback 机制** 间接操作订阅：

```
ReactVM 检测到事件 → emit Callback → Reactive Network 执行实际订阅/取消订阅
```

### **4.2 Approval Magic Demo 深度解析**

**状态分离设计**：

```
// Reactive Network 实例特有状态
address private _callback;

// ReactVM 实例特有状态  
uint256 public counter;
```

**构造函数的双层初始化**：

```
constructor(ApprovalService service_) payable {
    owner = msg.sender;
    approval_service = service_;
    
    if (!vm) {  // ← 关键防护：仅在主网执行
        // 订阅"订阅请求"和"取消订阅请求"本身
        service.subscribe(SEPOLIA_CHAIN_ID, address(approval_service), SUBSCRIBE_TOPIC_0, ...);
        service.subscribe(SEPOLIA_CHAIN_ID, address(approval_service), UNSUBSCRIBE_TOPIC_0, ...);
    }
}
```

**权限控制修饰符**：

```
modifier callbackOnly(address evm_id) {
    require(msg.sender == address(service), 'Callback only');
    require(evm_id == owner, 'Wrong EVM ID');
    _;
}
```

**react() 函数的事件路由逻辑**：

```
function react(LogRecord calldata log) external vmOnly {
    if (log.topic_0 == SUBSCRIBE_TOPIC_0) {
        // 编码 subscribe() 调用 → emit Callback 到 Reactive Network
        bytes memory payload = abi.encodeWithSignature("subscribe(address,address)", address(0), subscriber);
        emit Callback(REACTIVE_CHAIN_ID, address(this), CALLBACK_GAS_LIMIT, payload);
    } 
    else if (log.topic_0 == UNSUBSCRIBE_TOPIC_0) {
        // 类似处理取消订阅
    } 
    else {
        // 实际的 Approval 事件处理 → 回调到 Sepolia 的 ApprovalService
        (uint256 amount) = abi.decode(log.data, (uint256));
        bytes memory payload = abi.encodeWithSignature("onApproval(...)", ...);
        emit Callback(SEPOLIA_CHAIN_ID, address(approval_service), CALLBACK_GAS_LIMIT, payload);
    }
}
```

## **五、禁止事项与最佳实践**

### **5.1 明确禁止的订阅模式**

| 禁止项 | 原因 | 替代方案 |
| --- | --- | --- |
| 非等值比较（<, >, 范围） | 系统不支持 | 链下过滤或合约内二次判断 |
| 单订阅内的析取条件（OR） | 避免组合爆炸 | 多次调用 subscribe() |
| 全链 + 全合约同时通配 | 无意义且资源浪费 | 至少限定一个维度 |
| 单链全事件订阅 | 被认为不必要 | 明确事件类型 |

### **5.2 成本与性能考量**

> **取消订阅昂贵**：需要搜索并移除存储中的订阅记录，涉及 EVM 存储操作

> **重复订阅允许**：系统不主动去重（避免昂贵的存储检查），但功能上等效于单次订阅，用户需自行保证幂等性

* * *

## **六、关键设计模式总结**

### **6.1 静态订阅 vs 动态订阅**

| 维度 | 静态订阅 | 动态订阅 |
| --- | --- | --- |
| 配置时机 | 构造函数 | 运行时通过事件触发 |
| 灵活性 | 固定监听目标 | 按需订阅/取消 |
| 复杂度 | 低 | 高（需处理 Callback 路由） |
| 适用场景 | 已知固定依赖 | 用户可配置的监听服务 |

### **6.2 跨链通信流程（动态订阅场景）**

```
┌─────────────┐     事件触发      ┌─────────────┐
│  源链合约    │ ───────────────→ │  ReactVM    │
│ (如Sepolia) │                  │  合约副本    │
└─────────────┘                  └──────┬──────┘
                                        │
                                        ▼
                              ┌─────────────────┐
                              │  react() 处理逻辑 │
                              │  → emit Callback  │
                              └────────┬────────┘
                                       │
                    ┌──────────────────┼──────────────────┐
                    ▼                  ▼                  ▼
              ┌─────────┐      ┌─────────────┐      ┌─────────┐
              │ 订阅操作  │      │ 取消订阅操作  │      │ 业务回调  │
              │(Reactive│      │ (Reactive   │      │(回源链)  │
              │ Network) │      │  Network)   │      │         │
              └─────────┘      └─────────────┘      └─────────┘
```

  

* * *

## **七、实践要点 checklist**

-   \[ \] 构造函数中始终使用 `if (!vm)` 保护订阅调用
    
-   \[ \] 动态订阅时正确实现 `vmOnly` 和 `rnOnly` 修饰符
    
-   \[ \] `Callback` 事件必须指定足够的 `gas_limit`
    
-   \[ \] 主题参数使用 `uint256(uint160(address))` 进行地址编码
    
-   \[ \] 避免重复订阅以降低不必要的 Gas 消耗
    
-   \[ \] 复杂条件通过多次 `subscribe()` 调用实现，而非单订阅内 OR 逻辑
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->

回来太晚了，各位加油，I need to lie down
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->


**ReactVM** 是 Reactive Network 的专用执行引擎，为 Reactive Contracts 提供隔离、确定性的运行环境，使其能够安全地监听和响应跨链事件；Reactive Network 作为**底层基础设施**，通过这一架构实现了智能合约从**"被动调用"**到"**主动反应"**的范式转变。  
  
**核心概念：双状态环境（Dual-State Environment）**

### **1\. 基本架构**

**表格**

| 环境 | 特性 | 功能 |
| --- | --- | --- |
| Reactive Network | 标准 EVM 区块链 + 系统合约 | 订阅/取消订阅源链事件、用户交互 |
| ReactVM | 隔离的受限虚拟机 | 事件处理、业务逻辑执行、发起回调 |

**关键设计决策**：每个部署地址拥有独立的 ReactVM，实现并行化处理以支持大规模事件处理。

* * *

### **2\. 执行上下文检测机制**

`detectVm()` **函数原理**

**solidity**

复制

```solidity
function detectVm() internal {
    uint256 size;
    assembly { size := extcodesize(0x0000000000000000000000000000000000fffFfF) }
    vm = size == 0;  // true = ReactVM, false = Reactive Network
}
```

**表格**

| 检测结果 | 含义 | vm 值 |
| --- | --- | --- |
| size > 0 | 系统合约存在 → Reactive Network | false |
| size == 0 | 无系统合约 → ReactVM | true |

**环境限制修饰符**

**solidity**

复制

```solidity
modifier rnOnly() { require(!vm, 'Reactive Network only'); _; }
modifier vmOnly()  { require(vm,  'VM only'); _; }
```

* * *

### **3\. 双状态变量管理**

**变量分离模式**

**表格**

| 状态 | 变量来源 | 典型用途 |
| --- | --- | --- |
| Reactive Network 变量 | 继承自 AbstractReactive | service（订阅服务）、vm（环境标志） |
| ReactVM 变量 | 合约自身声明 | 业务逻辑状态（如 triggered, threshold） |

**实例：Uniswap 止损订单合约**

**Reactive Network 专用逻辑**（构造函数中）：

**solidity**

复制

```solidity
if (!vm) {
    service.subscribe(SEPOLIA_CHAIN_ID, pair, UNISWAP_V2_SYNC_TOPIC_0, ...);
    service.subscribe(SEPOLIA_CHAIN_ID, stop_order, STOP_ORDER_STOP_TOPIC_0, ...);
}
```

**ReactVM 专用变量**：

**solidity**

复制

```solidity
bool private triggered;      // 防止重复回调
bool private done;           // 最终停止信号
address private pair;        // 交易对地址
uint256 private threshold;   // 触发阈值
```

**ReactVM 专用函数**：

**solidity**

复制

```solidity
function react(LogRecord calldata log) external vmOnly {
    // 处理 Sync 事件，检查阈值，emit Callback
}
```

* * *

### **4\. 交易执行流程对比**

**Reactive Network 交易**

**表格**

| 触发方式 | 说明 | 示例 |
| --- | --- | --- |
| 用户直接调用 | 管理功能、状态更新 | pause() / resume() |
| 源链事件触发 | 监听并分发事件到订阅者 | 自动 dispatch 到 ReactVM |

`pause()` **函数要点**：

-   `rnOnly` + `onlyOwner` 双重限制
    
-   遍历取消所有可暂停订阅
    
-   设置 `paused = true` 状态标志
    

**ReactVM 交易**

**plain**

复制

```plain
源链事件 → Reactive Network 监听 → 分发到 ReactVM → 自动调用 react()
```

**核心特征**：

-   ❌ 用户无法直接调用
    
-   ✅ 仅由系统通过事件触发
    
-   `react()` 函数输出：状态更新 + 跨链回调（`emit Callback(...)`）
    

* * *

### **5\. 关键设计原则总结**

**表格**

| 原则 | 实现方式 |
| --- | --- |
| 环境隔离 | 同一合约代码，两个独立状态存储 |
| 权限控制 | rnOnly/vmOnly 修饰符确保函数在正确环境执行 |
| 并行处理 | 每地址独立 ReactVM，避免事件处理瓶颈 |
| 事件驱动 | ReactVM 完全依赖事件输入，无外部直接调用 |

* * *

### **6\. 开发要点 checklist**

-   \[ \] 继承 `AbstractReactive` 获取基础功能
    
-   \[ \] 构造函数中区分 `if (!vm)` 进行订阅注册
    
-   \[ \] 业务逻辑函数标记 `vmOnly`
    
-   \[ \] 管理函数标记 `rnOnly` + `onlyOwner`
    
-   \[ \] 明确分离两种状态的变量用途
    
-   \[ \] `react()` 函数处理事件并触发回调
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->



## **Reactive Network 生态系统笔记**

### **一、项目定位**

**Reactive Network** 是一个支持**可扩展、可互操作 dApp** 的区块链网络，核心特色是**跨链自动化**，完全用 **Solidity** 编写且**完全在链上运行**。

**核心理念**："If you can express it as a condition, you can automate it"（只要能表达为条件，就能自动化）

* * *

### **二、技术栈（Reactive Stack）四大支柱**

| 模块 | 核心功能 | 关键特性 |
| --- | --- | --- |
| Decentralized Automation | 智能合约的"如果-那么"逻辑 | 无需机器人/手动触发；自执行DeFi策略；动态NFT |
| Cheaper Computation | 卸载重计算任务 | 垂直扩展；Gas成本仅为L1的一小部分 |
| ZK-Verified Events | 零知识证明验证跨链事件 | 不泄露数据即可验证；支持跨链借贷无需KYC |
| DeFi Infrastructure | AI兼容执行环境 | AI模型可作为合约部署；自主代理跨链操作 |

* * *

### **三、生态项目详解（14个项目）**

**A. Shogun**

-   **定位**：AI+DeFi自动化
    
-   **机制**：Reactive Contracts 结合 AI 生成信号 + 自动链上执行
    

**B. NewEra Finance**

-   **定位**：Uniswap V4 机制自动化
    
-   **功能**：TWAMM（时间加权自动做市商）+ 预言机驱动的限价单自动执行
    

**C. QSTN**

-   **定位**：Web3 调研平台
    
-   **功能**：自动透明化奖励分发
    

**D. DexTrade**

-   **定位**：P2P 交易环境
    
-   **核心**：无Gas跨链兑换协调
    

**E. Rogues Studio**

-   **定位**：Web3 游戏
    
-   **机制**：追踪玩家活动 → 分发NFT奖励；游戏事件记录于低成本链，奖励可跨链铸造
    

**F. SmarTrust**

-   **定位**：去中心化自由职业者托管服务
    
-   **流程**：
    
    1.  付款锁定在自选区块链
        
    2.  里程碑达成 → Reactive 自动释放资金
        
    3.  争议发生 → 分配以太坊中立仲裁员，在原链强制执行裁决
        
-   **优势**：无中介、无手动步骤、无Gas意外
    

**G. Non-Locking FlashLoan Protocol（FlexiLoan）**

-   **创新**：用户保持代币完全控制权的同时提供流动性
    
-   **架构**：Reactive Network 事件驱动架构
    

**H. ReacDEFI**

-   **定位**：无代码部署平台
    
-   **功能**：模板化部署 Reactive 智能合约，几点击实现跨链DApp自动化
    

**I. Voltrade**

-   **定位**：交易竞赛应用
    
-   **背景**：源自 $BRETT x Reactive Network 活动（3500万交易量，3万美元奖池）
    
-   **目标**：将概念转化为可扩展产品
    

**J. ReactiveFlow Lender**

-   **功能**：跨链借贷（以太坊Sepolia ETH抵押 → Kopli测试网MATIC贷款）
    
-   **技术**：Reactive 智能合约实现无缝自动化
    

**K. Flash Profit Extractor**

-   **定位**：DeFi套利工具
    
-   **机制**：自动代币兑换 + 实时定价
    

**L. Dynamic NFT Royalty System**

-   **创新**：实时调整 + 跨链收益
    
-   **数据**：创作者可多回收\*\*40%\*\*收入；买家和市场的费用更低
    

**M. Reactive Bridge**

-   **功能**：无需中心化中介的跨链代币转移
    
-   **意义**：为可互操作DeFi和dApp生态奠定基础
    

**N. Based BRETT**

-   **定位**：首个 Reactive Meme币
    
-   **机制**：
    
    -   自动链上排行榜实时追踪Uniswap/SushiSwap的$BRETT交易
        
    -   X API动态更新顶级交易者头像为专属BRETT x Reactive NFT
        
-   **成果**：消除手动追踪，实现透明竞争，无需通胀激励即可维持交易参与度
    

* * *

### **四、关键洞察**

1.  **技术差异化**：Reactive 的核心竞争力在于**事件驱动架构** + **跨链原生**，而非简单的EVM兼容链
    
2.  **应用场景聚焦**：DeFi自动化（止损、套利、借贷）、游戏（跨链NFT奖励）、AI集成（信号→执行）
    
3.  **商业模式创新**：多个项目展示"低成本链记录 + 高价值链结算"的分层架构
    
4.  **Meme币实验**：Based BRETT 案例证明 Reactive 可用于**社交层身份验证**（X API + NFT头像），超越纯金融用例
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
