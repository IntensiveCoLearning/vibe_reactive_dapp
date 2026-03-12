---
timezone: UTC+8
---

# FairyTaleBliss

**GitHub ID:** FairyTaleBliss

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
再！跑！Uniswap V2 止损：

这次按着 GitHub 的 [readme.md](http://readme.md) 来走：

首先部署给定的代币对：

TK1：

Deployer: 0x74217992032536E195DAD5aDa6E7bDf127B0A1E4 Deployed to: 0x75b9FA9992cB63254917a8B0b7CD7bA89cB31036 Transaction hash: 0x540a513b99484edb84dd24894c5b9719a48d6652f1fb46c1b0df4bbbf5f6e37a

TK2：

Deployer: 0x74217992032536E195DAD5aDa6E7bDf127B0A1E4 Deployed to: 0x15D5ed21F4050E8cEdD2Fce2F9FEF84ae026F111 Transaction hash: 0x7fb4592c12ffa6039eae18d5f697e1c48524bf77d86b245c3937d2370187e90d

交易对地址也是提前定好的，我直接填写进 .env 文件： `export UNISWAP_V2_PAIR_ADDR=0x1DD11fD3690979f2602E42e7bBF68A19040E2e25`

然后是部署 **Destination Contract：**

Deployer: 0x74217992032536E195DAD5aDa6E7bDf127B0A1E4 Deployed to: 0x15D5ed21F4050E8cEdD2Fce2F9FEF84ae026F111 Transaction hash: 0x7fb4592c12ffa6039eae18d5f697e1c48524bf77d86b245c3937d2370187e90d

其中 Deployed to 配置至 CALLBACK\_ADDR

下一步是向资金池增加流动性，有bug，暂时卡住了。先打卡。
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

1\. 今天贴个我曾经给Reactive写的推文吧：[https://x.com/JaggerLam/status/2022665535197204713](https://x.com/JaggerLam/status/2022665535197204713) 比较粗浅的认知，但是毕竟跑过玩具Demo，也看了一些相关资料，写了这么一篇。

2\. 日拱一卒，Solidity 和 EVM 还得学。今天解开一个小困惑，关于tx.origin, msg.sender, msg.value. 原来这是 Solidity 语言内置的、在合约执行时可访问的全局变量。

3\. 一个月前跑Uniswap V2止损 Demo 的挫败历历在目，今天打算问问 Ivan 发现我已经忘记了疑惑点。好了，重跑一遍，看看能不能触发。我怀疑部署没问题，是我对 Uniswap 相关命令不熟悉。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->


其实在实习计划时候跑过最简单的callback了，uniswap没跑成功。对Reactive有点了解，但是不太体系。对EVM + Solidity也不是很熟悉，所以还得递归学习很多东西。

今天不是很明确学习方向，也没学什么东西。明天积累点东西再打卡好了。感觉现在需要研究明白demo代码，然后vibe coding一些东西。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
