---
timezone: UTC+8
---

# Thomas

**GitHub ID:** Thomas-YHS

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->
今天完成了Demo的部署并运行，具体的实现如下文所示：  
\# 跨链事件与回调作业提交

\## 1) 部署信息

\- Origin Contract 地址: `0x9A76EB25854B42B60B984C933b08eD12eeAeFdC8`

\- Destination Contract 地址: `0x2F4dDA454F470537445e70F479EF9D7F24Ce40Ae`

\- Reactive Contract 地址: `0x4DA4f5ebe4de9Da95Ab7DEdFa92a76755531918b`

\## 2) 交易哈希

\- Deploy Origin Tx: `0xe218241014d541437b7953d0e6c6d83a0b4de15b269020decdbdbd93277d4a87`

\- Deploy Destination Tx: `0xf0f7767ecd347404985e4f006461b1f0e709ec848ad9412413c4d994970b91f0`

\- Deploy Reactive Tx: `0xeaa98e9f8539e79b849fd273eb3de1e5ece215d13eb0b60530944ac1c8a2cfb1`

\- Trigger Origin Tx: `0xcccddb46d1cda1d193b8c353d687cc2a9949b6dd4028a16489c136371140783f`

\- Callback Tx（目标链）: `0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35`

\## 3) 触发成功证明

!\[1773144120335\](image/SUBMISSION\_TEMPLATE/1773144120335.png)

\- Origin `Received` 事件浏览器链接: [https://sepolia.etherscan.io/tx/0xcccddb46d1cda1d193b8c353d687cc2a9949b6dd4028a16489c136371140783f#eventlog](https://sepolia.etherscan.io/tx/0xcccddb46d1cda1d193b8c353d687cc2a9949b6dd4028a16489c136371140783f#eventlog)

!\[1773144297441\](image/SUBMISSION\_TEMPLATE/1773144297441.png)

\- Destination `CallbackReceived` 事件浏览器链接: [https://sepolia.basescan.org/tx/0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35#eventlog](https://sepolia.basescan.org/tx/0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35#eventlog)

\- Reactscan Lasna 成功截图 [https://lasna.reactscan.net/address/0x8dc56a4e682b2e12f5e4056d8e82b30c2dee94bc/2](https://lasna.reactscan.net/address/0x8dc56a4e682b2e12f5e4056d8e82b30c2dee94bc/2)

!\[1773144433736\](image/SUBMISSION\_TEMPLATE/1773144433736.png)

\## 4) 简短说明

\- 本次测试中，向 Origin 合约发送 `1000000000000000` wei 后，Reactive 合约监听到 `Received` 事件并触发跨链回调，在 Base Sepolia 上成功执行 callback 交易 `0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35`，完成 `CallbackReceived` 事件证明。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->

终于又开始了新的残酷共学，这次一定要坚持下来，这次想看看能不能做一个简单的链上套利机器人。  
今天看了看文档，把demo的代码拉下来，基础的配置调试了一下，明天部署到测试链上，下面是今天的笔记：

##   
Reactive Network

Reactive 的核心可以引用文档中的一句话来描述：

> Reactive Contracts monitor event logs across EVM chains and execute Solidity logic automatically when conditions are met. Instead of waiting for users or bots to trigger transactions, RCs run continuously and decide when to send cross-chain callback transactions — essentially providing **if-this-then-that automation for smart contracts**.

Reactive实现的最重要的一点是可以在链上持续监控并且发送callback到指定链，传统区块链必须有人发起交易，而Reactive Contracts **持续运行并监听事件**，当条件满足时会自动执行操作。

* * *

Reactive Network 的执行模型本质上是：

**监听 Origin 上的事件 → 在 Destination 上发送 callback → 通过 Callback Proxy 保证安全与可验证性**

Event on Origin → Reactive listens → Callback sent to Destination → Destination verifies through Callback Proxy

### Reactive 可以实现的场景

1.  自动止盈/止损
    
2.  清算保护
    
3.  自动切换投资组合
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
