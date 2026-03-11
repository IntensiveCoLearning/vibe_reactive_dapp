---
timezone: UTC+8
---

# SSS

**GitHub ID:** patrick-star-10

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
# **今日学习笔记｜Reactive Contract 结构理解**

今天主要学习 **Reactive Contract 的组成结构与执行流程**。

Reactive Network 的核心思想是 **事件驱动（Event Driven）智能合约**。

与传统智能合约必须由用户主动调用不同，Reactive Contract 可以在 **链上事件发生时自动触发执行逻辑**。

* * *

# **一、Reactive Contract 一句话理解**

Reactive Contract 的核心流程可以概括为：

**Event → React → Callback**

即：

1.监听链上事件

2.触发 react() 执行逻辑

3.发送 callback 在目标链执行操作

* * *

# **二、Reactive 系统整体架构**

Reactive Contract 在整个系统中的位置如下：

```
Origin Chain (源链)
      │
      │ Event
      ▼
Reactive Network
      │
      │ trigger react()
      ▼
Reactive Contract
      │
      │ callback
      ▼
Destination Chain (目标链)
```

可以理解为：

```
监听 → 计算 → 执行
```

Reactive Network 本质上像一个 **链上自动执行引擎**。

* * *

# **三、Reactive Contract 的三个核心组成**

Reactive Contract 一般包含三个核心部分：

### **1.事件监听规则（Event Trigger）**

首先需要定义：

**监听哪些链上的事件**

例如：

-   某个合约的 Transfer 事件
    
-   DeFi 价格更新
    
-   用户存款事件
    

监听信息一般包含：

```
chainId
contractAddress
eventTopic
```

Reactive Network 会持续扫描这些事件。

一旦事件出现：

```
触发 react()
```

* * *

### **2\. react() 函数（核心逻辑）**

当监听到事件后，Reactive Network 会调用：

```
react()
```

这个函数就是：

**自动执行的策略逻辑**

例如：

```
如果价格 > 1000
执行卖出
```

伪代码示例：

```
function react(Log calldata log) external {

    uint price = getPrice();

    if(price > 1000){
        triggerSell();
    }

}
```

react() 可以理解为：

**策略计算引擎**

* * *

### **3.callback 回调执行**

Reactive Contract 本身不能直接改变其他链的状态。

因此 react() 会发送：

```
callback
```

例如：

```
callback(
    targetChain,
    targetContract,
    data
)
```

Reactive Network 会在 **目标链执行该函数调用**。

完整流程：

```
监听事件
   ↓
react()
   ↓
callback()
   ↓
目标链执行函数
```

* * *

# **四、Reactive Contract 完整执行流程**

完整执行过程：

```
1 监听源链事件

User Deposit

      ↓

2 Reactive Network检测到event

      ↓

3 调用 Reactive Contract

react(log)

      ↓

4 执行策略逻辑

if condition

      ↓

5 发送 callback

      ↓

6 目标链执行函数
```

本质可以理解为：

**链上自动机器人系统**

* * *

# **五、Reactive Contract 的开发思维**

Reactive 合约的开发思维和传统智能合约不同。

传统合约：

```
用户调用函数
```

Reactive 合约：

```
事件触发执行
```

开发者思考方式从：

```
用户会调用什么函数？
```

变成：

```
什么事件会触发我的逻辑？
```

这就是 **Reactive Programming（响应式编程）**。

* * *

# **六、Reactive Contract 的典型应用**

Reactive 合约可以实现很多自动化场景：

### **自动交易**

```
监听价格 → 自动买卖
```

### **自动清算**

```
监听抵押率 → 自动清算
```

### **自动止盈止损**

```
价格触发 → 自动卖出
```

### **跨链自动执行**

```
ETH价格变化
→ BSC执行交易
```

* * *

# **七、Reactive 与传统 Bot 的区别**

传统自动交易：

```
中心化服务器 Bot
```

Reactive Contract：

```
链上自动执行
```

因此可以理解为：

```
Reactive = 去中心化自动机器人
```

* * *

# **八、今日思考问题**

Reactive 的执行流程是：

```
Event → React → Callback
```

但如果发生以下情况：

源链交易先成功，事件被检测到并触发 react()，

随后源链发生 **reorg（区块重组）**，该事件被回滚。

那么：

**已经触发的 callback 是否会被撤销？**

或者：

Reactive 是否有机制保证最终一致性？

这是 Reactive 系统中值得深入思考的问题。

* * *

# **今日总结：**

今天主要理解了 Reactive Contract 的核心结构：

```
Event Trigger
React Logic
Callback Execution
```

Reactive Network 的本质是一个：

**事件驱动的链上自动执行系统。**

这种架构可以实现：

-   自动交易
    
-   自动清算
    
-   自动策略执行
    
-   跨链自动操作
    

是 DeFi 自动化的重要基础设施之一。

* * *
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->


# **今天完成了第一个挑战任务**

[https://github.com/patrick-star-10/reactive-network-day1](https://github.com/patrick-star-10/reactive-network-day1)
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->




# 今日学习内容

1.  **学习了 Reactive Network 的跨链事件与回调机制。**
    
2.  **Origin Contract（链 A）emit 事件，触发流程。**
    
3.  **REM 节点监听事件，自动生成交易，发送到链 B。**
    
4.  **Destination Contract（链 B）接收 REM 交易，执行回调逻辑。**
    
5.  **合约写法与普通合约相同，Reactive Contract 不需要账户。**
    
6.  **REM 节点代替合约签名交易，实现 自动执行。**
    
7.  **跨链回调原理：REM 节点作为桥梁，映射事件到目标链。**
    
8.  **REM 网络多个节点协作，保证触发可靠性。**
    
9.  **核心流程：事件发生 → REM 监听 → 自动交易 → 回调执行。**
    
10.  **类比理解：普通 EVM 被动执行，REM 主动监听并操作，实现事件驱动合约。**
     
11.  **Reactive的大概框架：**
     
     1.  ┌───────────────────────────┐
         
         │        用户/前端          │
         
         │  调用 Origin Contract API │
         
         └───────────┬───────────────┘
         
                     │ 交易请求
         
                     ▼
         
         ┌───────────────────────────┐
         
         │     链 A: Origin Contract │
         
         │ - 业务逻辑                │
         
         │ - emit Event              │
         
         └───────────┬───────────────┘
         
                     │ 事件日志
         
                     ▼
         
         ┌───────────────────────────┐
         
         │       REM 节点网络         │
         
         │ - 监听 Origin Contract Event
         
         │ - 解析事件触发条件
         
         │ - 生成交易调用 Destination Contract
         
         │ - 分布式协作，保证可靠触发
         
         └───────────┬───────────────┘
         
                     │ 交易
         
                     ▼
         
         ┌───────────────────────────┐
         
         │      链 B: Destination Contract │
         
         │ - 接收 REM 交易
         
         │ - 执行回调逻辑
         
         │ - 更新链上状态
         
         └───────────────────────────┘
         

# 学习感悟：

今天的学习让我对 **Reactive Network 和事件驱动合约**有了更直观的理解。之前我只知道 EVM 合约需要手动发交易才能执行，但今天通过跨链事件和 REM 节点的实验，我真正体会到 **事件触发、自动交易、跨链回调**的完整流程。我发现，Reactive Contract 本身不需要账户，所有自动执行和跨链回调都是 REM 节点代为完成的，这让我明白了“链上自动化”的本质：**合约逻辑被事件驱动，节点来完成签名和执行，完全解放了人工操作**。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
