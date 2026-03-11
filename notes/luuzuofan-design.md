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
