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
# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->
折腾了几天BSC的止盈止损还是没过，头疼
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->

\## Reactive Contracts 挑战作业提交

\### 1. 我选择的 Demo

我选择的是 `Uniswap V2 Stop Order Demo`。

\### 2. 部署地址 / 交易哈希

部署地址：

\- 钱包地址`0xeaBe63116b6d5A3510c2F101A97E67136a821Be5`

\- Uniswap V2 Pair`0xE8739A1372DE645dEaE7717A2067F4BefC1FB628`

\- Callback 合约`0x3b3a9e2cD939112d9ca7779Ec751A68bC6ebd255`

\- Reactive 合约`0xd11A1Dc23D5999e9640ea1029887bC60f2955005`

关键交易哈希：

\- Reactive 合约部署`0xe7c38e41b7aa263c6876c87083ec82b73f222d08fbc94a41480ee2428926b807`

\- 授权 token`0xbd3f7505f32ac1d34f1ab788797ed0556124ce230d852abacacc8e5ca81ac8aa`

\- 触发前转账`0x93af4e7a0ccb5932bcc7048c8e5c8f1f14ce911d61bb7da4a78242afc0901e37`

\- 触发 swap`0x261e44cc73beae04ae97b1548bacdf6af4d95e00e3632a0a2fb0de324894e1b3`

\- callback 执行 / Stop 事件`0xc63dc3de06bf98b4e17e77dba13b19a648d2ced34920e94bb0c9e8afdb6d5290`

\### 3. 我是如何触发它的

我先在 Sepolia 上部署了一组新的测试 token，并创建了新的 Uniswap V2 Pair，然后向池子中加入流动性。

之后，我给 callback 合约授权，让它可以花费实际的 pair `token0`，再通过：

1\. 向 pair 直接转入少量 token

2\. 手动调用 `pair.swap(...)`

来改变池子的储备比例，触发 `Sync` 事件。Reactive Contract 监听到这个事件后，执行了 callback，从而完成止盈止损 Demo 的完整闭环。

\### 4. 触发成功的证明

本次触发成功的证明是 callback 合约发出了 `Stop` 事件。

证明信息：

\- Stop 事件交易哈希`0xc63dc3de06bf98b4e17e77dba13b19a648d2ced34920e94bb0c9e8afdb6d5290`

\- Stop 事件所在区块`10448845`

\- Pair 地址`0xE8739A1372DE645dEaE7717A2067F4BefC1FB628`

\- Client 地址`0xeaBe63116b6d5A3510c2F101A97E67136a821Be5`

\- 卖出的 token`0xF43E94E2163F14c4D62242D8DD45AbAacaa6DB5a`

\- 交换数量`1000000000000000000 -> 905870019061450485`

这说明整个流程已经成功跑通：

`event -> react() -> callback`
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->


# **Reactive 止盈止损研究笔记**

今天主要做了三件事：

1.  深入研究了 Reactive 止盈止损的底层原理，以及它和中心化交易所、订单簿型链上交易系统的区别
    
2.  优化了官方当前更偏“单个条件单”的止盈止损形态，验证了 callback 合约可以支持一次性创建止盈 + 止损
    
3.  对更高级的订单管理功能做了设计与成本评估，包括订单分组、按组取消、批量取消、改单等能力
    

## **一、Reactive 止盈止损和 Hyperliquid / CEX 型系统的区别**

以 Hyperliquid 为例，它的止盈止损属于交易系统原生支持的条件单能力。用户在其交易系统内持有仓位后，可以直接设置 TP/SL；当价格达到条件时，平台交易引擎会根据 mark price 来触发执行。这种模式本质上更接近订单簿体系下的仓位管理。

Reactive 的止盈止损则不同。它不是依赖一个中心化撮合系统来处理用户仓位，而是直接作用在用户自己的钱包资产上，不需要资金托管。它的执行逻辑是：持续监控目标流动性池的链上价格，一旦达到设定条件，就通过 callback 合约自动完成 swap。

因此，两者的核心区别在于：

-   Hyperliquid / CEX 型系统：仓位在交易系统内部，止盈止损由平台原生支持
    
-   Reactive：资产仍在用户钱包里，止盈止损是通过链上监控 + callback 执行完成的
    

## **二、为什么在我们当前实现里需要 WETH / WBNB**

这里有一个很重要的实现细节。

在我们当前这套 callback 设计里，执行逻辑基于 ERC-20 的 approve + swapExactTokensForTokens 语义。这意味着执行资产必须是 ERC-20 代币，而不能直接是原生 ETH 或 BNB。

因此，如果用户想对钱包里的 ETH 做止盈止损，就需要先把 ETH 转成 WETH；在 BSC 上则需要先把 BNB 转成 WBNB。也就是说，在我们当前实现里，底层路径更接近：

-   ETH -> WETH -> USDT / USDC
    
-   BNB -> WBNB -> USDT / USDC
    

这不是 DEX 本身的绝对限制，而是我们当前 callback 执行模型的限制。换句话说，这是“当前实现选择”的结果。

## **三、官方现有止盈止损为什么更像分开挂单**

根据目前收集到的资料，官方应用 ReacDEFI / Reactor 的公开形态更偏向“单个 stop order”。如果用户想同时做止盈和止损，当前产品形态更像是分别创建两笔条件单。

但从 callback 合约的能力来看，一次性创建止盈 + 止损完全可行。因为一组 bracket 本质上就是一次交易里同时创建两条订单记录：

-   一条对应止损
    
-   一条对应止盈
    

也就是说，我们现在的理解不是“分别下两个完全无关的单”，而是“一次 bracket 创建，生成一组绑定关系的两条腿”。

## **四、为什么需要订单分组机制**

如果只是简单地把止盈和止损都创建出来，还会遇到一个关键问题：

当其中一条腿先触发时，不能误伤用户其他无关订单。

为了解决这个问题，就需要引入显式的订单分组机制，例如：

-   BracketGroup
    
-   cancelBracket(groupId)
    

这样一来：

-   同一组止盈 / 止损会被绑定
    
-   一条腿执行后，只取消同组另一条腿
    
-   不会错误取消其他订单
    

这一步很关键，因为它决定了系统能不能真正支持“同一路径多组订单并存”。

## **五、为什么不像 CEX 一样默认支持很多高级功能**

继续往下思考，会发现一个现实问题：

为什么不像 CEX 那样直接支持：

-   一键取消全部订单
    
-   修改订单金额
    
-   修改止盈止损价格
    
-   查询活跃订单
    
-   设置订单过期时间
    

核心原因是 **gas 成本**。

在链上，合约每增加一种高级功能，通常就意味着：

-   更多状态变量
    
-   更多 storage 写入
    
-   更复杂的状态维护逻辑
    

而在 EVM 里，storage 写入是最贵的操作之一。  
因此，功能越丰富：

-   合约部署成本越高
    
-   每次新建订单的成本也会上升
    
-   后续取消、改单、批量操作的实现复杂度也会增加
    

这也是为什么很多官方产品会先做“最小可用 stop order”，而不是一开始就做成完整订单管理系统。

## **六、在 BSC 上做的实际成本对比**

为了验证 richer callback 是否值得，我在 BSC 上分别做了两种实现：

### **1\. 基础版 callback**

只包含止盈止损核心能力。

### **2\. 高级版 callback**

包含以下功能：

-   订单分组
    
-   同一路径多组订单并存
    
-   按组取消
    
-   一键取消全部订单
    
-   查询钱包当前活跃订单
    
-   订单过期时间
    
-   原地修改数量
    
-   原地修改止盈价 / 止损价
    
-   原地修改最小成交量
    
-   原地修改过期时间
    

### **新建订单 gas 对比**

-   基础版：518,760 gas
    
-   高级版：765,525 gas
    

### **实际 BNB 消耗对比**

-   基础版：0.000051876 BNB
    
-   高级版：0.0000765525 BNB
    

### **实际 USDT 成本对比**

-   基础版：0.034004 USDT
    
-   高级版：0.050179 USDT
    

### **差异**

-   多出来的成本约为：0.016175 USDT
    
-   gas 增幅约为：47.6%
    

## **七、当前结论**

从结果来看，高级版 callback 虽然显著增加了订单管理能力，但每次新建订单实际只多花了约 0.016175 USDT。从产品角度看，我认为这个代价在 BSC 上是划算的，因为它换来了更完整、更接近真实交易工具的订单管理体验。

不过，47.6% 的 gas 增幅也说明了一件很重要的事：

**止盈止损最终部署在哪条链上，仍然是一个非常关键的产品决策。**

如果部署在 gas 更高的链上，高级功能带来的成本放大会更加明显；而像 BSC 这种低成本链，就更适合承载 richer callback 和更完整的订单管理能力。

## **八、目前的工程判断**

基于今天的研究，我目前的判断是：

-   如果目标只是“最轻量的止盈止损”，基础版 callback 就足够
    
-   如果目标是“更接近真实交易工具的完整订单管理”，那么高级版 callback 是值得的
    
-   但 richer callback 更适合部署在低 gas 成本链上
    
-   因此，**在哪条链上做止盈止损**，将直接影响功能上限和产品体验
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->




打卡
<!-- DAILY_CHECKIN_2026-03-13_END -->

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
