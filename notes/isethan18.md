---
timezone: UTC+8
---

# isethan18

**GitHub ID:** isethan18

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->
## 2026.03.09

## Day 1：理解“睿应层”与传统 EVM 的根本区别

**核心点：从“拉取（Pull）”到“推送（Push）”**

-   **传统合约（被动）：** 逻辑像死水，必须有外部交易（EOA）来“踢”它一脚（调用函数），它才会动。
    
-   **Reactive 合约（主动）：** 逻辑像雷达。你部署它时，会给它一个“订阅清单”。一旦目标链上出现了清单里的 `Log`，Reactive Network 会主动把数据推给它，触发执行。
    

### 必须要知道的两个概念：

1.  **ReactVM：** 这是一个不保存状态资产、只跑逻辑的“大脑”。它能跨链读取，但不会直接改变原链状态，除非通过 **Callback（回调）**。
    
2.  **Inbound (入金) / Outbound (出金)：** \* **Inbound：** 监听其他链（如 Sepolia）的事件。
    
    -   **Outbound：** 逻辑跑完后，发出一笔交易去影响目标链。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
