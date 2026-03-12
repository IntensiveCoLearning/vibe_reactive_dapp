---
timezone: UTC+8
---

# Richmond522

**GitHub ID:** foreverdesmond

**Telegram:**

## Self-introduction

Web3 实习计划 2025 冬季实习生

## Notes

<!-- Content_START -->
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
今天进一步阅读了官方 Overview 和开发文档，对 Reactive Network 的整体架构有了进一步步认识。重点理解了 Reactive Network 提供 Event Driven（事件驱动） 的执行模式，当链上事件发生时，系统可以自动触发合约执行。这个机制依赖其执行环境 ReactVM，能够监听链上事件并触发 Reactive Smart Contracts 自动运行。同时也理解了，由于链上出块时间的限制，Reactive技术并不适合做高频交易的底层原理。

代码方面，今天已经完成了官方 Demo 仓库的环境准备，并浏览了 reactive-smart-contract-demos 中 basic 示例的项目结构。目前代码仍处于阅读和理解阶段，还没有开始实际部署或运行合约。

明天计划开始实际运行 Demo，并尝试编译、部署第一个 Reactive Smart Contract，进一步理解其事件触发执行流程。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

今天由于时差，错过了Co-learning  

今天阅读了 **Reactive Network** 官网和开发者文档，对 Reactive 的核心理念有了初步了解。Reactive 通过 **ReactVM** 支持事件驱动的智能合约，使合约可以监听链上事件并自动执行逻辑，这与传统 **Ethereum Virtual Machine** 需要用户发送交易触发合约执行的模式不同。Reactive 提出的 **Reactive Smart Contracts** 可以在事件发生时自动响应，适合自动化 DeFi、跨链交互和链上自动化策略等场景。学习过程中我还发现，这种事件驱动模式和 .NET 开发中常见的 IoC（控制反转）思想有些相似，程序逻辑更多是由事件或框架触发，而不是主动调用，这种设计可以让系统更加解耦和自动化。
<!-- DAILY_CHECKIN_2026-03-10_END -->
<!-- Content_END -->
