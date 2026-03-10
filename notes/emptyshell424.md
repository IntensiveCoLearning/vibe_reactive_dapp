---
timezone: UTC+8
---

# shell

**GitHub ID:** emptyshell424

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->
## 合约设计逻辑

**Origin (**`BasicDemoL1Contract.sol`**)** — 部署在 Sepolia

-   `receive()` 接收 ETH，emit `Received(from, sender, value, counter)` 然后退款
    
-   `value` 是 indexed 的 topic\_3，RSC 用它来做条件过滤
    

**Destination (**`BasicDemoL1Callback.sol`**)** — 部署在 Sepolia

-   构造时传入 callback proxy 地址，做 `msg.sender` 校验，防止任意人调用
    
-   部署时要附带 0.01 ETH 作为 gas 储备
    

**Reactive (**`BasicDemoReactiveContract.sol`**)** — 部署在 Reactive Lasna

-   构造函数调用 `service.subscribe()`；如果 revert（在 ReactVM 中），则 `vm = true`
    
-   `react()` 只在 ReactVM 环境执行，检查 `topic_3 >= 0.01 ETH` 后 emit `Callback`
    
-   `address(0)` 是占位符，callback proxy 执行时会替换为实际 RSC 地址
<!-- DAILY_CHECKIN_2026-03-10_END -->
<!-- Content_END -->
