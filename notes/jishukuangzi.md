---
timezone: UTC+8
---

# jishukuangzi

**GitHub ID:** jishukuangzi

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->
打卡  
打卡
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->


打卡
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->



学习ing
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->




今日在学习foundry框架
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->





## 操作步骤与学习笔记 — Uniswap V2 止损订单 Demo

### 1️⃣ 准备环境

-   **工具**：MetaMask 钱包、Hardhat、Node.js、Ethers.js 或 Web3.js
    
-   **网络**：选择测试网（如 Goerli）
    
-   **依赖**：
    
    ```
    git clone https://github.com/ReactiveContracts/demos.git
    cd demos/uniswap-v2-stop-loss
    npm install
    ```
    
-   **笔记**：
    
    -   Reactive Contract（RC）是可以对链上事件主动做出响应的智能合约。
        
    -   止损订单 Demo 模拟交易者设置一个触发价格，当价格低于触发价时，自动卖出以防损失。
        

* * *

### 2️⃣ 部署合约

-   **Hardhat 部署命令**：
    
    ```
    npx hardhat run scripts/deploy.js --network goerli
    ```
    
-   **笔记**：
    
    -   部署脚本会创建一个 StopLossManager 合约实例，管理所有止损订单。
        
    -   注意记录合约地址和部署者钱包地址。
        

* * *

### 3️⃣ 注资

-   **操作**：
    
    1.  用测试网 ETH 或 USDC 向合约注资（例如：0.1 ETH）。
        
    2.  合约内部会记录用户的订单和对应的资产。
        
-   \*\*示例 Ethers.js：
    
    ```
    const tx = await stopLossManager.deposit({ value: ethers.utils.parseEther("0.1") });
    await tx.wait();
    ```
    
-   **笔记**：
    
    -   注资步骤非常关键：RC 会监控这些资金，并在触发条件下操作。
        
    -   仔细确认 token 地址和数量，防止错发。
        

* * *

### 4️⃣ 设置止损订单

-   **操作**：
    
    ```
    await stopLossManager.createStopLossOrder(
      tokenAddress,          // 要卖出的代币地址
      ethers.utils.parseUnits("0.05", 18),  // 卖出数量
      ethers.utils.parseUnits("1500", 18)   // 触发价格
    );
    ```
    
-   **笔记**：
    
    -   订单会被存储在合约的 mapping 中。
        
    -   RC 监听 Uniswap V2 的价格变动事件。
        

* * *

### 5️⃣ 验证场景

-   **触发条件**：
    
    -   让市场价格低于设置的触发价（可以在测试网模拟价格变化，或者手动调低）。
        
-   **观察**：
    
    -   合约自动执行交易，将用户的代币兑换成 ETH。
        
    -   可以在 Etherscan 测试网查看交易记录。
        
-   **笔记**：
    
    -   看到交易成功执行，就是 **“享受那一点多巴胺”** 的时刻 😄
        
    -   验证完整流程：
        
        1.  注资 → 2. 设置止损 → 3. 触发条件 → 4. 自动交易 → 5. 查看结果
            

* * *

### 6️⃣ 额外笔记

-   **安全性**：
    
    -   RC 依赖 Chainlink 或 Uniswap 的价格数据，要保证 oracle 数据可靠。
        
    -   部署在主网前应仔细审计。
        
-   **可拓展性**：
    
    -   可以增加多资产支持、多条件触发。
        
    -   可以和 Liquidation Protection Demo 结合，实现更复杂的自动化策略。
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->






# eactive Contracts 跨链开发学习笔记

## 一、Reactive Contracts 的核心概念

在本模块中，我学习了如何利用 **Reactive Contracts** 构建跨链功能。  
Reactive Contracts 的核心思想是：**智能合约可以自动监听链上的事件，并根据事件触发新的交易。**

其基本执行流程可以理解为：

```
event（事件） → RC 逻辑处理 → callback transaction（回调交易）
```

即：

1.  某条链上的合约触发 **event**
    
2.  Reactive Contract 捕获该事件并执行逻辑
    
3.  最终在另一条链上触发 **callback transaction**
    

这种机制使得 **不同区块链之间可以通过事件驱动的方式进行交互**。

* * *

# 二、Reactive Network 的核心能力

## 1 跨链执行（Cross-chain Execution）

Reactive Contracts 可以实现跨链交互流程：

1.  监听某条链上的合约事件
    
2.  执行逻辑处理
    
3.  在另一条链上触发新的交易
    

这种机制可以用来构建：

-   跨链桥
    
-   跨链借贷
    
-   跨链预言机
    
-   跨链自动化交易系统
    

这些应用通常部署在 \*\*Ethereum 等 EVM 兼容链上。

* * *

## 2 链上自动化（On-chain Automation）

Reactive Network 允许智能合约 **自动执行链上任务**，不需要依赖链下机器人。

传统自动化系统通常依赖：

-   off-chain bot
    
-   定时脚本
    
-   centralized server
    

而 Reactive Contracts 可以实现：

-   自动监听事件
    
-   自动触发交易
    
-   自动执行逻辑
    

典型应用包括：

-   自动收取多个流动性池的手续费
    
-   自动止损 / 止盈交易
    
-   清算保护
    
-   周期性资金再平衡
    
-   自动资金归集
    

这使得 DeFi 系统可以更加 **去中心化和自动化**。

* * *

## 3 模块化扩展（Modularity）

Reactive Contracts 还支持 **无需修改原合约即可扩展功能**。

在传统开发中，如果想为某个协议增加功能，通常需要：

-   修改原合约
    
-   或 fork 代码
    

而 Reactive Contracts 提供另一种方式：

**订阅已有合约的事件并作出响应。**

例如：

在 \*\*Uniswap 的流动性池中实现止损订单。

开发者不需要修改 Uniswap 的合约，而是：

1.  订阅 Uniswap 的交易事件
    
2.  监控价格变化
    
3.  当达到止损条件时自动触发交易
    

这种模式大大提高了系统的 **可扩展性和模块化能力**。

* * *

# 三、Reactive Network 的技术组成

在学习过程中，我了解了 Reactive Network 的关键技术组件：

### 1 ReactVM

Reactive Network 使用 **ReactVM** 执行 Reactive Contracts 的逻辑。

ReactVM 的作用是：

-   监听链上事件
    
-   执行响应逻辑
    
-   触发跨链回调交易
    

* * *

### 2 Event Subscriptions

Reactive Contracts 可以 **订阅链上事件**。

当指定事件发生时：

-   RC 会自动捕获日志
    
-   解析事件数据
    
-   执行对应逻辑
    

这种机制类似于：

```
区块链事件监听系统
```

* * *

### 3 Callback Transactions

当 RC 处理完事件后，会在目标链上发起 **callback transaction**。

callback 的作用是：

-   调用目标链上的合约函数
    
-   完成跨链逻辑执行
    

例如：

```
Chain A event
      ↓
Reactive Contract
      ↓
Chain B callback
```

* * *

# 四、DeFi 场景应用

在课程中还学习了 Reactive Contracts 在 DeFi 场景中的应用，例如：

### 1 止损订单（Stop Orders）

通过订阅 DEX 交易事件，可以实现：

-   自动监控价格
    
-   达到价格阈值时自动卖出资产
    

这类似于传统交易所中的 **止损单**。

* * *

### 2 清算保护（Liquidation Protection）

在借贷协议中：

-   当抵押率下降时
    
-   系统可以自动执行补仓或还款操作
    

Reactive Contracts 可以帮助实现：

-   自动检测风险
    
-   自动执行保护交易
    

* * *

# 五、实践任务

本模块的实践任务是：

从 **Reactive Smart Contract demos** 仓库中选择一个示例，并在测试网上运行。

可选 Demo 包括：

-   基于 \*\*Uniswap V2 的止损订单
    
-   清算保护系统
    

任务步骤包括：

1.  在测试网部署智能合约
    
2.  为合约注入资金
    
3.  触发对应事件
    
4.  验证整个流程成功执行
    

完整流程包括：

```
部署合约
↓
监听事件
↓
触发事件
↓
Reactive Contract 执行
↓
callback transaction
↓
验证执行结果
```

* * *

# 六、开发工具

本项目使用的主要开发工具是：

-   Foundry
    

Foundry 用于：

-   编译 Solidity 合约
    
-   测试合约
    
-   部署合约
    
-   与区块链交互
    

部署时需要配置多个环境变量，例如：

-   ORIGIN RPC
    
-   DESTINATION RPC
    
-   REACTIVE RPC
    
-   private keys
    
-   callback 地址
    

这些参数用于连接不同链并完成跨链调用。

* * *

# 七、学习收获总结

通过本模块的学习，我主要理解了以下几个关键点：

1.  Reactive Contracts 可以通过 **监听链上事件并触发回调交易** 来实现跨链交互。
    
2.  Reactive Network 提供了一种 **无需链下机器人即可自动执行链上任务** 的机制。
    
3.  通过订阅事件，可以在 **不修改原合约的情况下扩展已有协议的功能**。
    
4.  Reactive Contracts 在 DeFi 场景中可以实现多种自动化功能，例如止损交易和清算保护。
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->







# Reactive Network 项目学习笔记（学习收获）

通过完成 Reactive Network 项目的实践，我对 **跨链事件系统、Reactive Smart Contracts 以及事件驱动的区块链交互机制**有了更加深入的理解。本次学习主要获得了以下几方面的知识。

* * *

# 一、理解了 Reactive Network 的事件驱动架构

在学习这个项目之前，我对区块链应用的理解主要停留在 **用户发送交易 → 智能合约执行逻辑** 的传统模式。

通过这个项目，我了解到 Reactive Network 采用的是一种 **事件驱动（Event-driven）架构**。在这种模式下，系统可以通过监听链上事件来自动触发后续操作，而不需要用户再次手动发起交易。

具体流程可以理解为：

源链触发事件 → Reactive Network 监听事件 → Reactive 合约处理事件 → 目标链执行回调操作。

这种机制使得区块链应用可以实现更加 **自动化和实时化的交互逻辑**。

* * *

# 二、学习了跨链事件触发与回调机制

在本次项目实践中，我理解了 **跨链事件触发和回调的完整流程**。

当源链上的合约产生事件日志时，Reactive Network 会对这些日志进行监听，并将相关数据传递给 Reactive Smart Contract。Reactive 合约在接收到事件数据后，会根据预设条件进行判断，如果满足条件，就会触发一个跨链回调，在目标链上调用指定合约的函数。

通过这一过程，我理解了 **跨链交互并不一定需要用户直接在多条链上操作，而是可以通过事件监听和自动回调来完成**。

这种机制为构建 **跨链自动化应用**提供了新的思路。

* * *

# 三、理解了 Reactive Smart Contract 的作用

在项目中，我学习了 Reactive Smart Contract 的基本功能和作用。

Reactive Smart Contract 的主要职责包括：

1.  订阅源链上的事件日志
    
2.  解析事件中的数据
    
3.  根据预设条件判断是否触发操作
    
4.  生成跨链回调请求
    

可以把 Reactive 合约理解为一种 **自动化的逻辑处理组件**，它能够根据链上发生的事件自动执行相关逻辑，从而实现不同区块链之间的联动。

* * *

# 四、学习了如何通过事件订阅实现链上监控

在项目实践中，我学习到了 **事件订阅（Event Subscription）机制**。

智能合约可以通过事件（Event）记录链上行为，例如交易信息、用户地址和金额等。Reactive Network 可以监听这些事件日志，并根据指定的 topic 过滤需要关注的事件。

通过这种方式，系统能够实现对链上行为的 **实时监控和响应**。

这种机制在很多应用场景中都非常重要，例如：

-   自动交易系统
    
-   风险监控系统
    
-   跨链资产管理
    

* * *

# 五、理解了跨链回调的安全设计

在目标链的回调合约中，我学习到了如何通过 **权限验证机制** 来保证跨链调用的安全性。

回调合约会验证调用者是否来自授权的 Reactive Network 服务地址，从而防止未授权的地址调用回调函数。这种设计可以确保跨链操作不会被恶意利用。

通过这一部分学习，我进一步理解了 **跨链交互中安全控制的重要性**。

* * *

# 六、掌握了多链环境的部署流程

在项目实践过程中，我还学习了如何在多链环境中部署智能合约。整个系统涉及三种不同的网络：

1.  源链（Origin Chain）
    
2.  Reactive Network
    
3.  目标链（Destination Chain）
    

部署流程包括：

1.  部署源链合约用于产生事件
    
2.  部署目标链回调合约用于接收跨链调用
    
3.  部署 Reactive 合约用于监听事件并触发回调
    

通过这一过程，我对 **跨链系统的整体架构和部署方式**有了更加清晰的认识。

* * *

# 七、对 Reactive Network 应用场景的理解

通过本次项目实践，我还了解到 Reactive Network 可以应用在许多自动化区块链系统中，例如：

-   去中心化自动交易策略
    
-   跨链资产管理系统
    
-   自动清算机制
    
-   DAO 自动执行决策
    

Reactive Network 提供了一种 **基于事件触发的自动化执行机制**，能够显著提升区块链应用的自动化程度。

* * *

# 八、总结

通过本次 Reactive Network 项目的学习，我对 **事件驱动型区块链架构和跨链回调机制**有了更加系统的理解。

我不仅学习了如何监听链上事件和解析日志数据，还掌握了如何通过 Reactive Smart Contracts 在不同区块链之间实现自动化交互。同时，我也理解了跨链回调的安全设计以及多链部署流程。

整体而言，这次学习让我认识到 **区块链应用不仅可以通过用户交易驱动，还可以通过链上事件实现自动化响应和跨链协作**，为构建更加复杂和智能的 Web3 应用提供了新的思路。
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->








### **一、睿应层课程概述**

-   主题：响应式合约（RCs）基础及技术概念
    
-   目标：
    
    1.  理解 RCs 与传统智能合约的区别
        
    2.  掌握控制反转（Inversion of Control）概念
        
    3.  了解 RCs 如何自主监控区块链事件并执行操作
        
    4.  探索 RCs 的实际应用场景
        

* * *

### **二、RCs 与传统智能合约的区别**

| 特性 | 传统智能合约 | 响应式合约 (RCs) |
| --- | --- | --- |
| 执行方式 | 被动，需要外部账户调用 | 主动，持续监控并自主执行 |
| 触发条件 | 外部交易（EOA调用） | 区块链事件或状态变化 |
| 控制权 | 在调用者手中 | 控制权在合约自身（IoC） |
| 应用场景 | 基础逻辑操作（如转账） | 自动化交易、数据收集、套利、再平衡等 |

* * *

### **三、关键概念**

1.  **响应性（Reactivity）**
    
    -   RC 能持续监听区块链事件，并在条件满足时自动执行操作。
        
    -   思路：观察 → 判断 → 执行。
        
2.  **控制反转（Inversion of Control, IoC）**
    
    -   控制权从外部调用者转向合约自身。
        
    -   合约自主决定何时执行逻辑，而不依赖外部交易触发。
        
3.  **事件监控与自动响应**
    
    -   订阅区块链事件或状态变化（如价格更新、交易完成、流动性变化）。
        
    -   根据预设规则自动执行动作（下单、套利、重平衡等）。
        

* * *

### **四、实际应用场景**

1.  **数据收集**：从预言机获取链外数据，更新链上状态
    
2.  **UniSwap 停止订单**：价格达到阈值自动买卖
    
3.  **DEX 套利**：发现价格差异时自动套利
    
4.  **资金池再平衡**：自动调整池中资产比例
    

* * *

### **五、理解小结**

-   RC = 传统合约 + 主动性 + 自动事件响应
    
-   关键点：**主动监控 + 控制反转 + 自动执行规则**
    
-   适合**需要实时决策和自动化操作的场景**  
    

## 一、学习目标（构建一个跨链事件与回调合约）

本次学习主要围绕 **Reactive Network 的跨链事件机制**，目标包括：

1.  学习如何使用 **Reactive Network** 构建跨链事件系统。
    
2.  理解如何在 **一个区块链触发事件**，并通过 **Reactive Smart Contracts (RSCs)** 在 **另一条链上执行回调操作**。
    
3.  学习如何利用 **合约继承、事件机制以及跨链回调** 来实现链上应用之间的交互。
    

* * *

# 二、Reactive Network 基本概念

**Reactive Network** 是一种用于构建 **事件驱动型区块链应用** 的网络。

传统智能合约通常是：

> 用户发送交易 → 合约执行逻辑

而 Reactive Network 的核心思想是：

> **事件触发 → 自动执行响应逻辑**

也就是说：

某个链上的 **事件(Event)** 可以自动触发另一个合约的 **回调函数(callback)**。

这种模式叫：

**Reactive Programming（响应式编程）**

* * *

# 三、跨链事件系统的工作流程

Reactive Network 可以实现 **跨链事件响应机制**，基本流程如下：

### Step 1：源链触发事件

在 **源链（Source Chain）** 上执行某个操作，例如：

-   转账
    
-   NFT Mint
    
-   合约调用
    

合约会 **发出一个事件（Event）**。

例如：

```
emit TransferTriggered(user, amount);
```

* * *

### Step 2：Reactive Network 监听事件

Reactive Network 会：

-   监听链上的事件
    
-   识别符合条件的事件
    
-   将事件信息发送到 Reactive 合约
    

* * *

### Step 3：Reactive Smart Contract 处理事件

Reactive Smart Contract（RSC）会：

-   接收事件数据
    
-   执行预设逻辑
    
-   触发回调函数
    

* * *

### Step 4：目标链执行回调

RSC 会在 **目标链（Target Chain）** 上调用某个函数。

例如：

```
mintReward(user, amount);
```

最终实现：

**源链事件 → 目标链操作**

* * *

# 四、Reactive Smart Contracts (RSCs)

**Reactive Smart Contracts** 是 Reactive Network 的核心组件。

它的作用是：

> 根据链上事件自动执行逻辑。

主要功能包括：

1.  监听事件
    
2.  解析事件数据
    
3.  执行回调函数
    
4.  触发跨链操作
    

RSC 相当于：

**区块链里的自动化机器人（Event Automation Agent）**

* * *

# 五、合约继承在系统中的作用

在 Reactive Network 开发中，合约通常会使用 **继承（Inheritance）**。

继承的作用包括：

1.  **复用代码**
    

基础 Reactive 合约已经提供：

-   事件监听
    
-   回调接口
    
-   网络通信
    

开发者只需要继承这些基础合约即可。

例如：

```
contract MyReactiveContract is ReactiveBaseContract
```

* * *

2.  **扩展功能**
    

开发者可以在子合约中添加：

-   自定义事件处理逻辑
    
-   自定义回调函数
    
-   跨链交互逻辑
    

* * *

# 六、事件（Event）机制

在 Solidity 中，**Event** 用于记录链上行为。

示例：

```
event TransferTriggered(address user, uint256 amount);
```

当事件被触发时：

```
emit TransferTriggered(msg.sender, amount);
```

事件的作用：

1.  记录交易日志
    
2.  供外部系统监听
    
3.  触发 Reactive Network 的响应机制
    

* * *

# 七、跨链回调机制

Reactive Network 通过 **跨链回调（Cross-chain Callback）** 实现不同区块链之间的交互。

基本原理：

1.  **链A触发事件**
    
2.  **Reactive Network监听事件**
    
3.  **Reactive合约接收事件**
    
4.  **在链B执行回调函数**
    

例如：

```
Source Chain: Event Triggered
      ↓
Reactive Network Listener
      ↓
Reactive Smart Contract
      ↓
Target Chain Callback
```

* * *

# 八、技术优势

Reactive Network 的跨链事件机制具有以下优势：

### 1\. 自动化执行

无需人工操作，事件发生后系统自动执行。

* * *

### 2\. 跨链交互

可以连接多个区块链，实现跨链应用。

* * *

### 3\. 模块化开发

通过 **合约继承**，开发者可以快速构建复杂系统。

* * *

### 4\. 事件驱动架构

相比传统智能合约：

```
用户交易 → 合约执行
```

Reactive 模式：

```
事件发生 → 自动触发逻辑
```

更适合自动化应用。

* * *

# 九、总结

本次学习主要理解了 **Reactive Network 的跨链事件机制**：

1.  Reactive Network 采用 **事件驱动架构**。
    
2.  当某条链上触发 **Event** 时，Reactive Network 会监听该事件。
    
3.  **Reactive Smart Contracts (RSCs)** 会接收事件并执行逻辑。
    
4.  最终可以在 **另一条链上执行回调函数**，实现跨链交互。
    

通过 **合约继承、事件机制和跨链回调**，开发者可以构建复杂的 **跨链自动化应用系统**。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->









### **一、课程概述**

-   主题：响应式合约（RCs）基础及技术概念
    
-   目标：
    
    1.  理解 RCs 与传统智能合约的区别
        
    2.  掌握控制反转（Inversion of Control）概念
        
    3.  了解 RCs 如何自主监控区块链事件并执行操作
        
    4.  探索 RCs 的实际应用场景
        

* * *

### **二、RCs 与传统智能合约的区别**

| 特性 | 传统智能合约 | 响应式合约 (RCs) |
| --- | --- | --- |
| 执行方式 | 被动，需要外部账户调用 | 主动，持续监控并自主执行 |
| 触发条件 | 外部交易（EOA调用） | 区块链事件或状态变化 |
| 控制权 | 在调用者手中 | 控制权在合约自身（IoC） |
| 应用场景 | 基础逻辑操作（如转账） | 自动化交易、数据收集、套利、再平衡等 |

* * *

### **三、关键概念**

1.  **响应性（Reactivity）**
    
    -   RC 能持续监听区块链事件，并在条件满足时自动执行操作。
        
    -   思路：观察 → 判断 → 执行。
        
2.  **控制反转（Inversion of Control, IoC）**
    
    -   控制权从外部调用者转向合约自身。
        
    -   合约自主决定何时执行逻辑，而不依赖外部交易触发。
        
3.  **事件监控与自动响应**
    
    -   订阅区块链事件或状态变化（如价格更新、交易完成、流动性变化）。
        
    -   根据预设规则自动执行动作（下单、套利、重平衡等）。
        

* * *

### **四、实际应用场景**

1.  **数据收集**：从预言机获取链外数据，更新链上状态
    
2.  **UniSwap 停止订单**：价格达到阈值自动买卖
    
3.  **DEX 套利**：发现价格差异时自动套利
    
4.  **资金池再平衡**：自动调整池中资产比例
    

* * *

### **五、理解小结**

-   RC = 传统合约 + 主动性 + 自动事件响应
    
-   关键点：**主动监控 + 控制反转 + 自动执行规则**
    
-   适合**需要实时决策和自动化操作的场景**
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->










## 1️⃣ 理解 Reactive 与传统 EVM 的根本区别

-   **传统 EVM 智能合约**：
    
    -   合约是“被动”的：它只在交易调用时执行（transaction-driven）。
        
    -   外部事件或条件不会主动触发合约逻辑，你必须显式发送交易来改变状态。
        
    -   比如 ERC20 的 `transfer`，必须有人发交易才会执行。
        
-   **Reactive 智能合约**：
    
    -   合约是“主动”的：它可以订阅事件，一旦事件发生，自动触发对应逻辑（event-driven）。
        
    -   执行逻辑与事件解耦，形成 **Event → Reactive Logic → Callback Transaction** 链路。
        
    -   类似前端的 React：状态变了（事件来了），系统自动响应（执行逻辑）
        

## 2️⃣ 核心架构：ReactVM + Reactive Network

-   **ReactVM**：
    
    -   类似 EVM，但加入了事件监听和调度机制。
        
    -   支持在链上或链下处理事件逻辑（可按需选择同步/异步处理）。
        
    -   执行逻辑的方式不再依赖主动交易触发，而是基于事件订阅。
        
-   **Reactive Network**：
    
    -   提供事件分发、状态同步、Callback Transaction 发送的基础设施。
        
    -   负责把链上事件传递给 Reactive Logic，并把逻辑执行结果发回链上。
        

**画一个简化链路：**链上事件 (Event) → Reactive Network 接收→ Reactive Logic 处理→ 生成 Callback Transaction→ 链上执行 → 更新状态  

## 3️⃣什么是事件驱动智能合约（Reactive Smart Contracts）

**定义**：合约逻辑不再只是响应交易，而是响应链上或链下事件，自动执行。

-   **特点**：
    
    1.  **自动化响应**：事件触发逻辑，不需要用户主动调用。
        
    2.  **可组合**：多个事件可以绑定不同逻辑，形成复杂业务流程。
        
    3.  **链下计算支持**：一些逻辑可以在链下执行，提高效率。  
        

**应用场景示例**：

-   NFT 盲盒开箱：购买事件触发生成随机 NFT。
    
-   DeFi 风控：价格波动事件触发清算逻辑。
    
-   DAO 自动管理：投票事件触发资金分配。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
