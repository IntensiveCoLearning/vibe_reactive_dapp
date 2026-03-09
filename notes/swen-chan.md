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
