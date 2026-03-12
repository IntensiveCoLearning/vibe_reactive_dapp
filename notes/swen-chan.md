---
timezone: UTC+8
---

# Siwei Chen

**GitHub ID:** swen-chan

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
可以把 Reactive Network 粗略理解成：

**“把监听事件 + 后台 bot 判断 + 跨链再发交易”这整套流程，做成统一的事件驱动执行层。”** 官方首页也是这样定位它的：面向 EVM 的 event-driven execution layer，用来做跨链、链上自动化。

用一个关于**“从 Polygon 上点跨链，自动在另一条链上铸币”** 的简化例子来理解：

先记 3 个角色：

-   **Origin chain**：事件发生的链。比如用户在 Polygon 上发起桥接请求。
    
-   **Reactive Contract**：监听事件、判断条件、决定要不要继续执行的合约。它会订阅链上日志，并在事件出现时自动运行 Solidity 逻辑。
    
-   **Destination chain**：真正执行结果的链。比如在 Ethereum Sepolia 上 mint 对应代币。
    

整个流程可以理解成：

**1）用户先在源链做一个动作**  
比如用户在源链的代币合约里发起 bridge 请求。这个合约会发出一个 `BridgeRequest` 事件，里面通常带上用户地址和数量。Reactive Bridge 的官方示例就是这样设计的。

**2）Reactive Contract 之前已经“订阅”了这种事件**  
Reactive Contract 会通过 `subscribe(...)` 告诉 Reactive Network：我只关心某条链、某个合约、某个 topic 的日志。这个订阅通常在构造函数里设置。

**3）事件一出现，Reactive Network 就把它送进 ReactVM**  
Reactive Network 会监控这些被订阅的 origin-chain 事件；一旦匹配，就把日志分发给对应的 ReactVM。ReactVM 是隔离执行环境，用来处理这些事件。每个部署者地址都有自己的 ReactVM。

**4）**`react()` **函数开始跑业务逻辑**  
这一步最像“自动判断器”。`react()` 会读取那条事件日志，检查是不是合法、金额是多少、要发去哪条链，然后决定下一步怎么做。Reactive 文档把这件事称为 event-driven execution，也就是“事件驱动执行”。

**5）如果条件满足，就发出 callback 请求**  
ReactVM 本身不是直接去目标链乱调用，而是根据逻辑发出一个 callback 请求。文档里说明，ReactVM 对外做事的方式之一，就是通过 Reactive Network 向 destination chain 发送 callback。

**6）目标链通过 Callback Proxy 收到这笔调用**  
Reactive 的目标链侧会通过一个 **Callback Proxy** 合约转发这次调用。目标合约可以检查两件事：  
一是调用者是不是 Callback Proxy；  
二是里面带的 RVM ID 对不对。  
这样目标合约能验证这次 callback 确实来自正确的 Reactive Contract。

**7）目标链执行最终动作**  
例如 mint 代币、释放资产、触发某个函数。Reactive Bridge 的官方示例里，Reactive Contract 监听源链 `BridgeRequest`，然后在目标链触发 mint。

你可以把它压缩成一句话：

**源链发事件 → Reactive Network 发现 → ReactVM 运行** `react()` **→ 发 callback → 目标链执行结果。**

对初学者最重要的理解是：

-   普通合约：**别人调用我，我才动。**
    
-   Reactive Contract：**我先订阅事件，事情一发生，我就自动按规则继续做。**
    

还有一个很关键但容易忽略的点：

**同一个 Reactive Contract 实际上有“两份实例、两份状态”**：  
一份在 Reactive Network 上，负责订阅等网络侧事务；  
一份在 ReactVM 里，负责收到事件后执行反应逻辑。两份代码相同，但状态分开。
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->

最近忙AI共学

回头看

![ba46fe09f67663e6f291e6a76f6241fb.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/swen-chan/images/2026-03-11-1773244551191-ba46fe09f67663e6f291e6a76f6241fb.png)
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->


可以把 **Reactive Network** 理解成：给区块链加上一层“**事件触发的自动执行系统**”。

核心机制只有 4 步：

1.  **订阅事件**  
    开发者先告诉系统：“我只关心某条链、某个合约、某类事件。”Reactive Contract 通过 `subscribe()` 订阅这些链上日志（event logs）。订阅条件通常包括链 ID、合约地址、以及 event 的 topics。
    
2.  **事件发生**  
    当目标链上真的出现了匹配事件，Reactive Network 会捕捉到它，并把这条事件数据交给 Reactive Contract 的 `react()` 函数处理。这个函数拿到的内容包括链 ID、合约地址、topics、event data 等元信息。
    
3.  **在 ReactVM 里判断逻辑**  
    这段逻辑不是在原链上跑，而是在一个隔离环境里跑，叫 **ReactVM**。每个部署者有自己的私有 ReactVM。你可以把它理解成“专门负责看事件并做判断的小执行环境”。`react()` 只能由系统在这个环境里触发。
    
4.  **发起回调，触发目标链交易**  
    如果判断条件成立，合约会发出 `Callback` 事件，里面写明：要去哪个链、调用哪个合约、带什么参数。Reactive Network 看到这个 `Callback` 后，就会在目标链提交对应交易。
    

它的原理，本质上是把传统 dApp 里常见的：

-   监听链上事件
    
-   后台脚本判断
    
-   Bot 再去发交易
    

这三件事，尽量收进一个统一的链上自动化框架里。官方把它描述为一种 **event-driven execution layer**，也就是“事件驱动的执行层”，用于让合约能在不同 EVM 链之间自动响应和执行。

用一句最通俗的话说：

**普通智能合约更像“被人调用才动作”；Reactive Contract 更像“看到某件事发生，就自己按规则继续做事”。**

一个典型例子是跨链桥：  
源链上的合约先发出 `BridgeRequest` 事件；Reactive Contract 监听到后，在 `react()` 里解析参数，再发出 `Callback`，让目标链上的合约去 mint 对应资产。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->



dd
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
