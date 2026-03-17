---
timezone: UTC+8
---

# Eddie-534

**GitHub ID:** Eddie-534

**Telegram:** 

## Self-introduction

Let's vibe Reactive dApp！

## Notes

<!-- Content_START -->
# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->
2026.3.17 今天解决了回调合约部署问题，现在完成抵押与借出，但遇到了问题。  

![屏幕截图 2026-03-17 232626.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Eddie-534/images/2026-03-17-1773762480831-_____2026-03-17_232626.png)![屏幕截图 2026-03-17 234617.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Eddie-534/images/2026-03-17-1773762494634-_____2026-03-17_234617.png)
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->

26.3.16 今天开始学习 aave 清算保护，领取了 USDC，部署了三个合约，但其中一个出了点问题，还在解决中。  

![屏幕截图 2026-03-16 234610.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Eddie-534/images/2026-03-16-1773676234407-_____2026-03-16_234610.png)
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->


26.3.15 今天终于完成了挑战任务2，整理了一下思路:

1.部署代币合约，token0 和 token1

2.调用 factory 合约创建交易对，注入代币添加流动性，铸造LP代币

3.部署回调合约，授权回调合约 token0

4.部署反应式合约（卖 token0/买 token1，触发条件:0/1＜=0.9）

5.调用 swap 往钱包转 token1，自动 sync，reactive 链上监听到事件，触发回调合约 stop，把 token0换成 token1

![屏幕截图 2026-03-15 170906.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Eddie-534/images/2026-03-15-1773565754935-_____2026-03-15_170906.png)![屏幕截图 2026-03-15 170803.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Eddie-534/images/2026-03-15-1773565763846-_____2026-03-15_170803.png)
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->



26.3.14 今天把三个合约都重新改了下，整理思路从头开始，但最后 reactive 链上还是没监测到，回调合约地址也没有交易事件，到底是哪里出问题了呢。  

![屏幕截图 2026-03-14 233704.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Eddie-534/images/2026-03-14-1773502698356-_____2026-03-14_233704.png)![屏幕截图 2026-03-14 233832.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Eddie-534/images/2026-03-14-1773502778055-_____2026-03-14_233832.png)![屏幕截图 2026-03-14 233927.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Eddie-534/images/2026-03-14-1773502786869-_____2026-03-14_233927.png)
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->




26.3.13 今天创建了uniswap 交易对，并部署了 reactive 合约，triggerdrop 砸盘，但回调合约和 reactive 合约还没有反应，出了点问题，还在调试中。  

![屏幕截图 2026-03-13 225452.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Eddie-534/images/2026-03-13-1773414332483-_____2026-03-13_225452.png)
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->





26.3.12 今天在做挑战第二关，完成了部署源链合约，目标链合约和reactive合约，并且创建了 uniswap 交易对。  

![屏幕截图 2026-03-12 233834.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Eddie-534/images/2026-03-12-1773330074633-_____2026-03-12_233834.png)
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->






26.3.11 今天完成了构建跨链事件与回调合约。  
  
源合约：0x885c0dfD7894cdeEE3e55afB42f44Bec8757427d

回调合约：0xCa9A0d9B680cf7096f39588F3acdD74ADd1C4aa0

Reactive合约0x0cF71E200DCf94587f487FF1C34035236A2747a0

交易希哈：0x4cd9a77c64012881b43d94d976201ba5c778bcacea6b00a5de122719e8ed8d29  
  

![屏幕截图 2026-03-11 002452.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Eddie-534/images/2026-03-11-1773207012734-_____2026-03-11_002452.png)![屏幕截图 2026-03-11 132436.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Eddie-534/images/2026-03-11-1773207022314-_____2026-03-11_132436.png)
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->







26.3.10 今天部署了三个合约，连接了 reactive lasna 网络，明天将会触发并验证跨链回调。  

![屏幕截图 2026-03-10 233540.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/Eddie-534/images/2026-03-10-1773157153639-_____2026-03-10_233540.png)
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->








26.3.9 今天对 reactive network 进行学习和理解：

1\. Reactive Network 与传统EVM的根本区别

传统的 EVM 只能被动执行交易：你必须先发起一笔交易，EVM 才会更新状态。

而 Reactive Network 可以主动监听和响应事件。它与 EVM 的根本区别在于：

Reactive Network 则是 “事件自触发”（当链上或跨链事件发生条件满足时，合约自动运行）。

在 Reactive Network 中，合约可以订阅和响应来自外部的链上事件。

2\. 核心架构：ReactVM + Reactive Network 环境

ReactVM（反应式虚拟机）

专门为运行 Reactive Smart Contracts 而设计的执行环境。

Reactive Network 环境

当监听到的条件满足时，这个环境会唤醒 ReactVM 并执行对应的反应式合约。

3\. 理解“事件驱动智能合约”与 Reactive Smart Contracts

事件驱动智能合约

· 这是一种新的编程范式。传统的合约是“函数调用驱动”的，你必须调用它的函数。事件驱动的合约则是“订阅驱动”的，它会一直保持待命状态，一旦监听到特定的事件（比如某个地址转了一笔账，或者某个价格突破了阈值），就会自动执行预定义的回调逻辑。

Reactive Smart Contracts（睿应式智能合约）

特性：

1\. 可订阅性：

2\. 自动执行：不需要用户支付 Gas 来触发，只要事件发生且条件满足，代码就自动运行。

3\. 跨链能力：一个反应式合约可以同时监听不同链上的事件，并在一条链上执行操作，或者在多条链上同时执行操作。

4\. Reactive Network 适合解决什么问题？

自动化跨链操作：

· 例如，你在以太坊主网上看到一笔巨额的 USDC 转账，自动在 Arbitrum 上执行一笔交易。这可以通过一个反应式合约来实现，而无需手动监控和操作。

即时清算与套利：

· DeFi 协议可以使用反应式合约，在某个链上的价格跌破某个水平或抵押率不足时，毫秒级自动触发清算或套利，降低延迟风险。

事件驱动的自动化策略：

· 比如，当某个 DAO 的投票提案通过时，自动执行资金拨付；或者当某个 NFT 以地板价成交时，自动触发购买。

跨链通知与同步：

· 当主网上的关键事件发生时，自动在其他链上更新状态或发送通知。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
