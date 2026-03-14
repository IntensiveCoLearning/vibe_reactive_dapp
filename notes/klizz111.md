---
timezone: UTC+8
---

# klizz

**GitHub ID:** klizz111

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->
### **1\. 事件的本质**

-   **定义**：在区块链上下文中，事件是智能合约执行过程中产生的日志记录（Logs）。
    
-   **结构**：每个事件包含特定的数据字段，其中最重要的是 **Topic 0**（事件的唯一标识符/签名哈希），它决定了事件的类型（例如 `Transfer`、`Swap` 等）。
    
-   **作用**：事件是链上状态变化的“信号”，反应式合约正是通过监听这些信号来触发自动化逻辑。
    

### **2\. 监控与过滤机制**

Reactive Network 并不盲目处理所有链上数据，而是通过高效的过滤机制工作：

-   **基于 Topic 0 的订阅**：用户在创建反应式合约时，必须指定要监听的 **Topic 0**。网络节点只关注匹配该特定哈希值的事件。
    
-   **多链支持**：系统可以同时监控多条区块链（如 Ethereum, Arbitrum, Optimism 等）上的事件。
    
-   **精准捕获**：一旦目标链上发生了匹配 Topic 0 的交易，该事件会被立即捕获并传递给对应的反应式合约进行处理。
    

### **3\. 从事件到执行的流程**

文章描述了事件触发自动化操作的完整生命周期：

1.  **发生**：用户在某条链上发起交易（如在 Uniswap 上进行 Swap），合约 emit 一个事件。
    
2.  **捕获**：Reactive Network 的节点扫描新区块，发现匹配的 Topic 0。
    
3.  **验证与排序**：网络验证事件的有效性，并将其放入执行队列。
    
4.  **触发执行**：系统自动调用已部署的反应式合约，将事件数据作为输入参数传入。
    
5.  **动作**：反应式合约根据预设逻辑执行操作（如发起新的交易、更新状态等）。
    

### **4\. 关键优势**

-   **实时性**：相比传统的外部轮询（Polling）或中心化机器人，这种基于事件驱动的架构能更快速地响应链上变化。
    
-   **去中心化**：事件的监听和处理由 Reactive Network 的去中心化节点网络完成，无需依赖单一的可信第三方监控服务。
    
-   **灵活性**：开发者可以针对任意标准 ERC-20、ERC-721 或自定义合约的事件编写逻辑，只要知道其 Topic 0 即可。
    

### **总结**

**事件（Events）** 是 Reactive Network 的“感官”。通过监听特定的 **Topic 0**，反应式合约能够实时感知链上发生的任何相关活动，并立即做出反应。这种机制将原本被动的智能合约转变为能够主动响应环境变化的自动化代理，是实现无需人工干预的复杂 DeFi 策略（如止损、套利、再平衡）的技术基石。
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->

-   ReactVM: ReactiveNetwork中的核心组件，负责执行Reactive Contract，并发送回调信息到目标链
    
-   归属权：Reactive COntract会部署到基于同一个地址的EOA的独有的ReactVM，并且同一个ReactVM中的合约可以进行状态共享
    
-   每个合约具有两种状态：ReactVM，负责emit event；Reactive Network State，响应EOA的function call，Administrative actions such as `pause()` may exist in the Reactive Network state
    
-   ![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/klizz111/images/2026-03-13-1773405875112-image.png)
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->


-   参加了Workshop
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->




-   完成 **Reactive 挑战「第一关」**
    
-   了解了REACTIVE的调用和部署流程
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->





## Day 01

-   set up dev env at [https://github.com/klizz111/Reactive](https://github.com/klizz111/Reactive/pull/1)
    
-   looking through [https://dev.reactive.network/#step-1--reactive-basics](https://dev.reactive.network/#step-1--reactive-basics)
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
