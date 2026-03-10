---
timezone: UTC+8
---

# vergissxie

**GitHub ID:** vergissxie

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->
## 🌟 一、 范式转移：什么是响应式智能合约 (RSC)?

传统的智能合约（如你写的 `HeroNFT`）是**被动**的。它们像一个锁着的箱子，只有当用户通过交易（Transaction）去“点”它时，它才会运行。

**响应式智能合约 (RSCs)** 代表了一种范式转移：

-   **自主性 (Autonomous)**：它们不依赖用户手动触发，而是主动监测 EVM 兼容链上的**事件 (Events)** 并做出反应。
    
-   **全链感知**：它们能监听多个区块链的日志，根据预设逻辑处理后，自主在其他链执行动作。
    
-   **去中心化自动化**：无需中心化的“定时任务”脚本或机器人，直接在 Reactive Network 的去中心化网络中完成。
    

* * *

## 🛠️ 二、 开发 RSC 的前置知识 (Prerequisites)

在开始 Reactive 开发之前，你需要掌握以下技能点：

1.  **Solidity 核心语法**：熟悉 `mapping`, `struct`, `event` 等基础（参考之前的 `HeroNFT` 练习）。
    
2.  **EVM 运行原理**：理解交易签名、函数调用以及 Gas 机制。
    
3.  **Git 与 命令行**：用于本地环境配置和代码版本管理。
    
4.  **钱包与测试币**：例如使用 MetaMask 在 Sepolia 测试网领水和交互。
    
5.  **DeFi 基础概念**：理解流动性池、AMM（如 Uniswap）的工作原理，以便理解 RSC 在金融场景下的应用。
    

* * *

## 🏗️ 三、 核心架构：The Reactive Loop

RSC 的工作流程形成一个完整的自动化闭环：

1.  **源链事件 (Source Event)**：源链（如 Ethereum）合约 `emit` 一个事件。
    
2.  **监听与过滤 (Subscription)**：Reactive Network 监测并匹配订阅 criteria。
    
3.  **响应逻辑 (Reactive Logic)**：网络触发 RSC 的 `react()` 函数。
    
4.  **回调执行 (Callback)**：RSC 发出 `Callback` 事件，网络自动在目标链（如 Base）发起交易。
    

* * *

## 💻 四、 深入代码：`IReactive` 接口详解

所有的 RSC 必须实现 `IReactive` 接口，它是响应式逻辑的“合同”。

### 1\. 事件快照：`LogRecord` 结构体

当 Reactive 节点捕捉到事件，它会将以下信息“喂”给你的合约：

-   `chain_id`: 事件在哪条链发生的。
    
-   `_contract`: 哪个合约喊的话。
    
-   `topic_0`: **核心指纹**（事件名的哈希，如 `Transfer`）。
    
-   `topic_1`~`topic_3`: 索引后的参数（如 `tokenId`, `from`, `to`）。
    
-   `data`: 非索引的详细数据。
    
-   `tx_hash`: 用于回溯原始交易的唯一哈希。
    

### 2\. 执行入口：`react()` 函数

Solidity

```
function react(LogRecord calldata log) external;
```

这是你的“大脑”。你在这里写 `if (log.topic_0 == ...)` 来判断捕捉到的是不是你要找的那个“信号”。

### 3\. 指令发出：`Callback` 事件

Solidity

```
event Callback(uint256 indexed chain_id, address indexed _contract, uint64 indexed gas_limit, bytes payload);
```

当你在 `react()` 里跑完逻辑，需要去别的链干活时，你就 `emit Callback`。Reactive Network 看到这个事件，就会自动帮你在目标链下单。

* * *

## 🛡️ 五、 安全铁律：回调验证与地址替换

### 1\. 环境隔离

**规则**：如果起源地是主网，目的地也必须是主网。

-   原因：防止黑客在零成本的测试网上伪造“大额转账”信号，去主网套取真实资产。
    

### 2\. 地址替换规则 (Authorization)

为了确保安全，Reactive Network 在执行回调时有一条硬性规定：

-   **自动替换**：网络会自动将回调 `payload`（指令包）的前 160 位（第一个 address 参数）替换为该 RSC 部署者的地址（RVM ID）。
    
-   **代码实现**：这意味着你的目标链函数第一个参数**必须**是 `address` 类型，且接收到的是调用者的合法身份。
    

* * *

## 📝 六、 实战案例：自动止损 (Stop-Loss)

将以上知识串联起来。假设你要实现“当 Uniswap 价格跌破阈值时自动卖币”：

Solidity

```
// 在 RSC 的 react 函数中构建 payload
bytes memory payload = abi.encodeWithSignature(
    "stop(address,address,address,bool,uint256,uint256)",
    address(0),  // 占位符：网络会自动替换成合法的 ReactVM 地址
    pair,        // 监控的交易对
    client,      // 用户地址
    token0,      // 代币地址
    coefficient, // 策略参数
    threshold    // 价格阈值
);

// 发出回调指令
emit Callback(chain_id, stop_order_contract, CALLBACK_GAS_LIMIT, payload);
```

### 总结

1.  **Event** 是信号发射源。
    
2.  **IReactive** 是信号接收器。
    
3.  **react()** 是逻辑处理器。
    
4.  **Callback** 是最终执行指令。
    

Q：为什么普通合约不能起到RC一样的效果

A：

**1\. 监测层（Reactive Network 的“眼睛”）**

-   **动作：** 响应式网络（Reactive Network）在底层协议级不断地扫描所有连接的 EVM 链（如以太坊、BSC 等）发出的事件日志。
    
-   **区别：** 普通合约没有这种“眼睛”，它看不见外面的世界。而响应式网络会根据你合约里定义的订阅条件，主动把匹配的事件抓取回来。
    

### 2\. 执行层（ReactVM 的“大脑”）

-   **动作：** 一旦“眼睛”看到了你感兴趣的事件，网络会自动触发你合约中的 `react()` 函数。
    
-   **运行环境：** RC 并不是运行在普通的以太坊节点上，而是运行在 **ReactVM**（响应式虚拟机）中。
    
-   **关键点：** 这笔调用 `react()` 的交易是由**响应式网络节点**自动发起的，不需要你手动点钱包。
    

### 3\. 反馈层（Callback 的“手”）

-   **动作：** 当你在 `react()` 逻辑里执行完判断，发出 `emit Callback` 事件时，响应式网络会立刻捕捉到这个指令。
    
-   **执行：** 网络会自动跨链，在目标链（Destination Chain）上发起一笔真实的交易来执行最终结果（比如卖币或发空投）。
<!-- DAILY_CHECKIN_2026-03-10_END -->
<!-- Content_END -->
