---
timezone: UTC+8
---

# Qy

**GitHub ID:** pillowtalk-Qy

**Telegram:** @Qy_originshift

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->
```

Origin Contract (链A)
 │
 │ emit Event
 ▼
Reactive Contract (Reactive Network)
 │
 │ 监听事件 + 判断条件
 ▼
Destination Contract (链B)
 │
 │ callback()
 ▼
触发 Destination 事件

```

### 跨链流程：Reactive Contract 会 **订阅 Origin 合约事件**，当检测到事件后执行逻辑，然后**回调 Destination 合约**  
  
Origin Contract（事件源）

### 负责 **触发事件**

```
// Origin.sol
pragma solidity ^0.8.20;

contract Origin {

 event Trigger(address sender, uint256 amount);

 function trigger() external payable {
 emit Trigger(msg.sender, msg.value);
 }

}
```

触发：

```
trigger()
```

链上日志：

```
Trigger(sender, amount)
```
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->

1.互换了些reactive的代币，为之后的作业和Dapp作准备

2.听了晚上的小课，更了解在reactive的nft的变化
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
