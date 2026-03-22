---
timezone: UTC+8
---

# irinakoay

**GitHub ID:** irinaguo

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-22
<!-- DAILY_CHECKIN_2026-03-22_START -->
打卡
<!-- DAILY_CHECKIN_2026-03-22_END -->

# 2026-03-21
<!-- DAILY_CHECKIN_2026-03-21_START -->

打卡
<!-- DAILY_CHECKIN_2026-03-21_END -->

# 2026-03-20
<!-- DAILY_CHECKIN_2026-03-20_START -->


打卡
<!-- DAILY_CHECKIN_2026-03-20_END -->

# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->



打卡
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->




打卡
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->





打卡
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->






打卡
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->








打卡
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->









今天终于把 Reactive 基础 Demo 完整跑通了，从测试网配置到跨链回调验证，全程走完了官方要求的挑战流程。部署过程中踩了两个关键坑：一是 Remix 里切换文件后必须重新编译，否则找不到可部署合约；二是 Reactive 合约部署时参数顺序不能搞反，一旦填错就会导致跨链监听失效。好在之前积累的排查经验派上了用场，一步步核对地址、切换网络，最终成功触发了源链事件，并在目标链上查到了  Callback  事件日志。

这次挑战让我彻底搞懂了反应式智能合约的核心逻辑：源链事件触发 → Reactive 网络监听转发 → 目标链回调执行，整个流程不需要手动中继，完全靠合约自动完成。相比传统跨链方案，Reactive 的事件驱动模型更简洁，也更安全——不需要托管私钥，所有逻辑都写在链上，透明可追溯。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->











学习第一天，清楚了整个 demo 的运行流程：触发事件 → 跨链监听 → 执行回调。接下来就是把部署步骤练熟，把每一步的地址和交易记录保存好，为后面的作业和更复杂的 demo 打基础
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
