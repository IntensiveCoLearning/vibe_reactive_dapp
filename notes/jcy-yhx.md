---
timezone: UTC+8
---

# jcy-yhx

**GitHub ID:** jcy-yhx

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-19
<!-- DAILY_CHECKIN_2026-03-19_START -->
参与了workshop
<!-- DAILY_CHECKIN_2026-03-19_END -->

# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->

打卡一下，在写代码。
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->


# 动手实践前端开发

尝试使用Next.js + wagmi/viem完成前端钱包互动的开发，可以互相切换两条链
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->



仔细看了下止损止盈代码，看了下官方建议开发方向。
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->




# Uniswap V2 与响应式自动化交易demo

## 一、 核心概念：什么是 Uniswap V2？

它是一套部署在区块链上的**自动化做市商 (AMM)** 协议。

### 1\. 交易对 (Pair)

-   **唯一性**：在同一个 Factory 合约下，任何两个代币（如 ETH/USDT）只能拥有**一个**专属的交易池合约。
    
-   **无许可性 (Permissionless)**：任何人都可以创建新对，但只有注入了资产（流动性）的池子才能进行交易。
    

### 2\. 数学公式：X x Y = K

-   **$x$ 和 $y$**：池子中两种代币的实时库存（Reserves）。
    
-   **$k$ (常数)**：在不计算手续费的情况下，x 和 y 的乘积必须保持不变。
    
-   **价格发现**：价格不是由外部输入的，而是由 x/y 的比例决定的。
    

## 二、 价格是怎么保持准确的？—— 套利机制

Uniswap 并不联网，它不知道币安（Binance）上的价格。

-   **脱钩**：如果有人在 Uniswap 大力买入，导致 x 减少，y 增加，Uniswap 的价格会高于市场价。
    
-   **套利 (Arbitrage)**：套利者发现差价后，会在价格低的地方买入，到价格高的地方卖出。
    
-   **结果**：这种基于利润驱动的“搬砖”行为，强迫 Uniswap 的价格始终与全球市场对齐。
    

## 三、 uniswap-v2-stop-take-profit-order合约部署步骤

### 1\. 建立实验室：创建与注资

-   **调用合约先往自己钱包中mint两种代币各一百个**
    
-   **Create Pair**：在 Factory 合约里开辟一个流动池，相当于放入一个柜台中。
    
-   **Transfer & Mint**：
    
    -   `transfer` 只是把钱扔进柜台地址。
        
    -   `mint` 才是关键：它让合约清点余额，并颁发 **LP Token**（取款凭证/股份证明）。
        

### 2\. 部署Callback 合约

-   **Router (路由)**：`0xC532...` 是官方服务台，负责自动计算兑换比例并执行。
    
-   **Callback 合约**：部署在目的地链（Sepolia），它平时带着 Gas 费（0.02 ETH）待命。
    
-   **安全锁**：通过构造函数绑定 **Proxy 地址**，确保只有 Reactive Network 能指挥它卖币。
    

### 3\. 部署“大脑”：Reactive 合约

-   **监控**：它监听池子的 `Sync` 事件。
    
-   **判断**：每当池子里的 x 和 y 发生变动，它就重算价格并对比设定的“止损线”。
    

## 四、 验证环节：人工砸盘与触发

为了测试自动化是否生效，我们手动改变价格：

1.  **向池子转账**：直接 `transfer` 大量代币。
    
2.  **调用** `swap`：强制换出另一种代币，打破池子原有的平衡。
    
3.  **连锁反应**：
    
    -   池子抛出 `Sync` 事件。
        
    -   **Reactive Network** 捕捉事件 -> 发现触发止损 -> 呼叫 **Callback 合约**。
        
    -   **Callback 合约** 自动前往 **Router** 执行卖出任务。
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->





# 经济模型-反应式合约如何支付执行和跨链回调费用-**实际运行时的费用支付流程**

## **RVM Transactions**

RVM 交易在执行时不包含 gas 价格。费用将在后续区块（通常是下一个区块）的 BaseFee 基础上计算并收取。由于记账在区块级别聚合，Reactscan 无法将费用与单个 RVM 交易一一对应。

## **最大 Gas 上限**

**RVM 交易的最大 gas 上限为 900,000 个单位。**

费用计算公式：  
fee = BaseFee ⋅ GasUsed

其中：

-   BaseFee：记账区块中每个 gas 单位的基础费用
    
-   GasUsed：执行过程中实际消耗的 gas
    

## Reactive Network 交易

RNK 交易遵循标准的 EVM gas 模型（提前支付）。

## 费用支付流程总结

| 概念 | 说明 | 处理方式 | 资金不足后果 |
| --- | --- | --- | --- |
| REACT 代币 | 用于所有 gas 和费用 | 提前充值合约 | 债务累积 |
| RVM Gas 模型 | 先执行，后付费（BaseFee × GasUsed） | 充值 + 结算债务 | 合约 inactive |
| 回调 Gas 下限 | 最低 100,000 gas | 目标链上充值回调合约 | 请求被忽略 / 黑名单 |
| 债务结算 | 手动 coverDebt() 或 自动 depositTo() | 直接转账或系统/代理 | 黑名单，停止执行 |
| 黑名单机制 | 债务过高 → 无法执行任何 tx/callback | 结算债务恢复 | 操作完全停止 |

# Uniswap V2 止损示例代码学习

## **合约结构概览**

### 1.**Reactive 合约**:UniswapDemoStopTakeProfitReactive.sol

部署在 Reactive Network 上，订阅用户回调合约的生命周期事件以及指定的 Uniswap V2 交易对sync日志，检测价格阈值并触发执行。

### **2.回调合约:UniswapDemoStopTakeProfitCallback**

部署在目的链（Sepolia），管理订单的创建、执行、暂停/取消，实际把 token 从用户钱包转给路由器并完成 swap。

### 3.辅助合约:RescuableBase

为回调合约提供了救援 ETH/ERC20 的通用功能。

两个主要合约之间通过**callback**事件和 Reactive Network 的服务（service.subscribe/service.unsubscribe）交互：  
Reactive 监控到条件满足后 emit 一个回调事件发送到目的链；回调合约的authorizedSenderOnly修饰符确保只有 Reactive 网络代理调用 executeStopOrder。
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->






# **ReactVM 与反应式网络作为双态环境**

**每个反应式合同有两个实例——一个在反应式网络上，另一个在其独立的 ReactVM。**

**Reactive Network**：一条 EVM 兼容的 L1 区块链，但它增加了系统合约，让合约可以订阅其他链（Ethereum、Sepolia、BNB、Polygon 等）的事件日志。

**ReactVM**：不是普通的 EVM，而是每个部署者地址（deployer）独享的一个**受限的、隔离的虚拟机实例**。

-   同一个 deployer 部署的所有合约，都跑在同一个 ReactVM 里，可以互相调用。
    
-   但它**完全隔离**于 Reactive Network 上的其他合约。
    
-   设计目标：**高性能、并行处理海量事件**，不阻塞主链。
    

用户管理的部分只在 Reactive Network处理,事件到来时的部分只在 ReactVM 执行，状态两套、代码一套、环境靠 vm 标志区分，写代码时需注意哪些变量应该只在哪个环境更新。

```
if (vm) { // 我在 ReactVM
    // 只处理事件逻辑
} else {  // 我在主链
    // 订阅、暂停等管理逻辑
}
```

# **如何订阅外部事件**

**订阅只能在主链（Reactive Network）做，事件进来后只在 ReactVM 的 react() 里处理。**

## 1\. 订阅的核心目的和流程

RC 通过订阅，监控其他链（Ethereum、Sepolia、BNB 等）的合约事件。一旦匹配的事件发生，Reactive Network 会自动把事件打包成 LogRecord，推送到你的合约的 react(LogRecord) 函数里执行。

```
外部链合约发出事件 Log
          ↓
Reactive Network 监听到（跨链桥/中继）
          ↓
检查是否有 RC 订阅了这个 chain_id + _contract + topics
          ↓
如果匹配 → 构造 LogRecord struct
          ↓
调用你的 RC 的 react(LogRecord) （在 ReactVM 里执行）
          ↓
你的 react() 里可以：
  - 更新状态（ReactVM 里的状态）
  - emit Callback(...) → 触发目标链上的回调执行
```

## 2\. 关键接口：ISubscriptionService（订阅服务）

**这是系统合约提供的接口**

```
interface ISubscriptionService is IPayable {
    function subscribe(
        uint256 chain_id,          // 源链的 EIP-155 chain ID（如 Sepolia=11155111）
        address _contract,         // 发出事件的合约地址
        uint256 topic_0,           // 事件签名 hash（必须）
        uint256 topic_1,           // indexed 参数1（可忽略）
        uint256 topic_2,
        uint256 topic_3
    ) external;

    function unsubscribe(...) external;  // 参数同上
}
```

-   **REACTIVE\_IGNORE** = uint256(0) 或 address(0)，表示“任意值都匹配”。
    
-   至少**一个参数必须具体**（不能全用忽略），否则禁止。
    
-   **禁止**订阅：全链所有事件、所有合约所有事件、范围比较（< >）、位运算等复杂过滤。只支持**严格相等**匹配。
    

例子：

```
// 监听某个 Uniswap Pair 的所有事件
service.subscribe(SEPOLIA_CHAIN_ID, pairAddress, REACTIVE_IGNORE, REACTIVE_IGNORE, REACTIVE_IGNORE, REACTIVE_IGNORE);

// 只监听 Sync 事件（topic_0 是事件签名 keccak）
service.subscribe(SEPOLIA_CHAIN_ID, address(0), SYNC_TOPIC_0, REACTIVE_IGNORE, REACTIVE_IGNORE, REACTIVE_IGNORE);

// 监听特定合约 + 特定 topic + 特定 owner（Approval 事件常用 topic_2 或 topic_1 存 owner）
service.subscribe(SEPOLIA_CHAIN_ID, address(0), APPROVAL_TOPIC_0, REACTIVE_IGNORE, uint256(uint160(specificOwner)), REACTIVE_IGNORE);
```

**注意：重复 subscribe 允许，但浪费 gas（幂等性自己保证）。unsubscribe 很贵（要搜索删除），尽量少用。**

## 3\. 收到事件后：LogRecord 和 react()

```
struct LogRecord {
    uint256 chain_id;
    address _contract;
    uint256 topic_0;   // 到 topic_3
    bytes data;        // 非 indexed 参数（abi.encode 的部分）
    uint256 block_number;
    uint256 op_code;   // 事件分类码（系统用）
    uint256 block_hash;
    uint256 tx_hash;
    uint256 log_index;
}
```

**收到事件后触发react（）操作**

```
function react(LogRecord calldata log) external vmOnly { ... }
```

  
**可以进行以下操作：**

-   解码 [log.data](http://log.data)
    
-   检查 log.topic\_x 是否匹配你关心的
    
-   读 non-indexed 数据
    
-   更新 ReactVM 状态
    
-   如果需要跨链执行 → emit Callback(target\_chain\_id, target\_contract, gas\_limit, payload);
    

**payload 通常是 abi.encodeWithSignature("someFunction(...)", args)。**

## 4.动态订阅？(未进行实操)

Reactive Network 的**系统合约（ISubscriptionService）**只能在 **Reactive Network 主链** 上调用，而大多数业务逻辑（react() 处理事件）发生在 **ReactVM** 里。

-   ReactVM 里**不能**直接 call service.subscribe() → 会 revert（系统合约地址在 VM 里没代码）
    
-   但又希望合约能“根据事件内容”决定要不要订阅某个新东西（比如用户刚 approve 了，就开始监听他的 Transfer）
    

解决办法：**用 Callback 作为桥梁** ReactVM → emit Callback → 主链收到 → 执行真正的 subscribe/unsubscribe

## 5\. 环境区分

-   **Reactive Network**（!vm）：能直接 call service.subscribe / unsubscribe
    
-   **ReactVM**（vm=true）：**不能** call subscribe（系统合约不存在，会 revert） → 所以所有订阅逻辑必须 rnOnly 或 if (!vm)
    
-   动态订阅靠 Callback “跨环境”通信：ReactVM emit Callback → 主链收到并执行 subscribe
    

# Reactive Contracts + Oracle

## **传统合约+预言机的痛点**

拿用 Chainlink 取 ETH/USD 价格为例子：

```
function getLatestPrice() public view returns (int) {
        (
            , int price, , , 
        ) = priceFeed.latestRoundData();
        return price;
    }
```

-   getLatestPrice() 是 **view 函数**，必须有人（EOA 或 keeper）主动 call 才能“刷新”数据。
    
-   合约自己**不能主动拉取**（无权限发起 tx）。
    
-   要实时响应价格变化？必须靠外部 keeper（如 Chainlink Automation、Gelato）定时调用 → 延迟 + gas 成本 + 中心化风险。
    
-   没法“事件驱动”：价格变了没人 call，就错过机会。
    

## Reactive Contracts + Oracle的优势

**Reactive 把 oracle 的“数据更新”事件化，让合约能真正“反应式”响应外部世界变化，而不用 keeper 轮询。**

**oracle 更新价格 → emit 事件 → Reactive 自动推到react() → 即时响应！**
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->







# Basic Demo 实操

## Basic Demo 三个合约详解与流程合约角色

### 1、Origin Contract（BasicDemoL1Contract）

-   receive()：收到 ETH → emit Received(origin, sender, value) → 退回 ETH
    
-   作用：事件触发器
    

### 2、Reactive Contract（BasicDemoReactiveContract）

-   constructor：调用 subscribe() 订阅源链 Received 事件（topic\_0 固定）
    
-   react()：收到 log → if value ≥ 0.001 ETH → emit Callback（payload 编码 callback(address)）
    
-   作用：大脑 + 反应逻辑
    

### 3、Destination Callback（BasicDemoL1Callback）

-   继承 AbstractCallback
    
-   callback(sender)：验证 authorizedSenderOnly + rvmIdOnly → emit CallbackReceived
    
-   作用：跨链接收端 + 授权验证
    

测试命令完整流程（cast send 0.001 ETH）

1.  转 0.001 ETH → Origin receive() 触发
    
2.  emit Received + 立即退回 ETH
    
3.  Reactive 扫描 → 调用 react()
    
4.  条件成立 → emit Callback（替换 sender 为 ReactVM ID）
    
5.  系统跨链 → Proxy 调用 Destination callback()
    
6.  Destination emit CallbackReceived（证明成功）
    

浏览器可见标志：

-   源链：Received 事件 + ETH 转入/退回
    
-   目标链：CallbackReceived 事件 + 来自 Proxy 的调用交易
    

## 一些概念

### **1\. 事件Topic**

-   **topic\_0**: 事件签名哈希（常数，如`Received`事件的hash）
    
-   **topic\_1-3**: indexed参数的值
    
    text
    
    ```
    event Received(origin, sender, value)
    topic_1 = origin地址
    topic_2 = sender地址  
    topic_3 = value金额
    ```
    

### **2\. rvmIdOnly修饰器**

solidity

```
// 特性：
- 自动验证调用者是Reactive VM
- 自动替换sender参数为真实Reactive合约地址
- 安全机制，防止伪造

// 效果：
你传入：address(0) 或 0x1234...
实际收到：你的Reactive合约真实地址
```

### **3\. 订阅函数**

```
function subscribe(
    uint256 chain_id,           // 要监听的链ID
    address contract_address,   // 要监听的合约地址
    uint256 topic_0,            // 事件签名哈希（必填）
    uint256 topic_1,            // 第一个indexed参数的过滤值
    uint256 topic_2,            // 第二个indexed参数的过滤值
    uint256 topic_3             // 第三个indexed参数的过滤值
) external;
```
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->








## **什么是睿应层（Reactive Network）**

**Reactive Network**是一个兼容 EVM 的链**，**引入了一种全新的合约类型，叫**Reactive Contracts，RC** 与传统智能合约的主要区别在于反应性。**传统智能合约**是**被动的，**等待别人调用函数去执行，而**RC**是主动的**，**可以主动监听事件，自动触发。

## **Reactive Contracts是怎么做到“主动”的**

创建反应式合约时，首先需要指定感兴趣的链、合同以及事件（events)，如：价格达到阈值、某个交易对流动性变化、某笔借贷抵押比例触发清算条件，一旦检测到感兴趣的事件，反应式网络会自动执行用户预先在反应式合同中实现的逻辑。

流程大概如下：

1、Reactive Smart Contract（RSC）在部署/配置时声明订阅，在 Solidity 代码里通过特定的接口，react() 函数 + 订阅参数，相当于在 Reactive Network 上注册了一个“事件订阅”。

2、Reactive Network 作为底层网络，本身是一个专用的执行层，它的节点会持续监听所有它支持的 EVM 兼容链的区块和日志（logs），实时扫描新区块里的事件日志，对比所有已注册的 RSC 订阅条件，看有没有匹配的 log，一旦匹配上，就把这条 log以交易的形式推送到 Reactive Network 自己的链上，调用对应 RSC 的 react() 函数，把事件数据作为参数传进去。.

3、在 Reactive Network 上执行 react()，RSC 的 react() 函数收到事件数据后，用 Solidity 逻辑判断是否要行动（if 条件、阈值等），如果要行动，它可以生成 callback 交易（发到目标链），或者更新自身状态、触发其他逻辑，整个执行发生在 Reactive Network 的 ReactVM（隔离的执行环境）里，gas 由 RSC 拥有者或经济模型支付。

## **How EVM Events Work**

当智能合约发出事件时，事件数据会存储在交易日志中。这些日志附着在区块链的区块上，但不会直接影响区块链状态。相反，它们提供了一种基于事件参数记录和检索信息的方法。开发者通过**event**关键词定义智能合约中的事件，随后是事件名称和他们希望记录的信息类型。智能合约在发送事件时使用 **emit**关键字，随后输入事件名称和待记录的数据。外部应用程序，如 dApps 或后端服务，可以监听这些事件。通过指定事件签名并可选地过滤参数，这些应用程序可以在事件发出时订阅实时更新。

## **Event Processing in Reactive Contracts**

反应式网络持续监控事件日志，并将其与反应式合同中定义的订阅标准进行匹配。当检测到符合条件的事件时，网络会触发**react()**方法，并传递相关信息。反应式合同可以访问所有标准的 EVM 功能。然而，它们运行在一个私有的 ReactVM，限制它们只能与同一部署器部署的合同交互。这种隔离确保反应式合约在处理反应式网络事件时保持受控和安全的环境。

## **如何向目标链发起回调**

Reactive 合约运行在 Reactive Network 这个专用链上（ReactVM 环境），当它收到事件、跑完 react() 逻辑后，如果需要“干点事”，但这个事要在另一条链上完成，就不能直接调用（因为 Reactive 链和目标链是分开的）。这时Reactive 合约 emit（发出）一个特殊的 Callback 事件。Reactive Network 的系统（节点/基础设施）看到这个事件 → 自动帮你在目标链上发一条交易，调用你指定的合约、执行指定的函数。

## Callback 事件的结构（event Callback）

```solidity
event Callback(
    uint256 indexed chain_id,       // 目标链的 chain ID（EIP-155，比如 Ethereum=1，Base=8453）
    address indexed _contract,      // 目标链上要调用的合约地址
    uint64 indexed gas_limit,       // 给这次跨链交易设置的 gas 上限（防止 gas 爆炸）
    bytes payload                   // 编码好的调用数据（告诉要调用哪个函数、传什么参数）
);
```

**payload 是最关键的：它是用 abi.encodeWithSignature 编码的，格式通常是 "函数名(参数类型列表)" + 实际参数。**

## 安全机制：RVM ID 自动替换（最重要的一点防作恶）

Reactive Network 强制在处理 payload 时，会把 payload 的前 160 bits（前 20 字节） 自动替换成 RVM ID（ReactVM ID）。RVM ID 其实就是这个 Reactive 合约的部署者（deployer）地址（或 ReactVM 实例的标识）。**结果：不管你在 Solidity 里第一个参数写什么（哪怕写 address(0)），实际发到目标链时，第一个参数都会变成你的 ReactVM 地址。**

**这样设计的目的是：**授权控制，目标链上的合约（比如你的 Stop Order 合约）可以检查 msg.sender（或第一个参数）是不是授权的 ReactVM 地址，只有“自己人”才能调用。防止滥用，别人不能伪造你的 Reactive 合约去乱发回调。必须留第一个参数给它，文档反复强调，payload 至少要有一个 address 类型参数（第一个槽位），否则调用会失败。

## 跨链回调处理总结

处理流程总结（Processing the Callback）

1.  Reactive 合约在 react() 里 emit Callback 事件。
    
2.  Reactive Network 监听到这个事件（因为它是系统级事件）。
    
3.  系统解析 chain\_id、\_contract、gas\_limit、payload。
    
4.  自动替换 payload 前 20 字节为 RVM ID。
    
5.  用提供的 gas\_limit，在目标链上发一条交易：调用 \_contract 的对应函数，传入解码后的 payload（已替换）。
    
6.  目标链执行成功 → 完成跨链自动化。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
