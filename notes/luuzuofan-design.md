---
timezone: UTC+8
---

# cooking

**GitHub ID:** luuzuofan-design

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->
# 学习Reactive 第三天

-   今天理解了 Callback 的实际发射方式：在 Reactive Contract 中，先通过 abi 把目标链要调用的函数和参数编码成payload，再通过emit callback 指定目标链、目标合约、gas 限额和执行内容，从而触发目标链上的后续操作。
    
-   Reactive Network 不只是一个统一的大网络，它还会为每个部署者提供独立的 ReactVM 执行环境。每个 Reactive Contract 实际上会在 Reactive Network 和对应的 ReactVM 中各有一个实例，这样可以实现状态隔离、并行执行和更高性能。
    
-   Reactive Contract 如何识别自己的执行环境。代码通过detectVM函数检查一个固定系统地址上是否存在合约代码：如果存在，说明当前运行在 Reactive Network；如果不存在，说明当前处于 ReactVM。这个判断用于后续限制不同函数只能在对应环境中执行
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->

# Reactive Dapp 学习第二天

直播学习:

-   不要一开始就让 AI 乱写合约，而是先让它帮你拆需求、做计划、解释流程；等你确认方案后，再让它写代码。代码写出来后，你自己去测试网部署和跑流程，再把结果拿回来让 AI 帮你分析。
    
-   案例：Claude 帮他先做一个跨链USDC Bridge的实现方案，先提出具体需求，让 AI 为“Ethereum 到 Base 的 USDC 跨链桥”生成实现计划。这个例子说明 Reactive Network 项目通常会拆成源链合约、Reactive 合约和目标链合约三层，先理清架构再开始编码。理解了 Reactive Contract 的事件处理接口。`LogRecord` 用于统一描述监听到的链上事件，`react()` 是处理这些事件的入口函数，而 `Callback` 事件则用于把处理结果转成发往目标链的执行任务。整个模式不是普通函数调用，而是“监听事件—处理事件—发出回调—目标链执行”的事件驱动流程。
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->


# Reactive Dapp 学习第 1 天

-   **传统智能合约**：被动，被调用才执行。机器人-EOA-EVM
    
-   **Reactive 合约**：更接近事件驱动，强调“监听—响应—回调”，能跨链
    

### 创建RCS:指定感兴趣的链、合同和事件

RC 是有状态的，意味着它们有一个可以存储和更新数值的状态。你可以在该州随时间积累数据，当历史数据与新的区块链事件组合符合指定条件时，你可以采取行动。

案例1中：预言机（外部数据提供器）+链外事件+RCs，此时RCs监控的是更新后的事件-----EVA

案例2中：RCs监控交易池，达到预期停止损单

案例3中：RC套利，单链情况下：执行套利交易；跨链情况下：调用目标链上的逻辑，或者利用多链流动性去做

总结：RCs的出现减少了对外部单一中心化机器人的依赖，并且反应更加迅速
<!-- DAILY_CHECKIN_2026-03-11_END -->
<!-- Content_END -->
