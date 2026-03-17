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
# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->
**3月17日 (周二)：Reactive 合约核心：订阅与生命周期**

-   **目标**：理解 Reactive 合约的双状态，并完成订阅逻辑。
    
-   **任务**：
    
    1.  **理论学习**：阅读 [Dev Guide](https://dev.reactive.network/)，重点理解 Reactive 合约如何在 Reactive Network（同步）与 ReactVM（私有状态）中存在。
        
    2.  **编写 Reactive 合约**：
        
        -   在 `constructor` 中调用系统合约（地址固定为 `0x...ffff`）的 `subscribe()` 方法。
            
        -   配置过滤维度：`chainId`（Origin 链 ID）、`originContract`、`topic0`。
            
    3.  **理解隔离**：明确 Reactive 合约的状态更新与普通 EVM 合约的区别。
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->

前端：

-   能连接钱包并切换到：Origin 链与 Lasna 链
    
-   做一个最小交互：能够触发 Origin 事件
    
-   能够展示 Reactive 三段链路的时间线（Origin- Reactive- Destination）
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->


**挑战任务与经济模型复盘**

**今日重点：** 完成 Notion 挑战，并理解“谁来付钱”。

• **挑战任务执行：**

• 按照 [挑战指引](https://ethpanda.notion.site/311bbd63be87809f9410c6fe8f8daff5?pvs=73) 完成特定的合约部署或逻辑改写。

• **建议：** 尝试修改 Stop Order 的逻辑，比如改成一个“价格上涨 10% 自动加仓”的 Trend Following 策略。

• **经济模型 (Economy)：**

• 理解 **Gas 消耗**：Reactive Network 的计算需要 Gas，目标链的回调也需要 Gas。

• 理解 **RVM Token** 的角色（如果有）：它是如何激励节点进行持续监听和执行的。

• **资料补充：**[**经济模型文档**](https://dev.reactive.network/economy)
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->



**代码拆解 —— 以 Uniswap V2 Stop Order 为例**

**今日重点：** 结合 GitHub 源码，看技术理论如何变成逻辑代码。

• **源码分析对象：**[**Stop Order Demo**](https://github.com/Reactive-Network/reactive-smart-contract-demos/tree/main/src/demos/uniswap-v2-stop-order)

• **核心逻辑拆解：**

1\. **构造函数：** 设定要监控的 Pair 合约地址。

2\. **onEvent 函数：** 这是核心。当监听到 Sync 事件（价格变动）时，计算当前价格。

3\. **逻辑判断：** if (currentPrice <= stopPrice)。

4\. **执行回调：** 调用 emitCallback，自动在 Uniswap 执行卖出操作。

• **动手尝试：** 尝试在本地环境（或 Remix）中模拟这段代码的触发条件，理解 Reactive Library 提供的接口。
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->




2026.03.13

## ReactVM 双状态模型与执行逻辑

**今日重点：** 理解 Reactive Network 的“大脑”是如何工作的，以及它为什么快。

-   **ReactVM 双状态模型：**
    
    -   **Global State（全局状态）：** 记录所有链（如 Ethereum, Polygon）同步过来的原始数据。
        
    -   **Local State（局部状态）：** 你的 Reactive Contract 运行时的私有状态。
        
-   **执行流 (The Pipeline)：**
    
    1.  **Catch：** 捕获源链的 Event Log。
        
    2.  **Process：** 在 ReactVM 中运行逻辑判断（是否满足触发条件）。
        
    3.  **Emit：** 生成一个 Callback 交易。
        
-   **跨链与自动化机制：**
    
    -   Reactive Network 本身不存储资产，它充当的是**跨链指挥官**。它通过私钥分片或中继机制，在目标链上代表你发起交易。
        
-   **学习资料：**[**ReactVM 深度解析**](https://dev.reactive.network/reactvm)
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->





## Day 3：挑战任务实战（终极目标）

**核心点：按照 Notion 指引完成跨链自动化**

要完成的挑战通常包含三个角色：

1.  **Origin Contract（源合约）：** 在 Sepolia 上发出一个 Log（比如 `emit Ping()`）。
    
2.  **Reactive Contract（睿应合约）：** 部署在 Lasna 上。它订阅了 `Ping` 事件，一旦抓到，就执行逻辑。
    
3.  **Destination Contract（目标合约）：** 在 Sepolia 或其他链上。当 Reactive 合约抓到 `Ping` 后，它会跨链调用这个合约的 `pong()` 函数。
    

### 挑战任务 Tips：

-   **部署顺序：** 先部署目标合约 $\\rightarrow$ 再部署 Reactive 合约（并订阅目标） $\\rightarrow$ 最后触发源合约。
    
-   **调试：** 如果没反应，检查 `topic0` 是否匹配（事件签名的哈希值）。
<!-- DAILY_CHECKIN_2026-03-11_END -->

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
