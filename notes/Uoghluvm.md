---
timezone: UTC+8
---

# SiC

**GitHub ID:** Uoghluvm

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->
\### 1. 宏观架构图：跨链事件流

事件如何从源链（Origin）经过 Reactive Network 最终到达目标链（Destination）。

\`\`\`text

\[ 源链: Origin Chain \] \[ Reactive Network \] \[ 目标链: Destination \]

(如: Ethereum Sepolia) (逻辑处理中心) (如: Base Sepolia)

| | |

\[ 用户合约 \] | |

| | |

触发事件 (Log) ----------------> \[ 监听器 \] |

| (根据 Subscription 过滤) |

| | |

| \[ Reactive Contract \] |

| 执行 react() 逻辑 ----------------> \[ 回调代理 \]

| | (Callback Proxy)

| | |

| | \[ 最终回调合约 \]

| | (执行跨链动作)

\`\`\`

\---

\### 2. 核心逻辑图：双状态环境 (RN vs. ReactVM)

响应式合约如何在两个环境之间切换

\`\`\`text

+-----------------------------------------------------------+

| Reactive Smart Contract (RSC) |

+------------------------------+----------------------------+

|

检测环境 (detectVm / check System Contract)

/ | \\

/ | \\

\[ 环境 A: RN 状态 \] | \[ 环境 B: ReactVM 状态 \]

(Reactive Network) | (执行隔离沙盒)

| | |

作用: 管理与配置 | 作用: 自动化响应

\- 订阅新事件 (Subscribe) | - 接收 L1 Event 数据

\- 修改合约参数 | - 计算跨链指令

\- 充值 Gas | - 发起 Callback

| | |

触发方式: 用户手动交易 | 触发方式: 系统自动触发

\`\`\`

\---

\### 3. 生命周期图：一个事件的“一生”

按照时间顺序描述变量在系统中的流向。

\`\`\`text

1\. 部署阶段

开发者 -> \[ 订阅指令 \] -> SYSTEM\_CONTRACT (告知要监听 ORIGIN\_ADDR)

2\. 触发阶段

ORIGIN\_ADDR (源链) 发出 Log (Topic\_0, Data...)

|

V

3\. 捕获阶段

Reactive Network 节点捕获该 Log

|

V

4\. 执行阶段 (ReactVM)

调用 RSC.react(chain\_id, contract, topics, data...)

|

| (逻辑判断: 如果满足条件 A，则执行 B)

V

5\. 输出阶段 (Callback)

RSC 发出跨链指令 -> DESTINATION\_CALLBACK\_PROXY\_ADDR

|

V

6\. 最终落脚

CALLBACK\_ADDR (目标链合约) 接收指令并修改状态

\`\`\`
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->

(wish no break)  

Got a new unitree Go2 Air, working on secondary development  
  
New idea: what if I use reactive to monitor real world with Go2, then act between chains?  
  
But why? where? how?
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
