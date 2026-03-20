---
timezone: UTC+8
---

# joycefey

**GitHub ID:** joycefey

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-20
<!-- DAILY_CHECKIN_2026-03-20_START -->
今日学习：继续构思黑客松的想法，对于reactive在IOT方面的应用有想法，但是对于Defi方面暂时没有太多想法，所以继续构思中。
<!-- DAILY_CHECKIN_2026-03-20_END -->

# 2026-03-19
<!-- DAILY_CHECKIN_2026-03-19_START -->

今天开始写黑客松项目的智能合约。
<!-- DAILY_CHECKIN_2026-03-19_END -->

# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->


今日学习：完成第二阶段的demo部署。开始第三阶段的内容学习，和黑客松的构思。

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/joycefey/images/2026-03-18-1773847027938-image.png)
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->



今日学习部署了UNISWAP的停止交易的合约demo，但是没有触发任何的记录，明天再来找一下原因。

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/joycefey/images/2026-03-17-1773762237308-image.png)
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->




今日学习：继续做第二阶段的学习内容。

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/joycefey/images/2026-03-16-1773673492552-image.png)
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->





今日学习：今天在做第二阶段的demo，以这个模型  
【链上事件——Reactive Contract 监听——react() 执行逻辑——callback transaction】为参考，选择的demo是Uniswap V2 Stop Order Demo。
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->






今日学习笔记：今天没有写代码，今天主要学习了昨天的会议内容，然后对源合约和响应合约更深入的理解了代码内容。
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->







核心架构的三个合约均已成功部署，参数配置无误。Origin 合约已成功在 Sepolia 链上触发多次事件。同时，Reactive 合约已在 Lasna 链上成功注资激活（余额 2.0）。 经排查 Reactscan 后台，目前 `RVM TRANSACTIONS` 仍为 0，推断当前 Lasna 测试网节点存在跨链事件监听延迟或拥堵，导致目标链暂未收到回调。整体事件驱动的自动化架构与跑通逻辑已验证完毕。

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/joycefey/images/2026-03-13-1773388357468-image.png)![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/joycefey/images/2026-03-13-1773388341974-image.png)
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->








今日学习：官方的github智能合约代码，deploy在remix上。

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/joycefey/images/2026-03-12-1773317930954-image.png)
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->









今日学习：reactive智能合约的核心是不再需要用户主动触发，而是由区块链上的event来驱动。以解决实时、自动响应的场景，例如defi自动化，跨链互操作。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->










Reative是实现自动化响应的，但不能因此去追求极致的低延迟，所以针对高频交易的defi项目还是有风险。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->











传统的 EVM只有当用户连上钱包，花 Gas 费才会被动地计算。而 Reactive Network 会主动监听源链上的信息，信息触发后就能自动执行。所以Reactive适合解决跨链自动化执行的相关问题。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
