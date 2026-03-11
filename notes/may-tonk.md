---
timezone: UTC+8
---

# Justin Li

**GitHub ID:** may-tonk

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
# 使用AI演示和分析了reactive contract的工作原理，图片和简单动画演示

[Reactive Contract 完全指南](https://may-tonk.github.io/html_may_tonk_web/reactive_contract_image.html/second_reactiveframe.html)

* * *

# **Reactive Contract 与自动清算学习总结**

## 1️⃣ 自动清算的基础概念

-   **健康因子（Health Factor, HF）**：  
    衡量借款人抵押品是否足够覆盖债务。
    
    -   HF > 1：安全
        
    -   HF < 1：触发清算风险
        
-   **清算触发原因**：  
    当抵押资产价值下跌导致 HF < 1 时，为防止坏账，系统需要进行清算。
    
-   **清算者（Liquidator）**：
    
    -   替系统偿还借款人部分债务
        
    -   获得折价的抵押物或奖励
        
    -   系统不直接平仓全部，是为了激励第三方参与清算
        
-   **收益机制**：
    
    -   每笔清算奖励通常是债务的一部分，而不是固定百分比（可能在 1%~10% 范围，根据平台设定不同）
        
    -   实际收益受抵押资产价格、滑点、Gas 成本、Flash Loan 利息和 MEV 竞争影响
        

* * *

## 2️⃣ Reactive Contract 在自动清算的作用

-   **核心特性**：事件驱动（Event-Driven Smart Contract）
    
    -   当链上某个事件发生（例如 HF < 1）
        
    -   自动触发合约执行，无需手动监听或 off-chain bot
        
-   **优势**：
    
    1.  **自动化架构**：减少服务器、RPC监听等运维成本
        
    2.  **跨链自动化**：结合 Hyperlane 可以实现跨链清算或套利
        
    3.  **复杂逻辑触发**：支持多条件事件触发，如价格 + 时间 + 特定交易
        
-   **限制**：
    
    -   延迟受区块时间限制（例如 7 秒左右）
        
    -   并非竞争清算速度的核心优势，MEV/mempool 机器人通常更快
        

* * *

## 3️⃣ Reactive Contract 延迟解析

-   **“7 秒延迟”**：通常指整个系统框架响应事件到交易上链的延迟
    
-   **不是累加**：即使有多个合约链式触发，也不会 7+7+7 秒，而是整个事件链大约一个区块延迟
    
-   **现实影响**：
    
    -   对清算收益：如果被 MEV bot 或高 Gas 机器人抢先，收益可能丢失
        
    -   对 Demo/跨链策略：7 秒延迟通常可接受
        

* * *

## 4️⃣ 与传统清算机器人的对比

| 特性 | Reactive Contract | Mempool / MEV 机器人 |
| --- | --- | --- |
| 执行方式 | 链上事件触发 | 监听交易池 / 提前预测 |
| 延迟 | ≈1 block（7秒左右） | <1 block（更快，抢先执行） |
| 自动化 | 高 | 高，但需维护 off-chain bot |
| 跨链能力 | 可结合 Hyperlane 实现 | 复杂，需要额外桥或协议支持 |
| 收益能力 | 中（适合自动化和 Demo） | 高（真正抢先执行清算赚钱） |
| 技术门槛 | 中 | 高（MEV / Flashbots 知识） |

> 结论：
> 
> -   **Reactive Contract** 优势在于架构自动化、跨链和可展示性
>     
> -   **MEV / mempool 机器人**优势在于清算速度和收益最大化
>     

* * *

## 5️⃣ 自动清算操作风险与成本

1.  **滑点风险**：在 DEX 卖出抵押资产可能低于预期
    
2.  **流动性限制**：大额清算可能自拉价格下跌
    
3.  **Flash Loan 成本**：借款利息 + 手续费
    
4.  **Gas 费用**：网络拥堵时需提高 Gas 才能入块
    
5.  **MEV 竞争**：其他机器人可能抢先，导致收益丢失
    

**重要结论**：

> HF<1 不等于可盈利清算，必须计算成本、滑点、Gas 和竞争情况。

* * *

## 6️⃣ Reactive Contract 清算的实践场景

-   **跨链清算机器人**
    
    -   Chain A 触发 HF<1
        
    -   Reactive Contract 自动触发
        
    -   Hyperlane 发送消息
        
    -   Chain B 执行清算交易
        
-   **自动套利与收益管理**
    
    -   事件驱动套利策略（价格差 + 交易触发）
        
    -   自动再平衡抵押仓位
        
    -   可做 Demo 或 Hackathon 展示项目
        
-   **教育与研发优势**
    
    -   可以直观演示链上事件驱动逻辑
        
    -   展示跨链自动化能力
        

# **Reactive Contract 学习总结（补充）**

## 1️⃣ Reactive Contract 适合的运用场景

1.  **自动化策略执行**
    
    -   价格触发交易（Price Triggered Trades）
        
    -   自动再平衡抵押品或仓位
        
    -   收益策略自动执行（Yield Optimization）
        
2.  **自动清算（Liquidation）**
    
    -   HF < 1 时自动触发清算交易
        
    -   可结合 Flash Loan，替系统回收债务
        
    -   适合 Demo 或测试网实验
        
3.  **跨链操作**
    
    -   利用 Hyperlane 等跨链消息协议
        
    -   在链 A 触发事件 → 链 B 自动执行交易
        
    -   跨链套利、跨链清算、跨链资产管理
        
4.  **复杂条件触发逻辑**
    
    -   多条件事件：价格 + 时间 + 特定交易
        
    -   自动执行多步骤策略
        
    -   无需人工干预，事件一旦满足即触发
        
5.  **Web3 项目展示与 Hackathon**
    
    -   事件驱动逻辑直观可展示
        
    -   自动化执行 + 跨链操作
        
    -   展示技术亮点和创新性
        

* * *

## 2️⃣ Reactive Contract 的核心优势

| 优势 | 说明 |
| --- | --- |
| 事件驱动自动化 | 链上事件触发，无需 off-chain bot，减少运维成本 |
| 跨链兼容性 | 可结合 Hyperlane，实现跨链触发和执行 |
| 复杂逻辑支持 | 支持多条件、多步骤事件触发 |
| Demo 和教学优势 | 易于展示事件驱动自动化，适合 Hackathon 或实验项目 |
| 稳定执行 | 不依赖外部节点或 RPC，链上触发可靠 |
| 灵活策略 | 可做清算、套利、收益管理等多种策略组合 |

* * *

## 3️⃣ 与传统清算机器人对比

| 特性 | Reactive Contract | MEV / Mempool 机器人 |
| --- | --- | --- |
| 执行方式 | 链上事件触发 | 监听交易池 / 提前预测 |
| 延迟 | ≈1 block（7秒左右） | <1 block（抢先执行） |
| 自动化 | 高，无需维护服务器 | 高，但需维护 off-chain bot |
| 跨链能力 | 强，结合 Hyperlane | 复杂，需要桥或协议支持 |
| 收益能力 | 中（适合策略和 Demo） | 高（抢先清算获取奖励） |
| 技术门槛 | 中 | 高（MEV / Flashbots 知识） |

> 总结：
> 
> -   **Reactive Contract 优势**：自动化、跨链、复杂逻辑、Demo/教学展示
>     
> -   **MEV bot 优势**：清算速度、收益最大化
>     

* * *

## 4️⃣ 现实注意事项

-   延迟是链上一个区块级别（7秒左右），不会叠加
    
-   HF<1 ≠ 盈利机会，需考虑滑点、流动性、Gas、Flash Loan、MEV竞争
    
-   Reactive Contract 更适合自动化和策略展示，不一定在激烈清算竞争中获利最大
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

# _采用AI做了两关于reactive contract的理解和一些问题的分析_

(完整网站明天补上）

# Reactive Contract三个合约大白话解释

## 三个合约，三个角色

```
L1Contract    =  门铃
L1Callback    =  房门
ReactiveContract =  门卫
```

* * *

## 完整过程

**① 你按门铃（发送 ETH）**

```
你 → 发 0.001 ETH → L1Contract
```

L1Contract 什么都不用做，就是响一声"叮咚"（emit 事件）

* * *

**② 门卫听到了（Reactive 监听到）**

```
ReactiveContract 在 Lasna 上 24小时值班
听到叮咚声 → 判断：金额够不够？
够 → 去开门
不够 → 不管
```

* * *

**③ 门卫去开门（触发回调）**

```
门卫用自己的钥匙（Reactive VM 地址）
去 L1Callback 那里执行操作
```

* * *

## **Reactive VM 地址是什么？**

就是**门卫的工牌**。

L1Callback 开门之前要检查：

```
"你是谁？"
→ 出示工牌（Reactive VM 地址）
→ 工牌对了才开门
→ 工牌不对直接拒绝
```

这就是为什么你部署 L1Callback 时要填 `CALLBACK_SENDER`，就是**提前登记门卫的工牌号码**，只认这一个。

* * *

## 部署顺序为什么是这样

```
先装门铃（L1Contract）  → 得到门铃地址
先装房门（L1Callback）  → 得到房门地址
最后雇门卫（ReactiveContract）→ 把两个地址都告诉门卫
```

门卫需要知道：

-   监听哪个门铃 → L1Contract 地址
    
-   去敲哪扇门 → L1Callback 地址
    

**所以必须先有门铃和房门，才能告诉门卫去哪。**

# 一、Reactive Contract 的三部分结构

根据你之前提供的示例，**Reactive Contract 的框架通常分为三部分**：

1.  **Callback 合约（BasicDemoL1Callback.sol）**
    
    -   作用：在目标链上接收回调事件。
        
    -   特点：继承 `AbstractCallback`，主要功能是 **触发事件** `CallbackReceived`。
        
    -   核心点：使用 `authorizedSenderOnly`、`rvmIdOnly(sender)` 修饰符确保安全调用。
        
    -   可以自由发挥的部分：
        
        -   在 `callback()` 中增加业务逻辑，例如 **记录状态、调用其他合约、发放奖励**。
            
    -   Solidity 关注点：
        
        -   修饰符和事件触发安全性。
            
        -   避免直接写入敏感信息。
            
2.  **基础目标合约（BasicDemoL1Contract.sol）**
    
    -   作用：模拟普通合约的事件触发，如接收 ETH、产生日志。
        
    -   核心点：
        
        -   `receive()` 或 `fallback()` 用来接收 ETH。
            
        -   使用 `call` 安全转发 ETH。
            
    -   可以自由发挥的部分：
        
        -   增加业务逻辑，例如 **余额统计、条件触发事件**。
            
        -   可以触发自定义事件，供 Reactive Contract 响应。
            
    -   Solidity 关注点：
        
        -   安全转账（`call` 而不是 `transfer`）。
            
        -   事件触发逻辑清晰，便于 reactive 合约订阅。
            
3.  **Reactive 合约核心（BasicDemoReactiveContract.sol）**
    
    -   作用：接收源合约事件日志（LogRecord），做条件判断后调用 Callback。
        
    -   核心点：
        
        -   继承 `AbstractReactive` 并实现 `IReactive` 接口。
            
        -   `react(LogRecord calldata log)` 是 **事件驱动的入口**。
            
        -   使用 `service.subscribe()` 订阅源链事件。
            
        -   条件判断，例如 `if (log.topic_3 >= 0.001 ether)` 决定是否触发 callback。
            
    -   可以自由发挥的部分：
        
        -   `react()` 内的业务逻辑完全可以自由扩展：
            
            -   条件判断更复杂，例如多事件、多条件、多阈值。
                
            -   调用其他合约或执行复杂计算。
                
    -   Solidity 关注点：
        
        -   避免在 `react()` 中消耗过多 Gas。
            
        -   安全调用外部 callback，防止重入或无限循环。
            
        -   注意事件订阅 `service.subscribe()` 的参数匹配正确。
            

* * *

# 二、Reactive Contract 与普通合约的区别

| 维度 | 普通合约 | Reactive Contract |
| --- | --- | --- |
| 调用方式 | 用户直接调用函数或交易 | 事件驱动自动调用 react() 或 callback() |
| 事件触发 | 可选，主要是日志记录 | 核心，Reactive Contract 是基于事件自动响应的 |
| 业务逻辑 | 完全由函数决定 | 逻辑分散在三个合约，通过事件/订阅触发 |
| 继承库 | 可选 | 必须继承 AbstractReactive / AbstractCallback 并实现 IReactive / 接口 |
| 安全关注点 | 常规 Solidity 安全 | 回调安全、Gas 限制、事件订阅、链间交互安全 |
| 部署依赖 | 单链 | 可能涉及跨链事件订阅（Reactive Network） |

> 所以在 Solidity 语法层面，Reactive Contract 还是 Solidity，只是 **逻辑模式从“函数调用驱动”变成“事件驱动”**，需要注意 **订阅事件、回调安全和 Gas 限制**。

* * *

# 三、开发 Reactive Contract 的重点（Solidity 语法和结构）

1.  **接口和抽象类继承**
    
    -   `IReactive`：保证 `react()` 有统一接口。
        
    -   `AbstractReactive` / `AbstractCallback`：提供安全调用修饰符和基础功能。
        
2.  **事件驱动逻辑**
    
    -   核心是 `react(LogRecord calldata log)` 和 `callback()`。
        
    -   Solidity 写法上：
        
        -   `calldata` 用于外部调用，节省 Gas。
            
        -   `emit` 触发事件，供下一步逻辑或链上索引器使用。
            
3.  **Gas 限制**
    
    -   事件驱动执行可能被外部触发，函数执行要 **轻量化**。
        
    -   `GAS_LIMIT` 常量用来限制调用 callback 消耗。
        
4.  **安全修饰符**
    
    -   `vmOnly`、`authorizedSenderOnly`、`rvmIdOnly(sender)` 等确保 **事件只能被订阅者/系统合约调用**。
        
    -   Solidity 注意点：
        
        -   不能去掉这些修饰符，否则 callback 可被任意地址调用。
            
5.  **跨合约调用安全**
    
    -   Callback 调用可能会触发外部合约：
        
        -   用 `call()` 代替 `transfer` 或 `send`。
            
        -   防止重入攻击。
            
6.  **事件订阅**
    
    -   `service.subscribe(originChainId, contract, topic, ...)`
        
    -   Solidity 语法上没区别，但逻辑上必须确保 **主题/topic 匹配源事件**。
        

* * *

# 四、自由发挥的空间

结合你的理解：

-   你 **可以在三个合约中增加业务逻辑**，实现你自己的功能：
    
    -   Callback 合约：处理回调结果，例如更新状态或发放代币。
        
    -   基础合约：触发事件，可以模拟任何业务行为。
        
    -   Reactive 合约：实现智能事件处理和条件判断。
        
-   **但必须遵守以下规则**：
    
    1.  **继承和接口不能随意改**，保证事件驱动机制可用。
        
    2.  **Gas 消耗不能太高**，尤其是 `react()`。
        
    3.  **安全修饰符不能去掉**，保证 callback 和 react 调用安全。
        

* * *

# 五、总结

1.  **Reactive Contract 是 Solidity，只是事件驱动模式**。
    
2.  **核心逻辑分三部分**：
    
    -   Callback（回调处理）
        
    -   基础合约（事件触发）
        
    -   Reactive（事件响应逻辑）
        
3.  **与普通合约的区别**：
    
    -   调用方式：事件触发而不是用户主动调用
        
    -   必须继承抽象类/接口
        
    -   更注重 **安全性和 Gas**
        
4.  **开发重点**：
    
    -   接口继承正确
        
    -   修饰符和事件安全
        
    -   react() 函数轻量高效
        
    -   正确订阅事件并匹配 topic
        
5.  **自由发挥**：
    
    -   可以在逻辑上做任何业务处理
        
    -   Callback、react 条件判断、基础合约事件逻辑都可以扩展
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->



# 一、Reactive Contract 是什么

**Reactive Contract** 是一种 **监听链上事件并自动触发跨链回调的智能合约机制**。

核心思想：

> 当某个链上事件发生 → Reactive Network 监听到 → 自动执行合约逻辑 → 调用另一个合约。

简单理解：

```
事件触发 → Reactive监听 → 自动执行 → 回调合约
```

类似 **Web2 的 webhook 自动触发机制**。

* * *

# 二、Reactive系统的三个核心角色

代码里有 **3个合约**：

| 合约 | 作用 | 所在链 |
| --- | --- | --- |
| BasicDemoL1Contract | 产生事件 | L1 |
| BasicDemoReactiveContract | 监听事件并触发回调 | Reactive Network |
| BasicDemoL1Callback | 接收Reactive回调 | L1 |

架构：

```
用户
 │
 ▼
BasicDemoL1Contract
 │
 │ emit Received event
 ▼
Reactive Network
 │
 ▼
BasicDemoReactiveContract
 │
 │ emit Callback
 ▼
Reactive VM
 │
 ▼
BasicDemoL1Callback
```

* * *

# 三、三个合约的核心逻辑

## 1 BasicDemoL1Contract（事件产生）

作用：

> 接收 ETH 并触发事件。

核心代码：

```
receive() external payable {
    emit Received(
        tx.origin,
        msg.sender,
        msg.value
    );
}
```

当有人发送 ETH 时：

```
发送ETH → receive()执行 → 触发 Received 事件
```

Reactive Network 就会监听这个事件。

* * *

## 2 BasicDemoReactiveContract（监听 + 反应）

这是 **Reactive 合约核心**。

作用：

1.  订阅某个事件
    
2.  当事件发生时执行 `react()`
    
3.  触发 callback
    

核心函数：

```
function react(LogRecord calldata log) external vmOnly
```

Reactive VM 会自动调用这个函数。

判断条件：

```
if (log.topic_3 >= 0.001 ether)
```

意思是：

```
如果收到ETH >= 0.001
就触发callback
```

触发回调：

```
emit Callback(destinationChainId, callback, GAS_LIMIT, payload);
```

Reactive VM看到这个事件后会执行回调。

* * *

## 3 BasicDemoL1Callback（回调执行）

作用：

> 接收Reactive Network的调用。

函数：

```
function callback(address sender)
```

Reactive VM会调用它。

然后触发事件：

```
emit CallbackReceived(
    tx.origin,
    msg.sender,
    sender
);
```

说明：

```
Reactive回调执行成功
```

* * *

# 四、Reactive Contract 工作流程

完整执行流程：

```
1 用户给 L1Contract 发送ETH
        │
        ▼
2 L1Contract触发 Received 事件
        │
        ▼
3 Reactive Network监听日志
        │
        ▼
4 调用 ReactiveContract.react()
        │
        ▼
5 判断条件 (ETH >= 0.001)
        │
        ▼
6 emit Callback
        │
        ▼
7 Reactive VM执行回调
        │
        ▼
8 调用 L1Callback.callback()
        │
        ▼
9 触发 CallbackReceived 事件
```

* * *

# 五、Reactive Contract 关键技术点

学习 Reactive 需要理解 **5个核心概念**。

* * *

## 1 Event Topic

事件：

```
event Received(address indexed origin, address indexed sender, uint256 indexed value);
```

事件会生成：

```
topic0 = 事件签名
topic1 = origin
topic2 = sender
topic3 = value
```

Reactive 监听：

```
service.subscribe(
 originChainId,
 contract,
 topic0,
 ...
);
```

* * *

## 2 Event Subscription（事件订阅）

Reactive Contract 在部署时订阅事件：

```
service.subscribe(...)
```

含义：

```
监听某个链
监听某个合约
监听某个事件
```

* * *

## 3 react() 函数

Reactive Contract 的核心入口：

```
function react(LogRecord calldata log)
```

Reactive VM监听到事件就会调用这个函数。

* * *

## 4 Callback 机制

Reactive Contract 触发：

```
emit Callback(...)
```

Reactive VM就会执行：

```
callback contract
```

* * *

## 5 Reactive VM

Reactive VM 是系统执行器：

负责：

```
监听事件
调用 react()
执行 callback
```
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
