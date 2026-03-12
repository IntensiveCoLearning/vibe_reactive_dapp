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
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
# **今日学习笔记**

## **理解 Subscribe / Trigger / Callback 模型（Reactive 思维）**

今天重点理解了 **Subscribe / Trigger / Callback** 这一套 **事件驱动模型（Event-Driven Model）**，它是 Reactive Contract 的核心逻辑，也是很多自动化系统的基础设计模式。

简单来说，这个模型解决的问题是：

> **系统不需要持续轮询检查，而是在事件发生时自动执行预设逻辑。**

整个流程可以概括为：

```
Subscribe → Trigger → Callback
订阅监听 → 事件触发 → 执行回调
```

* * *

# **一、Subscribe（订阅）**

**Subscribe 的作用是提前定义监听规则。**

也就是说，系统需要先知道：

-   要监听哪条链
    
-   要监听哪个合约
    
-   要监听哪个事件
    
-   是否需要过滤某些参数（topic）
    

本质上就是告诉系统：

> **当某种事件发生时，请通知我或让我执行响应逻辑。**

例如：

监听某个事件：

```
ValueSet(address,uint256,uint256)
```

在区块链中，每个事件都会对应一个 **事件签名哈希（topic0）**。

例如：

```
keccak256("ValueSet(address,uint256,uint256)")
```

Reactive 系统通常就是通过 **topic0** 来识别应该监听的事件类型。

所以 Subscribe 的本质是：

> **建立事件监听规则**

可以理解为：

```
提前埋伏
```

* * *

# **二、Trigger（触发）**

Trigger 是指：

> **当订阅的事件真的发生时，系统检测到并触发响应流程。**

例如源链上的某个合约执行：

```
emit ValueSet(msg.sender, oldValue, newValue);
```

当这个事件被写入区块链日志（log）后：

Reactive 网络会检测：

-   是否匹配订阅链
    
-   是否匹配合约地址
    
-   是否匹配 topic0
    
-   是否符合过滤条件
    

如果全部匹配，就会进入下一步：

```
Trigger
```

也就是：

> **订阅条件被满足，系统开始执行响应逻辑。**

注意：

Trigger **不是用户手动调用**，

而是 **事件本身触发系统响应**。

* * *

# **三、Callback（回调）**

当 Trigger 发生后，Reactive 合约会执行预先定义的逻辑。

例如：

-   判断参数
    
-   判断是否满足某个条件
    
-   决定调用哪个目标合约
    

最后会执行：

```
callback
```

也就是调用目标合约执行具体动作，例如：

-   自动转账
    
-   自动清算
    
-   自动交易
    
-   自动更新状态
    
-   自动执行 DAO 操作
    

因此 Callback 的本质是：

> **事件发生后的自动执行动作。**

* * *

# **四、完整流程**

整个模型的完整执行流程如下：

```
源合约 emit event
        ↓
Reactive 网络监听事件
        ↓
匹配 Subscribe 规则
        ↓
Trigger 响应逻辑
        ↓
执行 Callback 调用目标合约
```

换成三个关键词就是：

```
Subscribe = 注册监听规则
Trigger   = 事件匹配规则
Callback  = 执行自动动作
```

* * *

# **五、三种角色**

在 Reactive 架构中通常有三个角色：

### **1\. Source Contract（源合约）**

负责产生事件，例如：

```
emit Deposit(user, amount);
```

作用：

```
发送信号
```

* * *

### **2.Reactive Contract**

负责：

-   监听事件
    
-   判断条件
    
-   决定执行逻辑
    

作用：

```
逻辑中枢
```

* * *

### **3\. Target Contract**

负责真正执行最终操作，例如：

-   转账
    
-   更新状态
    
-   自动清算
    
-   自动交易
    

作用：

```
执行动作
```

* * *

# **六、与传统智能合约的区别**

传统智能合约模式：

```
用户调用函数
      ↓
函数执行
```

Reactive 模式：

```
事件发生
      ↓
系统自动检测
      ↓
自动执行逻辑
```

也就是说：

传统模式是：

```
Request → Response
```

Reactive 模式是：

```
Event → Response
```

* * *

# **七、这种模型的价值**

Subscribe / Trigger / Callback 的价值在于：

### **1 自动化执行**

无需人工手动触发。

* * *

### **2 事件驱动**

不需要持续轮询检查状态。

* * *

### **3 更适合链上系统组合**

不同合约之间可以通过事件形成自动化协作。

* * *

### **4 可以构建复杂链上自动系统**

例如：

-   自动止盈止损
    
-   自动清算
    
-   自动保险赔付
    
-   自动 DAO 执行
    
-   自动资金归集
    
-   自动跨链操作
    

* * *

# **八、一个简单例子**

假设做一个 **自动止盈系统**。

### **Subscribe**

监听某个预言机价格更新事件。

* * *

### **Trigger**

当价格更新事件发生，并达到止盈价格。

* * *

### **Callback**

自动执行交易合约：

```
swap / sell
```

* * *

# **九、核心理解**

Subscribe / Trigger / Callback 本质上是：

> **条件驱动执行模型**

可以用一个比喻理解：

```
Subscribe = 埋伏
Trigger   = 敌人出现
Callback  = 执行动作
```

也可以理解为：

```
先写下意图
条件满足
系统自动执行
```

这也是很多 **链上自动化系统、意图系统、Reactive DAO** 的核心思想。

* * *

# **今日总结**

1.理解了 **Reactive 系统的事件驱动逻辑**

2\. 搞清楚 **Subscribe / Trigger / Callback 的完整执行流程**

3.理解了 **事件 topic0 在订阅中的作用**

4.明白了 Reactive 合约与传统智能合约的区别

最核心的一句话：

> **Reactive 模型让区块链从“被动执行工具”变成“条件驱动自动系统”。**
<!-- DAILY_CHECKIN_2026-03-12_END -->

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
