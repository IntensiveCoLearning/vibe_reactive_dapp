---
timezone: UTC+8
---

# enderzcx

**GitHub ID:** enderzcx

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
打卡占位
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->

又是美好的一天
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->


# 睿应层（Reactive Network）学习整理

## 一、第一阶段：理解 Reactive Network 思维

### 1\. Reactive Network 和传统 EVM 的根本区别

传统 EVM 智能合约本质上是 **调用驱动** 的。也就是说，必须有人或某个程序主动发起交易，合约才会执行；如果没人调用，合约就不会自己运行。

Reactive Network 则是 **事件驱动** 的。开发者先定义要监听的链上事件，当这些事件发生后，Reactive Contract 会自动根据预设逻辑作出响应，而不是等待用户手动操作。官网将其概括为：Reactive Contracts 可以订阅支持的 EVM 链事件、用 Solidity 写条件逻辑，并在条件满足时自动触发跨链交易。

**一句话总结：**  
**传统 EVM = call-driven（调用驱动）**  
**Reactive Network = event-driven（事件驱动）**

* * *

### 2\. Reactive Network 的核心架构

Reactive Network 的核心可以先理解成这条链路：

**链上事件 → 订阅命中 → ReactVM 执行逻辑 → Callback / 后续动作**

其中关键组成是：

-   **Reactive Contract**：负责编写反应逻辑
    
-   **Subscriptions**：定义要监听哪些事件
    
-   **ReactVM**：执行 Reactive Contract 逻辑的运行环境
    
-   **Callback 机制**：把判断结果真正执行到目标链或目标合约上
    

文档说明，Reactive Contracts 并不是在普通 EVM 里直接以传统方式运行，而是在 **ReactVM** 这个隔离执行环境中处理收到的事件，并决定是否发出 callback。

* * *

### 3\. 什么是 reactive smart contracts

Reactive smart contracts（睿应式智能合约）可以理解为：

**一种能“感知链上事件”并“自动做出反应”的智能合约。**

它不是等别人来调用，而是先声明“我要监听什么”；一旦监听到匹配事件，就自动触发自己的逻辑。订阅文档中明确说明：订阅定义了 RC 要监听的事件，匹配事件出现后会触发合约的 `react()`。

**白话理解：**  
普通合约像“按钮式机器”，按下才工作。  
Reactive Contract 像“感应式机器”，检测到条件就自动工作。

* * *

### 4\. Reactive Network 适合解决什么问题

Reactive Network 适合处理 **自动化、条件触发、跨链协同** 这类场景，尤其适合那些不适合靠人持续盯盘、手动确认、手动发交易的任务。

典型场景包括：

-   自动化执行
    
-   止损 / 止盈
    
-   跨链响应
    
-   条件满足后的资产释放
    
-   多链事件联动
    
-   自动化 DeFi 策略
    

官网 ecosystem 案例里就覆盖了跨链借贷、自动交易、托管/里程碑付款、闪电贷协调等方向。

**核心原因：**  
它能在事件发生后自动响应，不依赖人工持续操作。

* * *

## 二、第二阶段：理解技术结构与开发模型

### 1\. Reactive Contract 的组成结构

Reactive Contract 至少要围绕三件事来理解：

-   **监听什么事件**
    
-   **事件发生后执行什么逻辑**
    
-   **满足条件后如何执行后续动作**
    

所以它不是单纯一段 Solidity 代码，而是一套完整的事件响应模型。文档把这套模型展开为订阅、触发、回调等模块。

* * *

### 2\. Subscribe / Trigger / Callback 模型

这是第二阶段最核心的主线。

Subscribe

**Subscribe = 先声明我要监听哪些事件。**

例如：

-   哪条链
    
-   哪个合约
    
-   哪种事件
    
-   哪些 topic 或参数
    

也就是先把“触发条件”定义好。订阅机制文档说明，Subscriptions 用来指定 Reactive Contract 要监听的目标事件。

Trigger

**Trigger = 被订阅的事件真的发生了。**

不是有人按按钮，也不是用户手动调用，而是“链上事件命中监听条件”，于是系统触发后续逻辑。

Callback

**Callback = 把反应逻辑的结果真正执行出去。**

它不是单纯通知，不只是“提醒一下有事发生”，而是把 Reactive Contract 的判断结果落实成后续链上动作，例如调用目标合约、发起后续交易等。events & callbacks 文档说明，Reactive Network 会根据 reactive logic 生成后续 callback 交易。

**一句话串起来：**  
**Subscribe → Trigger → Callback**  
\= **先订阅 → 事件命中 → 执行后续动作**

* * *

### 3\. ReactVM 的执行逻辑

ReactVM 可以理解为：

**Reactive Contract 的运行环境 / 执行虚拟机。**

普通智能合约主要运行在 EVM 中，而 Reactive Contract 的反应逻辑运行在 ReactVM 中。ReactVM 文档说明，它是隔离的执行环境，不同部署者地址映射到不同 ReactVM；各 ReactVM 可并行执行，而单个 ReactVM 内保持确定性。

**白话理解：**  
ReactVM 就像“反应逻辑处理器”。  
事件一旦命中订阅条件，Reactive Contract 就在 ReactVM 中运行 `react()` 逻辑，再决定是否发起 callback。

* * *

### 4\. 跨链和自动化机制

Reactive Network 的关键价值之一就是把“监听事件”和“执行后续动作”连接起来，因此很适合做跨链自动化。

整体过程可以理解为：

1.  在某条链上监听事件
    
2.  事件发生后由 ReactVM 执行逻辑
    
3.  逻辑满足条件时，通过 callback 把动作发往目标链或目标合约
    

官网和文档都强调，Reactive Contracts 的能力不止是“监听”，还包括自动触发跨链交易。

* * *

### 5\. 经济模型与运行成本

Reactive Contracts 要持续工作，不是完全“免费自动化”。文档中的经济模型说明，Reactive Contract 的活跃运行、事件处理以及 callback 执行都需要相应的经济支持。

主网文档中还提供了网络参数，例如 Reactive Mainnet 的 **chain id 为 1597**，并给出了系统合约等部署信息，测试网和水龙头信息也在开发文档中列出。

* * *

# 三、整套模型的最终总结

可以把 Reactive Network 归纳为下面这段话：

**Reactive Network 采用事件驱动模型。开发者先通过 Subscribe 订阅目标事件；当事件被触发后，Reactive Contract 会在 ReactVM 中执行反应逻辑；如果满足条件，就通过 Callback 把后续动作执行到目标合约或目标链上。**
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->




来了，开始残酷！不知道我都onchainclaw的reactive算不算创新点，能不能呗官方看上
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
