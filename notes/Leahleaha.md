---
timezone: UTC+8
---

# Leahleaha

**GitHub ID:** Leahleaha

**Telegram:** 

## Self-introduction

Let's vibe Reactive dApp！

## Notes

<!-- Content_START -->
# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->
Reactive Contract 作用

监听 Origin 事件并执行自动逻辑。

核心逻辑：

订阅 event topic

在 react() 中解析 event

构造 callback payload

发送 callback 到目标链

Reactive Contract 是整个系统的核心。
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->

# 参加Reactive Workshop

如何用AI开发反应式智能合约，相关实操技巧

在开发领域，基于反应式智能合约开发的场景下，AI存在的局限性
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->


差点忘记

先打一个卡
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->



Reactive 作业核心是一条最小自动化链路：

**源链事件 → Reactive 监听 → ReactVM 执行 → 目标链回调。**

只需要抓住这条链路即可。

* * *

一、Reactive Network 的核心机制

Reactive Network 的本质是：

**基于事件的自动执行系统。**

普通智能合约执行流程：

用户交易  
→ 合约执行  
→ 状态变化

Reactive 执行流程：

合约事件  
→ Reactive 监听  
→ 自动执行逻辑  
→ 触发跨链交易

关键变化是 **执行触发方式改变**。

* * *

二、

三个合约形成一个跨链闭环。

```
Origin Contract
      │
      │ emit event
      ▼
Reactive Contract
      │
      │ react()
      │ emit callback
      ▼
Destination Contract
```

三个合约职责明确：

Origin：产生事件  
Reactive：监听事件并触发回调  
Destination：接收回调并执行逻辑

* * *

三、Origin Contract（事件源）

职责：

1 产生链上事件  
2 提供触发函数

最小结构：

```
contract Origin {

    event OriginTriggered(address user, uint value);

    function trigger(uint value) external {
        emit OriginTriggered(msg.sender, value);
    }

}
```

核心点：

Reactive Network 监听的是 **event log**。  
事件参数将成为后续逻辑输入。

* * *

四、Reactive Contract（核心）

Reactive Contract 有两个运行环境：

Reactive Network  
ReactVM

两个环境作用不同。

Reactive Network：

-   订阅事件
    

ReactVM：

-   执行 react()
    
-   生成回调
    

结构逻辑：

```
constructor
 └─ subscribe event

react()
 ├─ 接收 event log
 ├─ 解析参数
 └─ 发送 callback
```

核心函数：

```
function react(LogRecord calldata log) external vmOnly
```

log 包含：

-   event topics
    
-   event data
    
-   contract address
    

Reactive 会把 event log 作为输入。
<!-- DAILY_CHECKIN_2026-03-10_END -->
<!-- Content_END -->
