---
timezone: UTC+8
---

# 张雪涛/erik.hahaha

**GitHub ID:** XuetaoZhang

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->
今日完成：

1、睿应层（Reactive Network）官网 Overview学习完成；

2、Reactive Contracts 文档学习到Lesson 3；

今日感想：

1、到现在还是只知道概念，无法实际操作，基础太弱有点无从下手的感觉；
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->

今日完成：

Uniswap v2止损订单demo学习完成：50%；

今日感想：

看文档半天不如上手操作一次；
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->


今日完成：

将uniswapv2 demo在本地启动完成；
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->



今日完成：

今天本地搭建foundry环境，将basic demo跑了一遍；第一次是哟个foundry，还是比hardhat感觉精简一些；

今日感想：

ai是个好老师，好的大模型就是超级好老师，教程实在是太简略了，还是得问gpt才行；
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->




今日完成：

1、今天重新读了一遍官方文档，并且重新把之前布置过的basic demo对照着文档理解重新看了一遍，尝试梳理一下Reactive的最小必要MVP：

**Contract：源合约，部署在源链上，主要定义和发射事件，这里是发射Received事件，当获得一笔eth转账后，发射Received事件；**  
**Callback：目标合约，部署在目标链上，主要定义定义和发射事件，这里是定义CallbackReceived和发射CallbackReceived事件，重点是重写了AbstractCallback 中的Callback函数，从Callback函数中发射CallbackReceived事件；**

**ReactiveContract：反应合约，部署在Reactive Network和ReactVM上，主要是绑定传入的目标链订阅事件，然后重写AbstractReactive 的React函数，直接调用callback方法，**

bytes memory payload = abi.encodeWithSignature(“callback(address)”, address(0));

emit Callback(destinationChainId, callback, GAS\_LIMIT, payload);

**这里为什么传address(0)作为参数还没懂，可能是占位符，最后把目标链，载荷等作为参数发射Callback事件；**

**睿应层自建了个Reactive Network网络，持续不断检测evm的事件，可能是轮询evm的日志系统（这个需要了解），如果有对应已经注册的事件直接提交抓取到内容给ReactVM来处理，触发对应ReactiveContract**合约的**React方法；**
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->






今日完成：

1、Dev 文档核心模块学习；  
2、demo跟敲；

今日感想：

1、今天学习时间不够，明天补上
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->







今日完成：

1、睿应层（Reactive Network）官网 Overview[https://reactive.network/；](https://reactive.network/)

2、开发文档全部学习完成；

3、完成挑战1，在sepolia上看到睿应层完整调用事件过程；

今日感想：

1、今天跟着把挑战1的内容一步一步部署，看到结果感觉还是挺复杂的；

2、还没来得及细看内部的事件发射和调用；
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->








今日完成：

1、睿应层（Reactive Network）官网 Overview学习完成；

2、Reactive Contracts 文档学习到Lesson 3；

今日感想：

1、到现在还是只知道概念，无法实际操作，基础太弱有点无从下手的感觉；
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->









今日完成：

1、[http://reactive.network](http://reactive.network)官网overview和文档查看学习完成50%，测试网eth操作转换REACT代币，了解整个reactive.network的作用框架等等；

今日感想：

1、参与睿应层框架学习第一天，纯自学，像海绵一样吸取水分，希望能够在这个圈子里能有自己的作用；
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
