---
timezone: UTC+8
---

# Tofu

**GitHub ID:** tofudfy

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->
\# Reactive Network 学习总结

本阶段重点关注 Reactive Network 的 \*\*运行机制\*\*：即链上事件如何被监听、`react()` 函数如何执行，以及 callback 交易是如何生成的。

\---

\## 1. Reactive 核心执行流程

Reactive Network 的\*\*详细执行步骤：\*\*

1\. \*\*事件触发\*\*：某条源链上的智能合约产生并发出事件（event log）。

2\. \*\*链上监听\*\*：Reactive Network 的节点捕获到该事件。

3\. \*\*VM 调用\*\*：ReactVM 启动，调用对应 reactive contract 的 `react()` 函数。

4\. \*\*逻辑计算\*\*`react()` 函数根据接收到的事件内容执行预设的响应逻辑。

5\. \*\*交易构造\*\*：Reactive Network 根据执行结果构建 callback 交易。

6\. \*\*跨链执行\*\*：callback 交易被发送到目标链并执行。

\---

\## 2. ReactVM 的核心作用

ReactVM 是 Reactive Network 的执行环境，其核心职责包括：

\- \*\*解析链上事件（Log）\*\*

\- \*\*调用 Reactive Contract\*\*

\- \*\*执行 `react()` 函数逻辑\*\*

\- \*\*构造 Callback Transaction\*\*

可以将 ReactVM 理解为以下三个组件的结合体：

| 组件 | 功能 |

| :--------------- | :--------------------------------- |

| \*\*事件解析器\*\* | 解析从链上监听到的原始事件数据。 |

| \*\*合约执行环境\*\* | 安全地执行 reactive contract 的业务逻辑。 |

| \*\*交易生成器\*\* | 根据执行结果生成最终的 callback 交易。 |

因此，ReactVM 本质上是一个 \*\*事件驱动的、专门用于执行合约逻辑的轻量级运行时（Runtime）\*\*。

\---

\## 3. Callback Transaction 详解

\*\*Callback Transaction\*\* 是 Reactive Network 实现自动化交互的核心机制。

\*\*定义：\*\*

当 Reactive Network 检测到预先设定的链上事件后，由系统自动构造并发送到目标链的交易。

\*\*主要特点：\*\*

| 特性 | 说明 |

| :----------- | :--------------------------------------- |

| \*\*自动触发\*\* | 完全自动化，无需用户手动发送交易。 |

| \*\*跨链执行\*\* | 可以在与事件源链不同的另一条链上执行。 |

| \*\*可编程\*\* | 执行的具体逻辑完全由 `react()` 函数决定。 |

\*\*典型应用场景：\*\*

| 触发事件 | Callback 行为 |

| :------------------- | :-------------------------------- |

| DEX 发生大额 Swap | 在另一条链上执行套利交易。 |

| 借贷协议达到清算条件 | 自动执行 liquidation（清算）。 |

| 资产在源链被锁定 | 在目标链上触发等额资产的释放（跨链转移）。 |

\---

\## 4. Reactive Contract 生命周期

一个 Reactive Contract 的完整运行生命周期如下：

| 阶段 | 说明 |

| :------- | :----------------------------------------------- |

| \*\*1. 部署\*\* | 将写好的 reactive contract 部署到 Reactive Network 上。 |

| \*\*2. 监听\*\* | 合约通过代码指定需要监听的特定链、特定合约及特定事件。 |

| \*\*3. 触发\*\* | 当监听的链上事件发生时，系统自动触发该合约的 `react()` 函数。 |

| \*\*4. 执行\*\* | `react()` 函数执行，并根据逻辑生成 callback 交易。 |

| \*\*5. 完成\*\* | callback 交易被发送至目标链并成功执行，整个响应流程结束。 |

\*\*Reactive Contract 的核心接口：\*\*

\`\`\`solidity

react(LogRecord log)

\`\`\`

\- \*`LogRecord`\*\*：一个数据结构，封装了从源链监听到的完整事件信息。

\- \*`react()`\*\*：开发者实现的核心函数，在此定义系统对特定事件的响应逻辑。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->

# **Reactive Network 学习总结**

## **1\. Reactive Network 是什么**

**Reactive Network** 是一个用于事件驱动自动执行（event-driven automation）的区块链网络。

它通过监听链上事件，在 Reactive 网络中执行开发者编写的逻辑，并自动在目标链上触发交易。

开发者编写 **Reactive Contract**，Reactive 节点执行其中的 react() 逻辑。

## **2\. 工作流程**

Chain A

↓ 1. 事件产生（event log）

Reactive Network

↓ 2. 事件订阅触发

ReactVM

↓ 3. 执行 react(LogRecord)

生成 callback

↓

Chain B

↓ 4. 调用目标合约

完成自动执行

## **3\. Reactive Network 对比**

| 系统 | 核心定位 | 主要功能 | 触发方式 | 执行逻辑位置 | 信任模型 | 典型用途 |
| --- | --- | --- | --- | --- | --- | --- |
| Reactive Network | 自动化执行网络 | 监听事件并自动执行交易 | 链上事件订阅 | ReactVM | 信任 Reactive 网络 | 自动清算、套利、跨链触发 |
| Bot | 自动化程序 | 监听事件并发送交易 | 本地监听 | off-chain 程序 | 无需额外信任 | MEV、清算、套利 |
| LayerZero | 跨链消息协议 | 跨链消息传递 | 合约主动发送消息 | 目标链合约 | Oracle + Relayer | Omnichain 应用 |
| Chainlink | Oracle 网络 | 数据预言机 / 自动化 | 数据请求或条件触发 | Oracle 节点 | Oracle 网络 | 价格预言机、自动执行 |
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
