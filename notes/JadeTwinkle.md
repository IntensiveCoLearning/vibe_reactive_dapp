---
timezone: UTC+8
---

# Jade

**GitHub ID:** JadeTwinkle

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->
# Reactive Network适合的应用场景

最适合 6 类应用：

### 1 DeFi 自动策略

例如：

-   自动清算
    
-   自动收益复投
    
-   自动再平衡
    

* * *

### 2 交易触发器

类似：

```
如果 BTC > 80000
自动买入
```

类似：

Web2 的 **stop loss / take profit**。

* * *

### 3 DAO 自动治理

例如：

```
提案通过
→ 自动执行 treasury 资金
```

无需人工操作。

* * *

### 4 NFT 动态逻辑

例如：

```
游戏角色升级
→ 自动更新 NFT metadata
```

* * *

### 5 自动支付 / 订阅

例如：

```
每月
自动支付服务费
```

* * *

### 6 跨链自动操作

例如：

```
Solana上NFT卖出
→ Ethereum自动分账
```

* * *

# 三、为什么传统 EVM 做不到？

传统 **Ethereum Virtual Machine** 是：

**Transaction-driven**

流程：

```
用户交易
→ 执行合约
→ 状态改变
```

Reactive Network：

**Event-driven**

流程：

```
链上事件
→ Reactive layer监听
→ 自动触发执行
```

这就是：

**Reactive Smart Contracts**

* * *

# 四、一个非常直观的类比

传统区块链：

```
按钮 → 才执行
```

Reactive Network：

```
条件满足 → 自动执行
```

类似：

| Web2 | Web3 |
| --- | --- |
| Zapier | Reactive Network |
| IFTTT | Reactive smart contracts |

* * *

# 五、总结

**Reactive Network = Web3 的 “IFTTT 自动化层”**

```
If X happens on-chain
Then execute contract Y下周的TODO：
```

# 六、下周TODO

-   理解 Reactive Contract 的组成结构
    
-   理解 Subscribe / Trigger / Callback 模型
    
-   理解 ReactVM 执行逻辑
    
-   理解跨链和自动化机制
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->

# 使用 AI 构建 Reactive Contracts (跨链智能合约)

## 一、 核心心法：AI 时代的开发者定位

-   **AI 是增强工具，而非替代品**：AI 能提升开发速度和代码质量，但**无法替代开发者对现实世界业务逻辑的理解**
    
-   **终极目标是“学习”**：在 Hackathon 或早期项目中，不要只追求“把东西建出来然后忘掉”，而是要通过阅读和验证 AI 生成的代码来提升自己的技术深度
    
-   **人类的独特价值在于“原创性”**：AI 擅长搜索、编译和解释，但不擅长提出全新的架构思路（例如讲座中提到的应对区块重组的“Ping-pong”机制，就是 CTO 原创的思路，而非 AI 提出的）
    

## 二、 推荐工具栈

-   **首选 AI**：**Claude AI** (讲座团队目前认为其在代码生成上表现最佳)
    
-   _推荐用法_：使用 **VS Code 扩展 (CLI 命令行工具)**，如 Claude Code。它可以直接在本地仓库文件夹中创建和修改文件，免去了在网页端来回复制粘贴的繁琐
    
-   **备选 AI**：Cursor, GitHub Copilot, ChatGPT, DeepSeek 。 (注：讲者认为 Gemini 目前在编程能力上可能不如 Claude )
    
-   **开发环境**：推荐使用 **Foundry** 进行以太坊/EVM 智能合约开发
    

## 三、 标准 AI 结对编程工作流 (SOP)

这是本次讲座最核心的实操指南，建议在开发新功能时严格遵循：

1.  **第一步：让 AI 先写“架构计划”，绝不要直接写代码**
    

_Prompt 要点_：明确说明你要构建什么（如 USDC 跨链桥），并**强制要求包含具体技术栈**（如：“Make a plan for implementation using **Reactive Network**”）

_模型选择_：在规划阶段，使用最强大的模型（如 Claude 3.5 Sonnet 或 Opus），因为架构设计是最复杂的环节

2.  **第二步：极限施压与验证计划**
    

仔细阅读 AI 给出的架构设计（Origin Contract -> Reactive Contract -> Destination Contract）

针对漏洞提问：例如询问“如何防止双花 (Double spend)？”、“如何处理区块重组 (Reorgs)？”

多轮迭代，直到你完全理解并认可该计划，没有任何遗留疑问

3.  **第三步：生成代码与阅读审核**
    

确认计划无误后，再下达指令：“Ask Claude to implement the plan”

**必须通读所有智能合约代码**。如果遇到不懂的行，像请教人类导师一样让 AI 逐行解释

4.  **第四步：手动部署与调试 (Human-in-the-loop)**
    

目前不建议让 AI 自动执行部署代理

开发者需自己准备钱包、领水，并将合约部署到测试网（Ethereum Sepolia, Base Sepolia, Reactive Kopli）

将链上交易哈希或区块浏览器链接（Etherscan/Reactscan）发给 AI，让它帮你分析执行结果
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->


# Reactive Staking Season 的结构

Reactive 的 staking 不是永久池，而是 **Season 机制**：

```
Season 1
Season 2
Season 3
Season 4
Season 5
```

每个 Season：

-   有新的 **reward pool**
    
-   有新的 **lock period**
    
-   用户可以 **restake**
    

例如之前 Season：

-   30天池
    
-   60天池
    
-   90天池
    

奖励来自同一个 pool。

特点：

-   锁仓越久 → 奖励越高
    
-   参与人数越多 → APY下降
    

因为奖励是 **按比例分配**。

* * *

# Season 5 的核心目标

Season 5 的设计主要有 **3个目标**。

### 1 提升网络安全性

Reactive 是 PoS 网络。

更多 staking → 更安全。

```
更多 token 被锁
      ↓
攻击成本更高
      ↓
网络更安全
```

* * *

### 2 提供长期流动性

如果 token 都在交易所：

```
价格波动大
卖压大
```

staking 可以：

```
减少 circulating supply
稳定市场
```

* * *

### 3 激励生态参与

Season staking 是一种：

**早期网络 bootstrap 机制**

很多区块链都这么做。

例如：

-   Avalanche
    
-   Polkadot
    

都会在早期提供较高 APY。

Reactive 初期 staking APY 曾达到 **约 15–35% 区间**
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->



# 一、Reactive Network 与传统 EVM 的根本区别

## 1️⃣ 传统 EVM：**请求驱动（Request-driven）**

在 **Ethereum** 及其他 EVM 链上：

**流程是：**

```
用户 → 发送交易 → 调用合约 → 合约执行
```

特点：

-   智能合约 **不会主动运行**
    
-   必须有人 **发送 transaction**
    
-   合约只是 **被动响应调用**
    

例如：

| 场景 | 触发方式 |
| --- | --- |
| Swap Token | 用户调用 DEX |
| Mint NFT | 用户调用 mint() |
| 清算 DeFi | 机器人调用清算函数 |

所以 EVM 世界里大量存在：

-   **bot**
    
-   **keeper**
    
-   **cron job**
    
-   **off-chain automation**
    

例如：

-   Chainlink Keepers
    
-   Gelato automation
    

本质是：

**链下服务在替合约做自动化。**

* * *

## 2️⃣ Reactive Network：**事件驱动（Event-driven）**

在 **Reactive Network** 中：

流程变成：

```
链上事件发生
      ↓
Reactive Contract监听事件
      ↓
自动执行逻辑
      ↓
触发新的交易
```

合约变成 **主动的自动化 agent**。

这就是：

**Reactive Smart Contract**

* * *

## 3️⃣ 关键区别总结

| 维度 | EVM | Reactive Network |
| --- | --- | --- |
| 执行模式 | 调用驱动 | 事件驱动 |
| 触发方式 | 用户 transaction | 链上事件 |
| 自动化 | 需要 bot / keeper | 链上原生自动化 |
| 逻辑位置 | 大部分在链下 | 更多在链上 |
| 合约角色 | 被动函数 | 主动 agent |

一句话：

**EVM 合约 = 被动 API**  
**Reactive 合约 = 自动机器人**
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->




今天是第一天！打算先来了解一下概念！

1.  学习如何使用 **Reactive Network** 构建跨链事件系统。
    
2.  理解如何在一个链上触发事件，并通过 **Reactive Smart Contracts (RSCs)** 在另一链上回调。
    
3.  学习如何利用合约继承、事件和跨链回调机制来创建链上交互。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
