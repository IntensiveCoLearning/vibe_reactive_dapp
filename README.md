# Let’s vibe Reactive dApp

## 介绍

「Let’s vibe Reactive dApp 」由 ETHPanda 与 LXDAO 联合发起，Reactive Network 提供技术支持，面向具备 Solidity / EVM 基础的开发者。本次共学聚焦事件驱动智能合约模型，系统理解 Reactive 的执行逻辑与架构设计，并完成一个可在测试网运行的端到端 Demo。

目标是跑通完整链路：**Event → Reactive Logic → Callback Transaction**，真正理解事件驱动 dApp 的构建方式。
## 关键词

ReactVM, Event-Driven, Automation
## 面向人群

- 了解 Solidity / EVM 的开发者
- 希望构建自动化 DeFi 或跨链应用的 Builder
- 对事件驱动架构与智能合约执行模型感兴趣的工程师
- 愿意每日学习打卡、系统理解 Reactive 技术模型的学习者

无需已有 Reactive 经验，但需要具备基础开发能力与持续投入的时间
## 报名时间

- 报名开始时间：2026-03-02
- 报名结束时间：2026-03-08
## 共学时间

- 共学开始时间：2026-03-09
- 共学结束时间：2026-03-22
## 发起人

- 姓名：Luna
- GitHub ID：nanakodesuu
- Telegram：nanakodesu
- Email：nanakodesyo@gmail.com
## 发起组织

  [Reactive](https://reactive.network/) <img alt="organization-logo" height="60px" width="60px" src="https://avatars.githubusercontent.com/u/164997232?s=200&v=4" />

  [LXDAO](https://lxdao.io/) <img alt="organization-logo" height="60px" width="60px" src="https://cdn.lxdao.io/bafkreiay6vxsvv3ksxr75lzzt3iqy3zja3o2epuxh47ivs24p2xs3awexm.png" />

  [ETHPanda](https://ethpanda.org/) <img alt="organization-logo" height="60px" width="60px" src="https://cdn.lxdao.io/10aed40b-4786-4c2b-aaaa-b7d8a119c00e.png" />


## 社群

微信群二维码：![微信群二维码](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/muxin-web3/images/2026-02-26-1772088519679-b779646e41bea2ced419c9186ac2a118.jpg)

微信联系人：XiaoHai67890
## 学习资料/课程安排

### 第一阶段：理解 Reactive 思维

**学习目标：**

- 理解 Reactive 与传统 EVM 的根本区别
- 核心架构：ReactVM + Reactive Network 环境
- 理解“事件驱动智能合约”，什么是 reactive smart contracts（睿应式智能合约）
- 知道 Reactive 适合解决什么问题

**学习材料：**

- Reactive Network 官网 Overview
    
    [https://reactive.network/](https://reactive.network/?utm_source=chatgpt.com)
    
- Reactive Contracts 文档
    
    https://dev.reactive.network/education/module-1/reactive-smart-contracts
    
- 主网，测试网以及水龙头信息
    
    https://dev.reactive.network/reactive-mainnet
    
- 官方 Ecosystem 案例
    
    https://reactive.network/ecosystem#cases
    
- Dev 文档 Introduction
    
    [https://dev.reactive.network/education/introduction](https://dev.reactive.network/education/introduction?utm_source=chatgpt.com)
    

**挑战任务：**

请根据指引完成挑战：[https://ethpanda.notion.site/312bbd63be878004a897e05a4841f7d4](https://www.notion.so/312bbd63be878004a897e05a4841f7d4?pvs=21)


### 第二阶段：理解 Reactive 技术结构与开发模型

**学习目标：**

- 理解 Reactive Contract 的组成结构
- 理解 Subscribe / Trigger / Callback 模型
- 理解 ReactVM 执行逻辑
- 理解跨链和自动化机制

**学习材料：**

- Dev 文档核心模块：[https://dev.reactive.network/](https://dev.reactive.network/?utm_source=chatgpt.com)
    - [Reactive Library (核心开发库) — 提供回调/订阅接口等组件](https://dev.reactive.network/reactive-library?utm_source=chatgpt.com)
- **GitHub —— 欢迎给我们点个 ⭐！如果有任何可以进一步改进的地方，也请告诉我们。**
    
    https://github.com/Reactive-Network/reactive-smart-contract-demos
    
    - 如有需要，可以使用 Stop Order 作为脚手架参考：
        
        https://github.com/Reactive-Network/reactive-smart-contract-demos/tree/main/src/demos/uniswap-v2-stop-order
        
- 订阅机制
https://dev.reactive.network/subscriptions
- 事件与回调
https://dev.reactive.network/events-&-callbacks
- ReactVM 双状态模型
https://dev.reactive.network/reactvm
- 经济模型
https://dev.reactive.network/economy
- GitHub 示例代码
    
    [https://github.com/Reactive-Network](https://github.com/Reactive-Network?utm_source=chatgpt.com)
    
- Uniswap V2 止损示例代码
https://github.com/Reactive-Network/reactive-smart-contract-demos/tree/main/src/demos/uniswap-v2-stop-order

**挑战任务：**

请根据指引完成挑战：[https://ethpanda.notion.site/311bbd63be87809f9410c6fe8f8daff5?pvs=73](https://www.notion.so/311bbd63be87809f9410c6fe8f8daff5?pvs=21)


### 第三阶段：动手实践 + Casual Hackathon 准备

**学习目标：**

前端：

- 能连接钱包并切换到：Origin 链与 Lasna 链
- 做一个最小交互：能够触发 Origin 事件
- 能够展示 Reactive 三段链路的时间线（Origin- Reactive- Destination）

后端：

- 能同时监听至少两条链，把事件统一成结构
- 实现推送通道（SSE 或 WebSocket 二选一）
- 能调用 RNK 专用 RPC 方法来进行验证
- 能把部署地址对应的 RVM 地址、reactive 交易状态作为排错信息返回给前端（或在日志里输出），并指导用户去 Reactscan 对照查看

智能合约：

- 理解双状态：Reactive 合约会在 Reactive Network 与私有 ReactVM 各有一份实例、状态隔离
- 写一个只负责 `emit Event(...)` 的合约，确保事件签名（topic0）稳定、并且参数里至少包含一个 `indexed`（Origin）
- 回调入口函数，并强制 **第一个参数为 `address`**（Destination）
- 在 constructor 里调用系统合约 `subscribe(chainId, originContract, topic0..topic3)` 建立订阅，并理解过滤维度就是 chainId,合约地址,topics 的等值匹配（Reactive 的 Subscribe 部分）
- 实现 `react(LogRecord)`：能从 log 中读出 topics/data，做条件判断（阈值,白名单,去重），并产出可观测事件（便于 UI/后端确认触发过）（Reactive 的 LogRecord 部分）
- 能在 Lasna 部署 Reactive 合约，并确认系统合约地址固定为 `0x…fffFfF`

**学习材料：**

- Dev Guide（部署与测试部分）
    
    [https://dev.reactive.network/](https://dev.reactive.network/?utm_source=chatgpt.com)
    
- 往期 Workshop
    
    https://youtu.be/PnPIHVKPKgo?si=ENJcLHFikNB0wQK6
    
- Reactive Network Ecosystem 页面（找灵感）
    
    https://reactive.network/ecosystem#cases
    

### 社媒与社区

[Twitter (EN)](https://x.com/0xReactive) | [Twitter (CN)](https://x.com/0xReactive_cn) | [Discord](https://discord.com/invite/SaZAfkgZhj) | [Telegram](https://t.me/reactivedevs)


## 共学激励

- 在共学过程中表现优秀的同学，将有机会加入 Reactive 中文大使 Tier 1，并进一步晋升至 Tier 2（Tier 2 将根据实际贡献获得相应激励与支持）。
- 在第二阶段黑客松中，若产出优秀 Demo 或实现产品落地，将有机会获得 Reactive 官方 Dev Fund 资金支持与资源扶持。


## 更多信息

- 整个学习过程以自驱为主，基于官方资料进行学习，并在社区内讨论与参与社区会议。
- 参与者需每日发布学习笔记打卡，所有学习记录将开源至 GitHub。
- 残酷共学期间，欢迎在社区内随时提问与交流。
- 参加共学营的朋友请加入 Reactive 中文社区，添加运营微信 XiaoHai67890，并备注【睿应层中文社群】。
  扫码加入：
  ![b779646e41bea2ced419c9186ac2a118](https://github.com/user-attachments/assets/0d908cc0-6f0e-4b2c-8e24-fa1c19d513ea)


## 残酷共学打卡记录表

✅ = Done ⭕️ = Missed ❌ = Failed

<!-- START_COMMIT_TABLE -->
| Name | 3.09 | 3.10 | 3.11 | 3.12 | 3.13 | 3.14 | 3.15 | 3.16 | 3.17 | 3.18 | 3.19 | 3.20 | 3.21 | 3.22 |
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| [aiyoudiao](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/aiyoudiao.md) | ⭕️ | ✅ | ⭕️ | ❌ | | | | | | | | | | |
| [pillowtalk-Qy](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/pillowtalk-Qy.md) | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ |   | |
| [Fuyew1](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Fuyew1.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [liwnldutng](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/liwnldutng.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Bill306](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Bill306.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [QingQiuGeek](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/QingQiuGeek.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [ShawnX-F](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/ShawnX-F.md) | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ |   | |
| [Cap-bit-mint](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Cap-bit-mint.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ |   | |
| [Pluto417-Qing](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Pluto417-Qing.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [SU-AN-coder](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/SU-AN-coder.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [JunjiaYang](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/JunjiaYang.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [khakili](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/khakili.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [W5W8L9jlu](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/W5W8L9jlu.md) | ⭕️ | ✅ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | | |
| [Karynam2](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Karynam2.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | |
| [zoeyz3](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/zoeyz3.md) | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | |
| [gnihTehT](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/gnihTehT.md) | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ |   | |
| [fenixIves](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/fenixIves.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | |
| [enderzcx](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/enderzcx.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ⭕️ |   | |
| [JintolChan](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/JintolChan.md) | ⭕️ | ✅ | ✅ | ⭕️ | ✅ | ❌ | | | | | | | | |
| [ggus39](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/ggus39.md) | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | |
| [mizuki258](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/mizuki258.md) | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | | |
| [yly46967-source](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/yly46967-source.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ✅ | ⭕️ | ✅ | ⭕️ | ❌ | | |
| [taozhiyuzhuo](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/taozhiyuzhuo.md) | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | | |
| [Tadaaaaaaaaa](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Tadaaaaaaaaa.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [resurrection-i](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/resurrection-i.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | |
| [Uoghluvm](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Uoghluvm.md) | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | |
| [Ryat2899](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Ryat2899.md) | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | | |
| [JadeTwinkle](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/JadeTwinkle.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ⭕️ | ⭕️ | ❌ | | | | |
| [liji3597](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/liji3597.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [Saudade77](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Saudade77.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [yzxian-11](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/yzxian-11.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [fzwy2785](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/fzwy2785.md) | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | |
| [isethan18](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/isethan18.md) | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ |   | |
| [yuyang128](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/yuyang128.md) | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | |
| [Amireux123138](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Amireux123138.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [explorerer](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/explorerer.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [sweetsky123](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/sweetsky123.md) | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | | |
| [just4zeroq](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/just4zeroq.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Mosssi](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Mosssi.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [tofudfy](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/tofudfy.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ⭕️ | ⭕️ | ✅ | ✅ | ✅ |   | |
| [Wea1her](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Wea1her.md) | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | | |
| [xmhhmx](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/xmhhmx.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [jishukuangzi](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/jishukuangzi.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [maxzhangg](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/maxzhangg.md) | ⭕️ | ✅ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | | |
| [Thomas-YHS](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Thomas-YHS.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ |   | |
| [xingyanghao0-hub](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/xingyanghao0-hub.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [jimmyYSY](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/jimmyYSY.md) | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | |
| [lebronboy500](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/lebronboy500.md) | ⭕️ | ⭕️ | ✅ | ❌ | | | | | | | | | | |
| [A-Pang](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/A-Pang.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [L-Macy](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/L-Macy.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [kuove](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/kuove.md) | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ❌ | | | | | | | | |
| [vstralcn](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/vstralcn.md) | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | | |
| [daidaidawang](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/daidaidawang.md) | ✅ | ⭕️ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ |   | |
| [patrick-star-10](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/patrick-star-10.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | |
| [emptyshell424](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/emptyshell424.md) | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ✅ | ❌ | | | |
| [Xboxpig](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Xboxpig.md) | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ⭕️ |   | |
| [hhh835](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/hhh835.md) | ⭕️ | ⭕️ | ✅ | ❌ | | | | | | | | | | |
| [chiahao-dev](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/chiahao-dev.md) | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | | |
| [VigorQuant](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/VigorQuant.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Rose838](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Rose838.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [may-tonk](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/may-tonk.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [amzukiii](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/amzukiii.md) | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | |
| [jcy-yhx](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/jcy-yhx.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ✅ | ✅ | ✅ | ⭕️ |   | |
| [dadwawd1-ops](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/dadwawd1-ops.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | |
| [panrui1984](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/panrui1984.md) | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | |
| [zk1047740032](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/zk1047740032.md) | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | | |
| [zhibo7060-gif](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/zhibo7060-gif.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [XuetaoZhang](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/XuetaoZhang.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ |   | |
| [bonujel](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/bonujel.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [cxh993505935-sys](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/cxh993505935-sys.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [kachrel](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/kachrel.md) | ⭕️ | ✅ | ⭕️ | ❌ | | | | | | | | | | |
| [FairyTaleBliss](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/FairyTaleBliss.md) | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ⭕️ |   | |
| [2273310475-dev](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/2273310475-dev.md) | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [Toby1009](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Toby1009.md) | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | |
| [Miranda-777](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Miranda-777.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Evanlove4ever](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Evanlove4ever.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [lorstyang](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/lorstyang.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [yayehuang2-ship-it](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/yayehuang2-ship-it.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [starrujian](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/starrujian.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ✅ | ✅ |   | |
| [joycefey](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/joycefey.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [so-ki](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/so-ki.md) | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | |
| [0xBrick-Li](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/0xBrick-Li.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [286748501-png](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/286748501-png.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [New-zy](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/New-zy.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [7metachain](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/7metachain.md) | ⭕️ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | | | |
| [leopc999](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/leopc999.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [hwish39-byte](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/hwish39-byte.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [KMSHSF](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/KMSHSF.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [SArreic](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/SArreic.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [kotoYoshi](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/kotoYoshi.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Riemann666](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Riemann666.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Sacultor](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Sacultor.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [ghostin1024](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/ghostin1024.md) | ✅ | ⭕️ | ✅ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | |
| [yedeyu](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/yedeyu.md) | ⭕️ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ❌ | | | | | | | |
| [finish-blip](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/finish-blip.md) | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ❌ | | | | | | | | |
| [drinkingmorewater](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/drinkingmorewater.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [barryxu-0410](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/barryxu-0410.md) | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ✅ | ⭕️ | ⭕️ | ❌ | | | |
| [Susie-beep](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Susie-beep.md) | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ |   | |
| [wodeche](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/wodeche.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [haolan0427](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/haolan0427.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Joyceyuuu](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Joyceyuuu.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [luuzuofan-design](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/luuzuofan-design.md) | ⭕️ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ✅ |   | |
| [vergissxie](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/vergissxie.md) | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ |   | |
| [1145141926](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/1145141926.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [azolonev-debug](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/azolonev-debug.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ✅ | ✅ | |
| [koushuijinne](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/koushuijinne.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Duamixu1](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Duamixu1.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [qianliFISH](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/qianliFISH.md) | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ❌ | | | | | | | | |
| [RJx233](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/RJx233.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [foreverdesmond](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/foreverdesmond.md) | ⭕️ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | |
| [SylvanLIUyu](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/SylvanLIUyu.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [ViVi-SH](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/ViVi-SH.md) | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | | |
| [Wwangjinghan](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Wwangjinghan.md) | ⭕️ | ⭕️ | ✅ | ✅ | ❌ | | | | | | | | | |
| [317232](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/317232.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [goodperson888](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/goodperson888.md) | ⭕️ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | |
| [hy3917-code](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/hy3917-code.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ✅ | ⭕️ | ⭕️ | ❌ | | | |
| [zhangmuf](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/zhangmuf.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [wwwjy1220](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/wwwjy1220.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [nanakodesuu](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/nanakodesuu.md) | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | |
| [shaopingZH](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/shaopingZH.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ✅ | ⭕️ | ❌ | | | |
| [0x-IHRR](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/0x-IHRR.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Xiaonan2020](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Xiaonan2020.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Carl040814](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Carl040814.md) | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ✅ | ⭕️ | ⭕️ | ❌ | | | |
| [hynfrank](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/hynfrank.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | |
| [0xClareYang](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/0xClareYang.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [fox896](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/fox896.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | |
| [swen-chan](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/swen-chan.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ⭕️ | ✅ | ⭕️ | ❌ | | | |
| [kvxunz](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/kvxunz.md) | ⭕️ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ❌ | | | |
| [2831753275-Tang](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/2831753275-Tang.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [yangyang-hub](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/yangyang-hub.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | |
| [annecn037](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/annecn037.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [nu1lspaxe](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/nu1lspaxe.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [klizz111](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/klizz111.md) | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [fca2025774696-art](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/fca2025774696-art.md) | ⭕️ | ⭕️ | ✅ | ❌ | | | | | | | | | | |
| [jhy-3](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/jhy-3.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [JonathanQUANLEE](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/JonathanQUANLEE.md) | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [YTT-Iris](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/YTT-Iris.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [zhuoyu18](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/zhuoyu18.md) | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | |
| [fylcr](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/fylcr.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [yoona333](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/yoona333.md) | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [wangty1013tianna](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/wangty1013tianna.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [huawanrr](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/huawanrr.md) | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | |
| [XGe711](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/XGe711.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [Lansyue](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Lansyue.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [jochenai](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/jochenai.md) | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | |
| [arangpemi](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/arangpemi.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [runrunrunz](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/runrunrunz.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [ShihaoZhou-NEU](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/ShihaoZhou-NEU.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [zblingling](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/zblingling.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Zhao444Four](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Zhao444Four.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | |
| [G-H11](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/G-H11.md) | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ⭕️ | ❌ | | | | |
| [hyr0ky](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/hyr0ky.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [LinLyra](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/LinLyra.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [narnona](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/narnona.md) | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ❌ | | | | | | | | |
| [slwyts](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/slwyts.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [haoshidoufasheng-dev](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/haoshidoufasheng-dev.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ |   | |
| [xiqing21](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/xiqing21.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Kwong-WJTECH](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Kwong-WJTECH.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [ershisihuasheng2003](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/ershisihuasheng2003.md) | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | |
| [irinaguo](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/irinaguo.md) | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ✅ |   | |
| [FSDSCCEVVS](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/FSDSCCEVVS.md) | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | |
| [XiaoHai67890](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/XiaoHai67890.md) | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | |
| [3200459199](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/3200459199.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [huahuahua1223](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/huahuahua1223.md) | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ✅ | ✅ | |
| [tf171398413-lgtm](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/tf171398413-lgtm.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [mayuxaing](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/mayuxaing.md) | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | |
| [PaulCoinmanlabs](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/PaulCoinmanlabs.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ❌ | | | |
| [hhjthhjtcv-sudo](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/hhjthhjtcv-sudo.md) | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | |
| [zzz100868](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/zzz100868.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [MarnieWu](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/MarnieWu.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ✅ | |
| [SeafaringSoul](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/SeafaringSoul.md) | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | | |
| [oiGho5t](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/oiGho5t.md) | ⭕️ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | | | |
| [surdress](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/surdress.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ❌ | | | |
| [JackCC703](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/JackCC703.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [SeeMoon357](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/SeeMoon357.md) | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ✅ | ✅ |   | |
| [potato89757](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/potato89757.md) | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | |
| [zhao-si-yi](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/zhao-si-yi.md) | ✅ | ⭕️ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ✅ | ✅ | ❌ | | |
| [merlin-ecde](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/merlin-ecde.md) | ✅ | ⭕️ | ⭕️ | ✅ | ✅ | ❌ | | | | | | | | |
| [fuyushiphilip](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/fuyushiphilip.md) | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [llyzsam](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/llyzsam.md) | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [Eddie-534](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Eddie-534.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [Im-Sue](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Im-Sue.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [beautifulrem](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/beautifulrem.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ |   | |
| [Leahleaha](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Leahleaha.md) | ⭕️ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | |
| [henanshifandaxue](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/henanshifandaxue.md) | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | |
| [gitgdut](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/gitgdut.md) | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | |
| [YuChanGongzhu](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/YuChanGongzhu.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [a0905087259-sketch](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/a0905087259-sketch.md) | ⭕️ | ⭕️ | ✅ | ❌ | | | | | | | | | | |
| [Jack-OuCJ](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Jack-OuCJ.md) | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | | |
| [NinaChow09](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/NinaChow09.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [wanghanyu0120](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/wanghanyu0120.md) | ✅ | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | | | | | |
| [Yuntwo](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Yuntwo.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [zouxiaomin512-ctrl](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/zouxiaomin512-ctrl.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [ysy040204-alt](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/ysy040204-alt.md) | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [ads12306](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/ads12306.md) | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ⭕️ | ⭕️ | ❌ | | | | |
| [zhaojinxiu6](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/zhaojinxiu6.md) | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   | |
| [sgxy975-bit](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/sgxy975-bit.md) | ✅ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ⭕️ |   | |
| [Lyu23](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Lyu23.md) | ⭕️ | ✅ | ✅ | ✅ | ⭕️ | ❌ | | | | | | | | |
| [zhouyx2026](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/zhouyx2026.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [lyysksj](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/lyysksj.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [dl4987638](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/dl4987638.md) | ✅ | ⭕️ | ✅ | ⭕️ | ✅ | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ |   | |
| [L19711221-debug](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/L19711221-debug.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [big-dudu-mosty](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/big-dudu-mosty.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [qiaopengjun5162](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/qiaopengjun5162.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [fuujiro](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/fuujiro.md) | ✅ | ✅ | ✅ | ✅ | ⭕️ | ✅ | ✅ | ⭕️ | ⭕️ | ❌ | | | | |
| [Archsoos](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Archsoos.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [xxy666-nb](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/xxy666-nb.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [hu-Angie](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/hu-Angie.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [mkbkoaa](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/mkbkoaa.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Abcrypto100](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Abcrypto100.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Mugeng-su](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Mugeng-su.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [yjj810815-cloud](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/yjj810815-cloud.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
| [Rohit12ka](https://github.com/IntensiveCoLearning/vibe_reactive_dapp/blob/main/notes/Rohit12ka.md) | ⭕️ | ⭕️ | ❌ | | | | | | | | | | | |
<!-- END_COMMIT_TABLE -->

















































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































<!-- STATISTICALDATA_START -->
## 统计数据

- 总参与人数: 0
- 完成人数: 0
- 完成用户: 
- 全勤用户: 
- 淘汰人数: 0
- 淘汰率: 0.00%
<!-- STATISTICALDATA_END -->


## 报名和打卡规则

- 报名：https://www.notion.so/lxdao/232dceffe40b8030993ad26f2eb6bed2

- 打卡：https://www.notion.so/lxdao/232dceffe40b80508330c5ee936d4dab


## 请假规则

每周请假 2 次


