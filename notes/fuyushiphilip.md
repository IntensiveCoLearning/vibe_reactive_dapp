---
timezone: UTC+8
---

# FYS

**GitHub ID:** fuyushiphilip

**Telegram:** 

## Self-introduction

Let's vibe Reactive dApp！

## Notes

<!-- Content_START -->
# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->
**Reactive Contracts 是什麼？**

傳統智能合約（Solidity）特性：

•  被動式（Passive）

•  必須由外部帳戶（EOA）或另一合約主動呼叫才會執行

•  無法自己「醒來」對鏈上事件做出反應

**Reactive Contract（反應式智能合約）**：

•  主動式（Active / Reactive）

•  可以**訂閱**其他鏈（或同一鏈）的事件 log

•  當特定事件 + 條件成立時 → **自動執行**邏輯

•  支援跨鏈自動觸發（callback / 發送交易到目標鏈）

•  本質上是「鏈上版的 If-This-Then-That (IFTTT)」

核心創新：**Inversion of Control (控制反轉)**

→ 不再是「使用者呼叫合約」，而是「合約在事件發生時被系統呼叫」。
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->

Reactive Network 完整执行生命周期

User

│

│ tx trigger()

▼

BasicDemoL1Contract

│

│ emit Trigger

▼

Ethereum Log

│

│ indexed by

▼

Reactive Network

│

│ match subscription

▼

ReactVM

│

│ call react()

▼

BasicDemoReactiveContract

│

│ send tx

▼

BasicDemoL1Callback

│

│ emit Reacted

▼

Done
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->


傳統 DeFi 架構（Bot 驅動）

flowchart LR

A\[Blockchain Event<br>Swap / Price / Transfer\]

A --> B\[Indexers<br>TheGraph / Custom Indexer\]

B --> C\[Off-chain Bot\]

C --> D{Strategy Logic}

D -->|trigger| E\[Send Transaction\]

E --> F\[Smart Contract\]  
  
  
Reactive Network 架構（Event-driven）  
flowchart LR

A\[Blockchain Event<br>Swap / Price / Transfer\]

A --> B\[Reactive Network<br>Event Engine\]

B --> C\[Reactive Contract\]

C --> D\[Execute Logic\]

D --> E\[Send Transaction<br>to target chain\]
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->



傳統Smart Contract 是被動的，意思是沒人調用contract, contract 就什麼都不會做。

現在的DeFi系統依賴bots/Keepers 監聽鏈上的交易。

Reactive Contract 監聽鏈上事件，然後自動執行 contract，核心概念Event-driven Smart Contract。

| Smart Contract | Reactive Contract |
| --- | --- |
| User trigger transaction | Blockchain event |
| Passive | Auto |
| Bot/Keeper | network |
| Call-based | Event-driven |
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->




![Screenshot 2026-03-09 at 7.51.52 PM.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/fuyushiphilip/images/2026-03-09-1773057195173-Screenshot_2026-03-09_at_7.51.52_PM.png)
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
