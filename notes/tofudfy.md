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
