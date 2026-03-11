---
timezone: UTC+8
---

# liam

**GitHub ID:** Lyu23

**Telegram:** 

## Self-introduction

Let's vibe Reactive dApp！

## Notes

<!-- Content_START -->
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
**一、核心概念理解**

**1\. 睿应层 vs 传统 EVM 的根本区别**

| 传统 EVM | 睿应层（Reactive Network） |
| --- | --- |
| 被动执行：需要外部调用者触发 | 主动执行：事件触发自动执行 |
| 单链运行 | 跨链事件监听 + 执行 |
| 需要 off-chain bots/keepers | 原生链上自动化，无需中间件 |
| 无法直接监听其他链事件 | 直接订阅任何 EVM 链的日志事件 |

核心差异： 传统智能合约是"被动等待调用"，Reactive Contracts 是"主动监听并响应"。  
  
**二、睿应层适合解决什么问题**

**适用场景**

| 场景 | 说明 |
| --- | --- |
| DeFi 自动化 | 自动清算保护、止盈止损、限价单 |
| 游戏 | 事件触发的游戏逻辑、奖励发放 |
| 跨链操作 | 跨链代币转移、桥接自动化 |
| 基础设施 | 低延迟日志监控、链间通信 |
| 托管/仲裁 | 自动释放支付、争议解决执行 |

**生态案例**

1.  ReacDEFI — 自动化 DeFi
    
2.  NewEra Finance — Uniswap V4 自动化
    
3.  SmarTrust — 去中心化托管
    
4.  Reactive Bridge — 跨链桥
    

**个人理解**

睿应层的本质：把"监听 + 执行"这两个原本需要 off-chain 完成的工作，变成了原生链上能力。就像给智能合约装上了"耳朵"和"自动手"——能听到事件，能自己行动。  
  
最大价值：

1.  降低自动化门槛（不用养 bots）
    
2.  提高可靠性（链上执行，无需信任第三方）
    
3.  增强 composability（合约之间可以事件驱动串联）
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

部署地址:

\- Origin: 0x69a7Be3f27f3F1e1024507d1F87103db638dD310

\- Callback: 0xB5195fBddBf5d583Dc2db69565269844cCB655f4

\- Reactive: 0x1047eEef1E061Ecc14DE3827573AC2b00fcc7523

触发交易哈希: 0xde1d9e77a4c6cbeebaa2860a9046159b8fb64991fa5c5c1fcd5abf23fd21346d  

  
Origin Contract:

[https://sepolia.etherscan.io/address/0x69a7Be3f27f3F1e1024507d1F87103db638dD310](https://sepolia.etherscan.io/address/0x69a7Be3f27f3F1e1024507d1F87103db638dD310)

Callback Contract:

[https://sepolia.etherscan.io/address/0xB5195fBddBf5d583Dc2db69565269844cCB655f4](https://sepolia.etherscan.io/address/0xB5195fBddBf5d583Dc2db69565269844cCB655f4)

Reactive Contract:

[https://lasna.reactscan.net/address/0x1047eEef1E061Ecc14DE3827573AC2b00fcc7523](https://lasna.reactscan.net/address/0x1047eEef1E061Ecc14DE3827573AC2b00fcc7523)
<!-- DAILY_CHECKIN_2026-03-10_END -->
<!-- Content_END -->
