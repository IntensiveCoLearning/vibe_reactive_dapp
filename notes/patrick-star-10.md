---
timezone: UTC+8
---

# SSS

**GitHub ID:** patrick-star-10

**Telegram:**

## Self-introduction

learning

## Notes

<!-- Content_START -->
# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->
# **_今天完成了第二个挑战：_**

[**https://github.com/patrick-star-10/reactive-network-Hackathon/tree/main/uniswap-v2-stop-order-sepolia-demo**](https://github.com/patrick-star-10/reactive-network-Hackathon/tree/main/uniswap-v2-stop-order-sepolia-demo)
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->

# **今日学习笔记**

## **主题：理解跨链和自动化机制**

### **一、核心概念**

**跨链**

让一条区块链能够知道另一条链发生的事情。

**自动化**

当某个条件成立时，系统自动执行操作，而不需要人工参与。

两者结合就是：

> **当A链发生某事件后，系统自动在B链执行操作。**

* * *

# **二、跨链的本质**

区块链之间是独立的账本系统：

-   Ethereum
    
-   BSC
    
-   Base
    
-   Solana
    

它们默认 **互相无法读取数据**。

因此需要一种机制：

**把一条链上的事件可信地传递到另一条链。**

跨链本质上是：

> **在不同链之间传递可信信息。**

* * *

# **三、跨链自动化基本流程**

一个完整流程可以概括为：

1.**源链发生事件**

例如：

```
emit Deposited(user, amount)
```

* * *

2\. **系统监听事件**

监听节点检测到该事件。

* * *

3\. **验证事件**

确认：

-   事件来源
    
-   区块确认数
    
-   参数正确
    

* * *

4.**目标链执行操作**

例如：

-   mint token
    
-   解锁资产
    
-   执行 callback
    

* * *

# **四、自动化机制逻辑**

自动化系统基本公式：

**Condition → Detection → Action**

即：

-   条件成立
    
-   系统检测
    
-   自动执行
    

例如：

-   价格低于某值 → 自动卖出
    
-   某事件触发 → 自动执行函数
    

* * *

# **五、跨链自动化核心公式**

整个系统可以总结为：

**Source Event → Observe → Verify → Execute**

即：

**事件发生 → 被监听 → 被验证 → 自动执行**

* * *

# **六、主要风险**

跨链自动化常见问题：

1.**信任问题**

目标链为什么相信源链事件。

2\. **延迟问题**

跨链执行存在时间差。

3\. **区块重组（reorg）**

已监听事件可能被回滚。

4\. **重放攻击**

同一消息被重复执行。

5\. **假事件攻击**

恶意合约伪造事件。

* * *

# **今日总结**

跨链解决的是：

**链与链之间的信息传递。**

自动化解决的是：

**条件满足后自动执行操作。**

两者结合后：

> **不同区块链可以基于事件自动联动。**
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->


# **今日学习总结：ReactVM 执行逻辑理解**

今天主要学习并理解了 **ReactVM 的执行逻辑以及 Reactive Contract 的运行流程**。ReactVM 是 Reactive Network 中专门用于执行响应式合约（Reactive Contracts）的运行环境，它的核心特点是 **事件驱动执行，而不是用户调用执行**。

* * *

## **一、ReactVM 的核心概念**

在传统的 EVM 合约中，合约函数通常是由用户主动发送交易触发执行，例如：

```
用户调用函数 → 合约执行
```

而在 Reactive Network 中，执行模式发生了改变：

```
链上事件发生 → Reactive Network 监听到日志 → ReactVM 执行响应式合约逻辑 → 触发 callback
```

因此 Reactive Contract 的核心思想是：

**不是等用户调用，而是对链上事件自动作出反应。**

ReactVM 就是专门运行这种 **事件驱动逻辑** 的执行环境。

* * *

## **二、ReactVM 的输入数据**

ReactVM 接收的输入不是普通函数参数，而是 **区块链日志（Log）数据**。

当某个合约执行 emit event 时，EVM 会生成一条日志，主要包含：

-   topic0：事件签名哈希
    
-   topic1/topic2：indexed 参数
    
-   data：非 indexed 参数
    
-   contract：事件来源合约
    
-   chainId：来源链
    
-   block / tx 信息
    

Reactive Network 的节点会监听这些日志，当匹配到订阅规则时，就会把日志传入 ReactVM 执行。

* * *

## **三、Reactive Contract 的执行流程**

Reactive Contract 的执行大致分为以下几个步骤：

### **1 订阅事件（Subscribe）**

响应式合约首先声明要监听的事件，例如：

-   来源链
    
-   来源合约
    
-   事件签名 topic0
    
-   其他 topic 过滤条件
    

这一步的作用是告诉系统：

**哪些事件发生时需要触发合约执行。**

* * *

### **2 监听事件（Trigger）**

当源链合约执行 emit event 时，会生成日志。

Reactive Network 节点会实时监听这些日志，并检查是否符合订阅条件。

如果匹配成功，就会触发 Reactive Contract 执行。

* * *

### **3 ReactVM 执行 react()**

ReactVM 会调用响应式合约中的 react() 函数，并把日志信息作为输入参数传入。

在 react() 中通常需要做以下事情：

-   解析 topic 和 data
    
-   使用 abi.decode 解包事件参数
    
-   判断逻辑条件是否满足
    
-   决定是否触发 callback
    

* * *

### **4 构造 callback payload**

如果条件成立，Reactive Contract 会构造回调数据，例如：

```
bytes memory payload = abi.encodeWithSignature(
    "callback(address,uint256,address,address,uint256,uint256)",
    ...
);
```

这里实际上是：

**把目标函数选择器 + 参数打包成可执行数据。**

* * *

### **5 发送 callback**

Reactive Network 会根据 payload 向目标链的目标合约发送 callback 交易，从而执行目标函数。

最终业务逻辑通常是在 **目标链合约中完成**。

* * *

## **四、ReactVM 的角色**

ReactVM 的角色更像一个 **自动化决策层**：

1.  接收链上日志
    
2.  解析事件数据
    
3.  判断是否满足执行条件
    
4.  决定是否发起 callback
    

真正的业务执行通常发生在 **callback 目标链合约** 中。

* * *

## **五、Reactive 模型的核心结构**

Reactive Network 的自动化逻辑可以总结为三个步骤：

```
Subscribe → Trigger → Callback
```

具体流程是：

```
源链事件发生
      ↓
Reactive Network 监听日志
      ↓
ReactVM 执行 Reactive Contract
      ↓
解析事件数据并判断条件
      ↓
构造 callback payload
      ↓
向目标链发送 callback
```

* * *

## **六、今天理解到的关键思想**

通过学习 ReactVM，我理解到 Reactive Network 的设计目标是：

**把传统依赖 bot 或服务器的自动化执行逻辑，变成链上的原生事件驱动执行。**

这意味着：

-   自动化逻辑不依赖中心化服务器
    
-   执行逻辑由合约控制
    
-   整个系统更加去中心化
    

ReactVM 本质上就是：

**一个专门处理链上事件并触发自动化执行的执行环境。**
<!-- DAILY_CHECKIN_2026-03-13_END -->

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
