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


