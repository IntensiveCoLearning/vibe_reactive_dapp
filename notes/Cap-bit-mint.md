---
timezone: UTC+8
---

# Eden

**GitHub ID:** Cap-bit-mint

**Telegram:** @Eden_mint

## Self-introduction

年初在LXDAO的帮助下正式进入 Web3，最近在 vibe coding 自己的项目中，想通过参与这次学习，理解并掌握 Reactive 的执行逻辑和架构，并能产出demo

## Notes

<!-- Content_START -->
# 2026-03-21
<!-- DAILY_CHECKIN_2026-03-21_START -->
Day13~
<!-- DAILY_CHECKIN_2026-03-21_END -->

# 2026-03-19
<!-- DAILY_CHECKIN_2026-03-19_START -->

Day-10

完成二阶段挑战，中间出现了一些卡顿，结合 AI 配合解决了。

还有几天就要结束共学了，这几天公事、私事一堆，加油吧
<!-- DAILY_CHECKIN_2026-03-19_END -->

# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->


Day-10

take a rest
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->




**小白学习Day-9**

今天尝试完成第二阶段的实战任务“让一个 Demo 在测试网上跑起来”。

我选择【Uniswap V2 止损订单】这个 Demo，能够实现监听Uniswap V2池的`Sync`事件，当价格（由储备金计算）达到阈值时，自动执行swap卖出代币。

简单来说，就是当 Uniswap 上的某个代币价格跌到一定程度，系统会自动帮我卖掉它。整个过程不需要我24小时盯着电脑。

先拆解下需要完成的步骤

**第一步：准备开发环境**

**简单来说：这步就是领取游戏币**

-   获取测试网代币 (lREACT)
    

[具体步骤可见：https://dev.reactive.network/reactive-mainnet](https://dev.reactive.network/reactive-mainnet)

-   将 Reactive Lasna 测试网添加到钱包里
    

**第二步：使用 Reactor 部署止损订单——打开自动开关**

**简单来说：这步就是告诉 Reactive 系统：“帮我盯着这个价格，到了就自动操作”**

-   访问 Reactor 平台
    
-   选择“止损订单”模板
    
-   填几个简单信息：需要监控的代币、设置触发价格门槛
    

**第三步：触发与验证**

**简单来说：这步就是制造一个“触发条件”，模拟真实市场波动，来测试自动开关是否灵验**

-   在 Uniswap 测试网上，发起一笔交易，故意把价格砸下去
    
-   查看 Reactive 系统，关注两点信息：检测到价格到了设定的阈值；自动触发一笔交易，帮我i卖掉代币
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->





Day-8

休一天，明日补上进度
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->






Day-6

今日完成了“构建一个跨链事件与回调合约”挑战

交易哈希：0x73d0f768ef271d7546d85916677bc389b6d52d0177e4138e8b0d742c669eb4ce

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Cap-bit-mint/images/2026-03-15-1773590294837-image.png)
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->







Day-5

今天在做“构建一个跨链事件与回调合约”的挑战。

结合 AI 辅助理解这个挑战的要求

-   部署事件源合约（**Origin Contract**）
    

我需要在源链上的部署一个**小型广播站**。它里面有个计数器，每次有人点一下“加1”按钮，它就大喊一声：“计数变啦！新数字是X，是谁点的我也知道！”（这就是完成触发事件）

-   部署 **Reactive Contract**
    

我需要监听这个小型广播站是否有喊话，所以，我需要在 Reactive Network 上放一个的**监听器。**一旦它识别到我设的规则，如“计数值超过了10了，我就给另一个链发信号你该工作”

-   部署回调合约（**Destination Contract**）
    

这是我放在目标链上的**接收器**。它接到 Reactive 合约的信号后，就会执行你预设好的动作，比如把这次喊话的信息（谁点的、新数字是多少、几点几分）存到自己的账本里，并再发个事件证明“我收到啦！”
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->








Day-4

今天出差请假一天
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->









Day-3

在理解传统 EVM 与 Reactive 的根本区别后，今天深入了解 Reactive 的技术架构

1.  跨链范围（Cross-Chain Reach）允许订阅任何支持链上的事件，就像全屋智能的传感器能覆盖所有房间，不受品牌限制；
    
2.  自动执行（Auto-Execution）让合约在条件满足时自动触发，无需调用者或 Keeper ，这相当于智能家居系统检测到你起床，自动执行起床模式，不需要你按任何按钮；
    
3.  可组合逻辑（Composable Logic）支持将多个响应式合约串联成多步骤的工作流，就像识别到我“起床→连接音响→播放音乐→拉开窗帘”可以组合成完整场景；
    
4.  可扩展设计（Built to Scale）通过隔离的 ReactVM 实例处理事件，避免复杂的协调开销，就像全屋智能为每个房间配备独立控制器，互不干扰，整体响应更快；
    
5.  直接日志访问（Direct Log Access）让合约直接从日志订阅中读取区块链事件，无需预言机或中间件，相当于智能音箱直接接收传感器信号，不需要经过云服务器中转，更安全、更实时。
    

所以，Reactive 不是简单地在 EVM 上加个触发器，而是从底层重新设计了合约的执行模型。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->










Day-2  
  
基于今天的学习，我想用生活例子来理解 Reactive 与传统 EVM 的区别：

传统 EVM 就像我设的 iPhone 闹钟：我必须手动设定好时间，到了点它才会响。

而 Reactive 更像是 iPhone 快捷指令，或者更准确地说，是全屋智能系统。它能够感知我的状态（比如“起床了”），然后自动协调不同区域的设备：连接音箱播放 QQ 音乐的今日30首、拉开窗帘、启动咖啡机。整个过程不需要我逐个操作，系统自主完成。

放到 Web3 环境里，传统 EVM 的运作逻辑是**被动触发**：只有用户发起交易、支付 Gas 并确认后，合约代码才会执行。而 Reactive 的核心是**主动监测**：它会持续监听各条 EVM 兼容链上的特定事件，一旦识别到预设条件达成，就自动触发跨链、多步的预设操作。这就实现了真正的链上自动化。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->











Day-1

冲冲冲！期待共学结束后自己的第一个 demo ！

Let’s vibe Reactive dApp
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
