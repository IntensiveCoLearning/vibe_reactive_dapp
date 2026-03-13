---
timezone: UTC+8
---

# dadwawd1-ops

**GitHub ID:** dadwawd1-ops

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->
参加co-learning
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->

参加reactive workshop
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->


### Reactive Contracts 与传统智能合约的区别

| 维度 | 传统智能合约 | Reactive Contracts (RCs) |
| --- | --- | --- |
| 执行方式 | 被动（Passive） | 主动反应（Reactive） |
| 触发条件 | 必须由 EOA（外部账户）直接发交易 | 持续监控区块链事件，自动触发 |
| 执行主体 | 依赖外部调用 | 合约自己决定何时执行 |

**核心一句话**：传统合约“等人来叫它”，RCs“自己盯着链上事件，一旦满足条件就立刻行动”。

### 3\. 控制反转（Inversion of Control）—— 最重要概念

-   传统方式：需要外部机器人/脚本持续监控链 + 持有私钥 + 手动发起交易（容易出问题、中心化风险高）。
    
-   RCs 方式：把“监控 + 决策 + 执行”全部交给合约本身，**完全去中心化、自动化、信任最小化**。
    
-   结果：不再需要任何人/机器人干预，逻辑全部跑在链上。
    

### 4\. Reactive Contract 内部工作流程

1.  **部署时声明**：
    
    -   要监听哪些链
        
    -   要监听哪些合约
        
    -   要监听的事件（Event Topic 0）
        
2.  **事件发生时**：
    
    -   RCs 立刻捕获事件数据
        
    -   执行预设逻辑（可做计算）
        
    -   支持**状态化**（Stateful）：可以累积历史数据，直到满足复合条件才行动
        
3.  **执行结果**：
    
    -   更新自身状态
        
    -   自动在 Reactive Network 内发起 EVM 交易（可信、快速、可靠）
        

### 5\. 实际用例（Use Cases）—— 最实用部分

① 多预言机数据收集

-   监听多个预言机合约的事件
    
-   聚合数据（如求平均值）
    
-   示例：体育比赛结果 → 自动触发信任less 赔付
    

② Uniswap 止损单（Stop Order）

-   实时监控 Uniswap 交易池的价格
    
-   价格到达预设止损点时，RCs **自动执行 Swap**
    
-   纯链上实现，无需第三方信任
    

③ DEX 套利（Arbitrage）

-   监控多个交易池的价格差
    
-   发现套利机会后立即执行（支持单链闪电贷或跨链）
    
-   比传统机器人更去中心化、更高效
    

④ 流动性池自动再平衡（Pools Rebalancing）

-   监控多条链上的流动性
    
-   自动加/撤资，实现跨交易所再平衡
    
-   适合专门为 RCs 设计的 DApp
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->



### 1\. 事件源合约 (Origin Contract)

-   **核心目标**：在源链（比如以太坊 Sepolia）上执行某项操作，并向外广播一个信号。
    
-   **学习重点 - 事件 (Events)**：在 Solidity 中，你需要使用 `event` 关键字定义一个事件（例如 `event Received(address indexed sender, uint256 amount)`），并在函数执行完毕时使用 `emit Received(msg.sender, msg.value)` 触发它。
    
-   **Reactive 的作用**：Reactive Network 的节点会在链下持续监听（Subscribe）这个特定的事件日志（Log）。
    

### 2\. 响应式合约 (Reactive Contract)

-   **核心目标**：部署在 Reactive Network 本身上，充当“大脑”和“中继”。它决定了什么时候、以什么条件触发目标链的动作。
    
-   **学习重点 - 合约继承与订阅**：
    
    -   这个合约通常需要继承官方提供的基础合约（如 `AbstractReactive`）。
        
    -   需要在构造函数中设置**订阅（Subscription）**，告诉网络你要监听哪个链（Origin Chain ID）、哪个合约地址，以及哪个特定的事件（通过 Topic 0 哈希来识别）。
        
-   **学习重点 - 条件判断与跨链Payload**：当监听到事件时，合约内的回调函数（如 `react()`）会被触发。你可以在这里编写逻辑（例如：判断金额是否大于 `0.001 ether`）。如果条件满足，它会将需要传递给目标链的数据打包成 Payload，并发出一个特定的 `Callback` 事件，指示 Reactive 节点去目标链执行交易。
    

### 3\. 回调合约 (Destination Contract)

-   **核心目标**：部署在目标链上，接收来自 Reactive Network 的指令并执行最终的业务逻辑。
    
-   **学习重点 - 权限控制 (Access Control)**：这是非常关键的一环！由于这是一个公开在链上的合约，你必须确保**只有** Reactive Network 授权的代理地址（Proxy Address）才能调用你的回调函数。通常你会写一个 `modifier onlyReactiveProxy()` 来拦截恶意的伪造调用。
    
-   **学习重点 - 数据解析**：合约需要能够解包并处理源链传过来的元数据（如原始发送者是谁、发生了什么）。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->




先打卡
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
