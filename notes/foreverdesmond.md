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
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
今天的学习重点从理论理解转向实际开发实践，开始尝试运行官方示例并熟悉 Reactive Network 的开发流程。

首先根据官方开发文档完成了开发环境的准备，并进一步阅读了 Reactive Developer Introduction，对 Reactive 合约的运行流程有了更清晰的认识。相比传统部署在 Ethereum Virtual Machine 上的智能合约，Reactive 合约需要运行在 ReactVM 环境中，通过监听链上事件来触发执行逻辑，因此在开发思路上更接近事件驱动的程序设计模式。

在代码实践方面，今天重点研究了官方仓库 reactive-smart-contract-demos 中的 basic 示例。通过阅读示例代码，逐步理解了 Reactive Contract 的基本结构，包括事件监听、触发条件以及执行逻辑的组织方式。同时也了解了示例中不同模块的职责，例如合约监听的事件来源、触发后的执行逻辑以及如何与链上状态进行交互。

在实际操作上，今天已经完成了示例项目的本地编译，并尝试按照文档步骤运行基础 Demo，成功理解了示例合约的运行流程和整体执行逻辑。目前主要还是以阅读代码和理解机制为主，对关键函数和事件触发流程做了一些简单的调试和验证。

通过今天的实践，对 Reactive Network 的开发模式有了更直观的理解：开发者只需要定义需要监听的链上事件以及对应的执行逻辑，Reactive Network 就可以在事件发生时自动触发合约执行，从而实现链上自动化任务。

当前进度：

\* 已完成 Reactive 开发环境准备

\* 已编译并运行官方 basic Demo

\* 已理解 Reactive Contract 的基本代码结构与执行流程

明天的计划：

明天计划根据 Challenge 要求，尝试编写或修改一个简单的 Reactive Smart Contract，并完成第一阶段的任务提交。
<!-- DAILY_CHECKIN_2026-03-12_END -->

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
