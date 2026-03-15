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
# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->
# Reactive DApp学习第5天

-   什么是幂等性：同样的事情就算被触发多次，结果也不能乱。
    
-   `subscribe` 和 `unsubscribe` 都通过链 ID、源合约地址以及 topic 条件来精确管理事件监听，其中 `topic_0` 通常用于标识事件类型，`topic_1~3` 用于更细的筛选。取消订阅比订阅更贵，因为系统需要搜索并移除已有记录。重复或重叠订阅是允许的，因此客户端需要自行保证幂等性。`IReactive` 则定义了反应式合约的标准接口，规定了事件输入结构 `LogRecord`、回调事件 `Callback` 以及核心处理函数 `react()`。
    
-   完整的结构：1.构造函数订阅：a.service=订阅服务/合约接口； b.if（vm)确认是放在reactiveVM里面还是reactive network里面，订阅只能在network里；c.service subscribe(注册监听规则）；d.callback
    

## Subscription Criteria：订阅标准到底是什么

在 Reactive 合约中，订阅通常在构造函数中通过 `service.subscribe()` 完成初始化。订阅条件由链 ID、源合约地址以及 topics 组成，其中至少一个条件必须是具体值，不能全部使用通配符。系统只支持严格相等匹配，不支持大小比较、范围条件或复杂 OR 组合。若需要监听多个来源或多种事件，应多次调用 `subscribe()`。此外，Reactive 合约也支持动态订阅：ReactVM 可根据接收到的事件决定新增或取消订阅，但真正的订阅管理仍需通过 Reactive Network 侧完成。动态订阅示例中，会先在初始化阶段声明链 ID、事件 topic、callback gas limit、owner 和业务服务实例等常量与状态变量。
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->

# ReactiveDapp学习第四天

-   ReactVM 这边就像“真正干活的机器人”：它一边盯价格，一边判断是否该止损；一旦该止损就立刻发 callback；等收到执行成功的回执后，再把订单状态标记为完成。
    
-   `pause()` 是一个运行在 Reactive Network 侧的管理函数，用于取消当前合约注册的事件订阅并将状态标记为已暂停，从而暂时停止该 Reactive Contract 继续监听外部事件。
    
-   `service.unsubscribe(...)` 用于取消一条已经注册的事件订阅。它需要提供与原订阅一致的链 ID、合约地址以及 topic 条件，Reactive Network 才能准确找到并删除对应的监听规则。按照“链 + 合约 + 事件类型 + 过滤条件”这整套规则，把这一条事件监听取消掉。
<!-- DAILY_CHECKIN_2026-03-14_END -->

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
