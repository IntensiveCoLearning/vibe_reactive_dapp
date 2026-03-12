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
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
\### 1. Reactive Contract 组成结构

**核心概念:**

\- Reactive Contracts (RCs) 是事件驱动的智能合约，用于跨链和链上自动化

\- RCs 监控 EVM 链上的事件日志，当条件满足时自动执行 Solidity 逻辑

\- 无需用户或机器人触发交易，RCs 持续运行并决定何时发送跨链回调交易

**部署环境:**

\- **Reactive Network (RNK)** — 公共链，EOA 与合约交互，管理订阅

\- **ReactVM (RVM)** — 私有执行环境，事件处理在此进行

\- 两份部署使用相同字节码，但独立运行，不共享状态

**状态分离:**

\- ReactVM 状态：订阅事件发生时自动更新

\- Reactive Network 状态：EOA 调用合约函数时更新

\- 合约可通过调用系统合约检测是否在 ReactVM 内执行

**合约验证:**

\- 使用 Sourcify 进行去中心化验证

\- 验证后可在 Reactscan 查看完整源代码

  
**跨链和自动化机制**

Origin & Destination 模型:  

-   Origin Chain: 事件发生的源链（如 Ethereum, BSC）
    

-   Destination Chain: 回调执行的目标链
    

-   使用 Hyperlane 进行跨链消息传输
<!-- DAILY_CHECKIN_2026-03-12_END -->

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
