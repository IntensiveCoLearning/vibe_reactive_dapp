---
timezone: UTC+8
---

# yangyang-hub

**GitHub ID:** yangyang-hub

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-22
<!-- DAILY_CHECKIN_2026-03-22_START -->
# Reactscan 学习笔记

## 📌 工具作用

👉 Reactscan = Reactive Network 的区块浏览器  
用于查看：

-   RVM 地址
    
-   合约
    
-   交易（含跨链 & 回调）
    

* * *

## 🧭 核心入口

-   Mainnet / Testnet 浏览器
    
-   🔍 搜索栏：直接查地址 / 交易
    
-   📍 RVM 页面：开发者核心视图
    

* * *

## 🧱 RVM（重点）

### 📍 什么是 RVM

-   你的“执行环境地址”
    
-   \= 你部署合约 & 发起 reactive 交易的地址
    

* * *

### 🔎 如何找到 RVM

1.  首页 → Latest RVMs
    
2.  或 View All RVMs
    
3.  或直接搜索地址
    
4.  或 URL：
    

```
https://reactscan.net/rvm/<ADDRESS>
```

* * *

### ⭐ 小技巧

-   点击 `[watch]` 👉 固定在首页顶部
    
-   避免反复搜索
    

* * *

## 📊 RVM 页面内容

### 1️⃣ 基本信息

-   REACT 余额
    
-   合约列表
    
-   交易记录
    

* * *

### 2️⃣ 交易表（核心）

字段说明：

| 字段 | 含义 |
| --- | --- |
| Type | DEPLOY / REACT |
| Callbacks | 回调次数 |
| Interacted With | 触发交易的合约 |

👉 REACT = 响应交易  
👉 DEPLOY = 部署合约

* * *

## 📦 Contract 页面

### 功能

-   查看某个合约所有交易
    
-   查看订阅（Subscriptions）
    

* * *

### 状态

-   Active 👉 正常运行
    
-   Inactive 👉 需补债（coverDebt）
    

* * *

### 📡 Subscriptions

字段：

-   Status（Active / Inactive）
    
-   Chain
    
-   Topics（事件过滤）
    

👉 本质：监听哪些链上事件

* * *

## 🔁 RVM Transaction（详细页）

点击交易进入：

### 📊 核心信息

-   From（EOA）
    
-   Interacted With
    
-   Type / Status
    

* * *

### ⛽ Gas 信息

-   Gas Used
    
-   Gas Price
    

* * *

### 🔗 跨链信息（重要）

如果是 reactive 交易：

-   Origin Transaction 👉 来源链
    
-   Destination Transaction 👉 目标链
    

* * *

### 📦 Payload（事件数据）

包含：

-   Contract
    
-   Topics（0~3）
    
-   Data
    
-   Block Number
    
-   OpCode
    

* * *

### 🧾 Logs

可能包含：

-   事件日志
    
-   回调信息（Callback）
    

* * *

## 🌐 RNK Transactions（全网交易）

首页右下角：

显示：

-   hash
    
-   status
    
-   contract
    
-   时间
    

👉 可点击查看详情

* * *

## 🔍 普通交易详情（RNK）

包含：

### 基本信息

-   From / To
    
-   Value
    
-   Block Number
    

* * *

### ⛽ Gas

-   Gas Used
    
-   Gas Price
    

* * *

### 📜 Logs

-   Event Signature
    
-   Topics
    
-   Data
    

* * *

## 🧠 核心理解总结

### 🔑 1. RVM 是核心

👉 所有 reactive 行为都围绕 RVM

* * *

### 🔑 2. 两类交易

-   DEPLOY 👉 部署
    
-   REACT 👉 响应执行（关键）
    

* * *

### 🔑 3. Subscription 是触发源

👉 决定你监听什么事件

* * *

### 🔑 4. Callback 是执行结果

👉 REACT → 触发 callback

* * *

### 🔑 5. 跨链是默认能力

👉 Origin ↔ Destination

* * *

## 🚀 一句话总结

👉 **Reactscan = 用来调试和观察“事件驱动合约执行（Reactive）”的浏览器**
<!-- DAILY_CHECKIN_2026-03-22_END -->

# 2026-03-21
<!-- DAILY_CHECKIN_2026-03-21_START -->

# 🧠 Reactive Library 学习笔记

## 📦 安装

```
forge install Reactive-Network/reactive-lib
```

* * *

## 🧩 核心架构

### 1️⃣ 基础合约层

✅ `AbstractReactive`

-   所有响应式合约的基类
    
-   继承 `AbstractPayer` + 实现 `IReactive`
    
-   核心能力：
    
    -   订阅事件（通过系统合约）
        
    -   处理事件（`react()`）
        
    -   自动检测运行环境：
        
        -   `vmOnly`（ReactVM）
            
        -   `rnOnly`（Reactive Network）
            

👉 关键点：

-   自动绑定系统合约（支付 + 订阅）
    
-   系统合约默认被授权支付
    

* * *

### 2️⃣ 支付模块

💰 `AbstractPayer`

提供资金与债务管理：

核心功能：

-   `pay(amount)` 👉 向授权调用者转账
    
-   `coverDebt()` 👉 清偿 vendor 债务
    
-   `receive()` 👉 支持直接转账
    

权限控制：

```
authorizedSenderOnly
```

👉 只有授权地址可以调用支付

* * *

### 3️⃣ 回调模块

🔁 `AbstractCallback`

-   继承 `AbstractPayer`
    
-   用于处理回调执行权限
    

核心机制：

-   `rvm_id` 👉 限制调用来源（ReactVM）
    
-   `vendor` 👉 回调代理
    

关键修饰符：

```
rvmIdOnly
```

👉 只允许指定 VM 调用

* * *

### 4️⃣ 可暂停订阅

⏸️ `AbstractPausableReactive`

-   支持订阅暂停/恢复
    

核心函数：

-   `pause()` 👉 取消订阅
    
-   `resume()` 👉 恢复订阅
    

特点：

-   仅 `owner` 可操作
    
-   依赖：
    
    ```
    getPausableSubscriptions()
    ```
    

* * *

## 🔌 接口层

### 📡 `IReactive`

-   核心入口
    

```
function react(LogRecord calldata log)
```

-   事件：
    

```
Callback(...)
```

* * *

### 💸 `IPayable`

-   `receive()` 收款
    
-   `debt()` 查询债务
    

* * *

### 💳 `IPayer`

-   `pay()` 发起支付
    

* * *

### 📬 `ISubscriptionService`

-   `subscribe()` 订阅事件
    
-   `unsubscribe()` 取消订阅
    

* * *

### 🧱 `ISystemContract`

\= `IPayable + ISubscriptionService`

👉 系统合约统一入口

* * *

## ⚙️ 系统组成（重要）

### 🏛️ 1. 系统合约

负责：

-   支付处理
    
-   订阅管理
    
-   权限控制
    
-   定时事件（CRON）
    

* * *

### 🔁 2. 回调代理（Callback Proxy）

负责：

-   执行回调交易
    
-   管理押金/债务
    
-   gas 计算
    
-   限制调用权限
    

* * *

### 📡 3. 订阅服务

-   管理所有事件订阅
    
-   支持：
    
    -   链 ID
        
    -   合约地址
        
    -   topic 过滤
        
    -   通配符匹配
        

* * *

## ⏱️ CRON 机制（重点）

👉 用区块驱动的“定时任务系统”

| 事件 | 间隔 | 时间 | 用途 |
| --- | --- | --- | --- |
| Cron1 | 每块 | ~7秒 | 高频 |
| Cron10 | 10块 | ~1分钟 | 常用 |
| Cron100 | 100块 | ~12分钟 |   |
| Cron1000 | 1000块 | ~2小时 |   |
| Cron10000 | 10000块 | ~28小时 | 低频 |

👉 特点：

-   无需外部 keeper / cron
    
-   通过订阅事件实现定时执行
    

* * *

## 🧠 核心设计思想总结

### 🔑 1. 事件驱动（Reactive）

-   不轮询，只响应事件
    

### 🔑 2. 合约自动化（Cron + Subscription）

-   链上定时执行
    

### 🔑 3. 内置支付系统

-   自动结算 gas / 债务
    

### 🔑 4. 权限严格控制

-   VM 限制
    
-   授权 sender
    
-   owner 控制
    

* * *

## 🚀 一句话理解

👉 **Reactive Library = “链上事件订阅 + 自动执行 + 内置支付”的智能合约框架**
<!-- DAILY_CHECKIN_2026-03-21_END -->

# 2026-03-20
<!-- DAILY_CHECKIN_2026-03-20_START -->



## Reactive Network RNK 扩展 RPC 学习笔记

### 1\. 基本定位

-   Reactive Network 支持标准 **Geth RPC**。
    
-   这页文档介绍的是 **RNK 自定义扩展方法**。
    
-   主要用途：
    
    -   查询 **ReactVM 交易**
        
    -   查询 **交易日志**
        
    -   查询 **合约代码 / 存储**
        
    -   执行 **只读调用**
        
    -   查询 **RVM / 订阅 / 过滤器 / 链上统计信息**
        

* * *

## 2\. 交易查询类

### `rnk_getTransactionByHash`

按 **RVM ID + 交易哈希** 查询 ReactVM 交易。

### `rnk_getTransactionByNumber`

按 **RVM ID + 交易序号** 查询交易。

### `rnk_getTransactions`

批量查询某个 RVM 从指定序号开始的一段交易。

### `rnk_getHeadNumber`

查询某个 RVM 当前最新交易号。

### 常见返回字段

这几个接口返回的交易对象字段基本一致：

-   `hash`：交易哈希
    
-   `number`：交易序号（hex）
    
-   `time`：时间戳
    
-   `root`：Merkle root
    
-   `limit`：gas limit
    
-   `used`：gas used
    
-   `type`：交易类型
    
    -   `0` Legacy
        
    -   `1` AccessList
        
    -   `2` DynamicFee
        
    -   `3` Blob
        
    -   `4` SetCode
        
-   `status`：状态
    
    -   `1` 成功
        
    -   `0` 失败
        
-   `from` / `to`：发起方 / 接收方
    
-   `createContract`：是否创建合约
    
-   `sessionId`：所在 RNK 区块号
    
-   `refChainId`：来源链 ID
    
-   `refTx`：触发该反应交易的来源链交易
    
-   `refEventIndex`：来源事件 opcode
    
-   `data`：输入数据
    
-   `rData`：返回数据
    

* * *

## 3\. 日志查询

### `rnk_getTransactionLogs`

查询某个 ReactVM 交易产生的日志。

### 返回内容

-   `txHash`：交易哈希
    
-   `address`：产生日志的合约地址
    
-   `topics`：事件 topics
    
    -   `topics[0]` 通常是事件签名
        
-   `data`：非索引参数数据
    

* * *

## 4\. RVM / 地址映射

### `rnk_getRnkAddressMapping`

把 **Reactive Network 合约地址** 映射到对应的 **RVM ID**。

适合理解：

-   “这个 Reactive 合约属于哪个 RVM？”
    

* * *

## 5\. 网络统计 / RVM 信息

### `rnk_getStat`

查询按 **来源链** 聚合的统计数据。

返回：

-   `txCount`：该来源链触发的交易数
    
-   `eventCount`：该来源链事件数
    

### `rnk_getVms`

查询全部已知 ReactVM 列表。

### `rnk_getVm`

查询单个 ReactVM 信息。

### 常见字段

-   `rvmId`
    
-   `lastTxNumber`
    
-   `contracts`：该 RVM 下合约数量
    

* * *

## 6\. 订阅与过滤器

### `rnk_getSubscribers`

查询某个 RVM 的订阅信息。

返回字段：

-   `uid`：订阅唯一 ID
    
-   `chainId`：来源链 ID
    
-   `contract`：来源链上的被订阅合约
    
-   `topics`：订阅的事件 topic 条件
    
-   `rvmId`
    
-   `rvmContract`：处理该订阅的 Reactive 合约地址
    

### `rnk_getFilters`

查询全网当前活跃的日志过滤器。

返回重点：

-   `uid`
    
-   `chainId`
    
-   `contract`
    
-   `topics`
    
-   `configs`
    
    -   `contract`
        
    -   `rvmId`
        
    -   `active`
        

* * *

## 7\. 合约状态查询

### `rnk_getCode`

查询某个 RVM 中某个合约在指定状态下的 **字节码**。

参数核心：

-   `rvmId`
    
-   `contract`
    
-   `txNumberOrHash`
    
    -   可传交易号
        
    -   或标签：`latest` / `earliest` / `pending`
        

### `rnk_getStorageAt`

查询某个合约指定 storage slot 的值。

参数核心：

-   `rvmId`
    
-   `address`
    
-   `hexKey`：32 字节存储槽
    
-   `txNumberOrHash`
    

* * *

## 8\. 只读调用

### `rnk_call`

在某个 RVM 中对合约做 **只读 EVM 调用**，不会产生交易。

适合：

-   查询 view / pure 方法
    
-   模拟读取链上状态
    

参数核心：

-   `rvmId`
    
-   `args`
    
    -   `to`
        
    -   `data`
        
    -   可选 `from / gas / gasPrice / value`
        
-   `txNumberOrHash`
    

返回：

-   十六进制结果 `result`
    

* * *

## 9\. 区块与 RVM 关系

### `rnk_getBlockRvms`

查询某个 RNK 区块中有哪些 RVM 产生了交易。

返回字段：

-   `rvmId`
    
-   `headTxNumber`
    
-   `prevRnkBlockId`
    
-   `txCount`
    

适合理解：

-   某个 RNK block 内，哪些 RVM 活跃
    
-   每个 RVM 在该 block 中处理了多少交易
    

* * *

## 10\. 参数规律总结

很多接口都会重复出现下面几个参数：

-   `rvmId`：ReactVM 唯一标识
    
-   `txHash`：交易哈希
    
-   `txNumber`：交易序号
    
-   `txNumberOrHash`：既可传交易号，也可传状态标签
    
-   `chainId`：来源链 ID
    
-   `topics`：事件过滤条件
    

* * *

## 11\. 记忆重点

你可以把这些接口按 5 类记：

### A. 交易

-   `rnk_getTransactionByHash`
    
-   `rnk_getTransactionByNumber`
    
-   `rnk_getTransactions`
    
-   `rnk_getHeadNumber`
    

### B. 日志

-   `rnk_getTransactionLogs`
    

### C. RVM / 网络信息

-   `rnk_getVm`
    
-   `rnk_getVms`
    
-   `rnk_getStat`
    
-   `rnk_getBlockRvms`
    
-   `rnk_getRnkAddressMapping`
    

### D. 订阅 / 过滤器

-   `rnk_getSubscribers`
    
-   `rnk_getFilters`
    

### E. 合约状态 / 调用

-   `rnk_getCode`
    
-   `rnk_getStorageAt`
    
-   `rnk_call`
    

* * *

## 12\. 一句话理解

RNK 扩展 RPC 本质上是在标准 Ethereum RPC 之外，补充了 **ReactVM 维度的交易、日志、订阅、状态和统计查询能力**。
<!-- DAILY_CHECKIN_2026-03-20_END -->

# 2026-03-19
<!-- DAILY_CHECKIN_2026-03-19_START -->




# Reactive Contracts — Subscriptions

## 🧩 核心概念

-   **Subscription（订阅）**：定义 RC 监听哪些链上事件
    
-   触发条件：匹配事件 → 自动调用 `react()`
    

订阅由以下条件组成：

-   `originChainId`
    
-   `contract address`
    
-   `topics[0–3]`
    

* * *

## ⚙️ 基本用法

### 在构造函数中订阅

```
if (!vm) {
    service.subscribe(
        originChainId,
        _contract,
        _topic_0,
        REACTIVE_IGNORE,
        REACTIVE_IGNORE,
        REACTIVE_IGNORE
    );
}
```

⚠️ 注意：

-   ReactVM 中不能调用 `subscribe()` → 用 `if (!vm)` 判断
    

* * *

## 🔍 过滤规则

支持精确匹配：

-   Chain ID
    
-   Contract address
    
-   Topics 0–3
    

* * *

## 🎯 通配符

### REACTIVE\_IGNORE（万能匹配）

```
0xa65f96fc...
```

### 其他通配方式

-   `uint256(0)` → 任意 chain
    
-   `address(0)` → 任意合约
    

👉 至少要有一个具体条件

* * *

## 📌 常见订阅模式

### 1️⃣ 某合约所有事件

```
subscribe(CHAIN_ID, contract, IGNORE, IGNORE, IGNORE, IGNORE);
```

### 2️⃣ 某类事件（topic0）

```
subscribe(CHAIN_ID, address(0), topic0, IGNORE, IGNORE, IGNORE);
```

### 3️⃣ 指定合约 + 事件

```
subscribe(CHAIN_ID, contract, topic0, IGNORE, IGNORE, IGNORE);
```

* * *

## 🔁 多订阅

```
service.subscribe(...);
service.subscribe(...);
```

👉 一个条件 = 一个 subscription

* * *

## ❌ 取消订阅

通过 system contract：

```
unsubscribeContract(...)
```

* * *

## ⚠️ 限制

### ❌ 不支持

-   范围查询（<, >）
    
-   位运算过滤
    
-   OR 条件
    
-   全局订阅（所有链/所有事件）
    

### ⚠️ 其他

-   只支持 **精确匹配**
    
-   重复订阅 ≈ 1 个（但会浪费 gas）
    

* * *

## 🔄 动态订阅（重点）

### 📊 流程

1.  ReactVM 收到事件
    
2.  `react()` 判断逻辑
    
3.  emit `Callback`
    
4.  RNK 执行订阅/取消订阅
    

* * *

## 🧠 RNK 侧函数

```
function subscribe(...) rnOnly callbackOnly
function unsubscribe(...) rnOnly callbackOnly
```

👉 只有 RNK 能真正改订阅

* * *

## ⚡ react() 逻辑

```
if (topic == SUBSCRIBE) {
    emit Callback → 调 subscribe
}
else if (topic == UNSUBSCRIBE) {
    emit Callback → 调 unsubscribe
}
else {
    emit Callback → 执行业务逻辑
}
```

* * *

## 🧾 总结一句话

👉 **Subscription = 精确事件过滤器 + 回调触发机制**
<!-- DAILY_CHECKIN_2026-03-19_END -->

# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->






# Reactive Network 学习笔记

## 一、核心概念

### 🔹 Reactive Network 是什么？

-   一个 **EVM 兼容链**
    
-   核心是 **Reactive Contracts（RCs）**
    

👉 本质：

> **事件驱动 + 跨链自动执行**

* * *

### 🔹 Reactive Contracts（RCs）

特点：

-   监听多链事件（logs）
    
-   自动执行 Solidity 逻辑
    
-   条件满足 → 触发跨链 callback
    

👉 类比：

> **链上版 IFTTT（If This Then That）**

* * *

## 二、核心能力（能做什么）

-   自动止盈 / 止损
    
-   清算保护
    
-   自动调仓（Rebalancing）
    
-   收益优化（Yield）
    
-   跨链自动化流程
    

* * *

## 三、关键架构

### 🔹 1. Origin & Destination

| 概念 | 含义 |
| --- | --- |
| Origin | 事件来源链（读 logs） |
| Destination | 执行回调链 |

✔ 可以：

-   多 origin
    
-   多 destination
    
-   动态选择目标链
    

* * *

### 🔹 2. Callback Proxy（关键安全组件）

作用：

-   统一入口执行 callback
    
-   保证调用可信
    

目标合约必须验证：

1.  `msg.sender == Callback Proxy`
    
2.  RVM ID 匹配 RC
    

* * *

### 🔹 3. Reactive 执行流程（核心！）

```
监听事件 → 满足条件 → 发起 callback → 目标链执行
```

* * *

## 四、跨链通信

### 🔹 默认方式：Callback Proxy

-   官方提供
    
-   简单、安全
    

* * *

### 🔹 替代方案：Hyperlane

使用场景：

-   某链没有 Callback Proxy
    
-   需要更灵活路由
    
-   已集成 Hyperlane 系统
    

👉 本质：**跨链消息传输层**

* * *

## 五、运行环境

### 🔹 双环境部署（非常重要）

| 环境 | 作用 |
| --- | --- |
| RNK（Reactive Network） | 用户交互 + 管理订阅 |
| RVM（ReactVM） | 执行逻辑 |

👉 特点：

-   同一合约 **两份部署**
    
-   **状态不共享！**
    

* * *

### 🔹 ReactVM 限制

-   ❌ 不能访问外部 RPC
    
-   ❌ 不能调用链外服务
    
-   ✅ 只能：
    
    -   处理 logs
        
    -   发 callback
        

* * *

## 六、开发关键点

### 🔹 1. 订阅机制

-   指定：
    
    -   chain
        
    -   contract
        
    -   event（topic）
        

* * *

### 🔹 2. 触发逻辑

-   在 `react()` 中写条件判断
    

* * *

### 🔹 3. Callback

-   构造 payload
    
-   发送到 destination
    

* * *

## 七、网络 & 链支持

### 🔹 主网（Mainnet）

-   Ethereum / Arbitrum / Base / BSC / Avalanche 等
    
-   Reactive Chain ID：**1597**
    

* * *

### 🔹 测试网（Testnet）

-   Sepolia / Base Sepolia / Lasna
    
-   Lasna Chain ID：**5318007**
    

⚠️ 注意：

> 主网和测试网 **不能混用**

* * *

## 八、合约验证（Sourcify）

### 🔹 部署时验证

```
forge create --verify --verifier sourcify ...
```

### 🔹 部署后验证

```
forge verify-contract ...
```

* * *

### 🔹 验证结果查看

-   使用 **Reactscan**
    
-   状态：`VERIFIED (EXACT MATCH)`
    

* * *

## 九、工具生态

-   Reactscan（浏览器）
    
-   Reactive Library（开发库）
    
-   RNK RPC（节点接口）
    
-   Demo（学习入口）
    

* * *

## 十、核心一句话总结

> **Reactive = 监听链上事件 → 自动判断 → 跨链执行**

* * *

## 🔥 最关键理解（建议记住）

1.  RC ≠ 普通合约  
    👉 它是“自动运行”的
    
2.  不是用户触发  
    👉 是“事件触发”
    
3.  双环境（RNK + RVM）  
    👉 且状态隔离
    

* * *
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->







# Reactive Network Demo 学习笔记

## 一、整体概念

**Reactive Network 的核心能力：**

-   🔍 低延迟监听链上事件（logs）
    
-   🔁 根据事件自动触发跨链调用
    
-   ⚡ 实现“事件驱动”的智能合约逻辑
    

👉 本 Demo 展示的是一个**基础的响应式（Reactive）合约系统**

* * *

## 二、系统架构

整个 Demo 包含三个核心合约：

### 1️⃣ Origin Contract（源链合约）

**BasicDemoL1Contract**

功能：

-   接收 Ether
    
-   将 Ether 原路返回给发送者
    
-   触发 `Received` 事件（记录交易信息）
    

👉 本质：**事件产生者（Event Source）**

* * *

### 2️⃣ Reactive Contract（响应式合约）

**BasicDemoReactiveContract**

功能核心：

-   订阅源链合约的事件（logs）
    
-   解析事件数据
    
-   满足条件时触发跨链回调
    

👉 本质：**事件监听 + 自动执行逻辑**

* * *

### 3️⃣ Destination Contract（目标链合约）

**BasicDemoL1Callback**

功能：

-   接收 Reactive Network 发来的回调
    
-   校验调用来源是否合法
    
-   记录执行信息（`CallbackReceived` 事件）
    

👉 本质：**执行最终动作**

* * *

## 三、核心机制详解

### 🔹 1. 事件订阅（Subscription）

部署时自动订阅：

```
service.subscribe(
    originChainId,
    _contract,
    topic_0,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE
);
```

说明：

-   `originChainId`：源链
    
-   `_contract`：监听的合约地址
    
-   `topic_0`：事件签名（Event Signature）
    

👉 作用：**只监听特定事件**

* * *

### 🔹 2. Log 数据结构

```
struct LogRecord {
    uint256 chain_id;
    address _contract;
    uint256 topic_0;
    uint256 topic_1;
    uint256 topic_2;
    uint256 topic_3;
    bytes data;
    uint256 block_number;
    uint256 op_code;
    uint256 block_hash;
    uint256 tx_hash;
    uint256 log_index;
}
```

重点字段：

-   `topic_0`：事件类型
    
-   `topic_3`：本 Demo 用来表示金额（重要！）
    
-   `data`：额外数据
    

👉 本质：**链上事件的结构化表示**

* * *

### 🔹 3. 事件响应逻辑（react 函数）

```
function react(LogRecord calldata log) external vmOnly {
    
    if (log.topic_3 >= 0.01 ether) {
        bytes memory payload = abi.encodeWithSignature("callback(address)", address(0));
        emit Callback(destinationChainId, callback, GAS_LIMIT, payload);
    }
}
```

逻辑拆解：

1.  接收事件数据（log）
    
2.  判断条件：
    
    -   `topic_3 >= 0.01 ETH`
        
3.  构造调用 payload
    
4.  触发跨链回调（emit Callback）
    

👉 核心思想：

> “监听事件 → 判断条件 → 触发动作”

* * *

### 🔹 4. 跨链回调机制

```
emit Callback(destinationChainId, callback, GAS_LIMIT, payload);
```

参数含义：

-   `destinationChainId`：目标链
    
-   `callback`：目标合约地址
    
-   `payload`：调用函数数据
    
-   `GAS_LIMIT`：执行 gas 限制
    

👉 作用：**让目标链执行函数**

* * *

### 🔹 5. Callback 合约安全机制

Destination 合约会：

-   校验调用者是否合法（Reactive Network）
    
-   防止恶意调用
    
-   记录事件：
    

```
CallbackReceived(...)
```

* * *

## 四、完整流程（重点！）

### 🔄 整体执行流程：

1️⃣ 用户在源链调用 `BasicDemoL1Contract`  
2️⃣ 合约 emit `Received` 事件

⬇️

3️⃣ Reactive Network 捕获 log

⬇️

4️⃣ `BasicDemoReactiveContract.react()` 被触发

⬇️

5️⃣ 判断金额 ≥ 0.01 ETH

⬇️

6️⃣ emit `Callback`

⬇️

7️⃣ 目标链执行 `callback()`

⬇️

8️⃣ `BasicDemoL1Callback` 记录结果

* * *

## 五、关键设计思想

### 💡 1. 事件驱动架构（Event-driven）

-   不主动调用
    
-   等事件触发逻辑
    

* * *

### 💡 2. 跨链自动化

-   无需人工干预
    
-   自动执行跨链逻辑
    

* * *

### 💡 3. 解耦设计

-   源链：只负责产生事件
    
-   Reactive：只负责判断逻辑
    
-   目标链：只负责执行
    

👉 优点：可扩展性强

* * *

## 六、可扩展方向

文中提到的增强点：

### 🔹 1. 多事件订阅

-   同时监听多个合约/事件
    

### 🔹 2. 动态订阅

-   根据条件调整监听对象
    

### 🔹 3. 状态持久化

-   让 reactive contract 有“记忆”
    

### 🔹 4. 自定义 callback

-   灵活构造 payload
    

* * *

## 七、部署要点

-   克隆项目
    
-   配置环境变量（重要！）
    
-   按 Deployment & Testing 步骤执行
    

* * *

## 八、总结一句话

👉 Reactive Network 的核心模式：

> **“监听链上事件 → 自动判断 → 跨链执行”**
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->








源链事件 -> 订阅捕获 -> 条件判断 -> 发起目标链回调

核心知识点：

\- 源链合约可以通过 event 把链上行为暴露为日志，供外部系统监听。

\- receive() 可以在合约接收 ETH 时自动执行逻辑。

\- 事件里的 indexed 参数会进入 topic，后续可用于高效过滤和订阅。

\- 一个合约可以只负责“产生事件”，不承担复杂业务处理。

\- Reactive 合约的关键不是主动调用，而是“订阅特定日志后被动响应”。

\- 构造函数中可通过系统合约注册订阅条件：链 ID、合约地址、topic\_0 等。

\- topic\_0 通常对应事件签名哈希，用来唯一标识某类事件。

\- react(LogRecord log) 表示接收到匹配日志后的响应入口。

\- 可以直接读取日志中的 topic\_1/topic\_2/topic\_3 做条件判断。

\- 这里利用 log.topic\_3 判断转账金额是否达到阈值。

\- Reactive 合约本身不直接完成目标链调用，而是通过发出 Callback(...) 事件交给底层系统执行。

\- 回调负载通常通过 abi.encodeWithSignature(...) 编码。

\- 目标链回调合约需要做调用来源校验，避免任意地址伪造调用。

\- authorizedSenderOnly 用来限制只有授权回调代理可调用。

\- rvmIdOnly(sender) 用来校验调用是否来自合法的 Reactive 执行上下文。

\- 跨链流程里通常有三类角色：

\- 源链合约：产生日志

\- Reactive 合约：监听并决策

\- 目标链合约：接收回调并执行业务

\- 部署时通常需要分别配置源链、目标链、Reactive Network 的 RPC 和私钥。

\- 这类架构适合做“事件触发型自动执行”，例如条件触发、告警响应、自动回调等。
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->










# Implementing Basic Reactive Functions 学习笔记

## 一、Reactive Contract (RC) 概念

-   **Reactive Contract** 类似 **Ethereum Smart Contract**，但它是\*\*事件驱动（event-driven）\*\*的。
    
-   主要作用：  
    **监听区块链事件 → 判断条件 → 自动执行交易回调。**
    
-   本案例：  
    用于 **Uniswap V2 的 Stop Order（止损单）自动执行**。
    

* * *

# 二、核心功能

该合约 **监听 Uniswap V2 liquidity pool 的 Sync 事件**，当价格达到预设阈值时：

1.  触发 stop order
    
2.  发送 callback 交易
    
3.  执行止损交易
    

* * *

# 三、合约主要结构

## 1 Event（事件）

用于记录合约执行过程。

主要事件：

| Event | 作用 |
| --- | --- |
| Subscribed | 订阅事件 |
| VM | VM执行标记 |
| AboveThreshold | 超过阈值 |
| CallbackSent | 已发送回调 |
| Done | 执行完成 |

* * *

# 2 关键变量

### 常量

| 常量 | 作用 |
| --- | --- |
| SEPOLIA_CHAIN_ID | Sepolia测试网 |
| UNISWAP_V2_SYNC_TOPIC_0 | Uniswap Sync事件 |
| STOP_ORDER_STOP_TOPIC_0 | Stop事件 |
| CALLBACK_GAS_LIMIT | callback gas限制 |

* * *

### 状态变量

| 变量 | 含义 |
| --- | --- |
| triggered | 是否已触发止损 |
| done | 是否执行完成 |
| pair | Uniswap交易对地址 |
| stop_order | stop order合约 |
| client | 用户地址 |
| token0 | 选择token0或token1 |
| coefficient | 价格系数 |
| threshold | 触发阈值 |

* * *

# 四、核心函数

* * *

# 1 Constructor（初始化）

初始化参数并**订阅事件**。

订阅两个事件：

### 1️⃣ Uniswap Pair

监听：

```
Sync event
```

用于检测流动池储备变化。

### 2️⃣ Stop Order Contract

监听：

```
Stop event
```

用于确认交易完成。

* * *

# 2 react() 函数（核心逻辑）

react() 用于 **处理区块链事件**。

流程：

### 情况1：Stop Order事件

如果事件来自 stop\_order：

检查：

-   triggered == true
    
-   topic匹配
    
-   pair地址匹配
    
-   client地址匹配
    

满足条件：

```
done = true
emit Done()
```

表示 **止损交易完成**。

* * *

### 情况2：Uniswap Sync事件

获取流动池储备：

```
reserve0
reserve1
```

调用：

```
below_threshold()
```

判断是否达到止损条件。

如果满足：

1️⃣ 发送 CallbackSent 事件  
2️⃣ 构造 callback payload

```
stop(address,address,address,bool,uint256,uint256)
```

3️⃣ 设置

```
triggered = true
```

4️⃣ 执行 callback

```
emit Callback(...)
```

* * *

# 3 below\_threshold() 函数

作用：

**判断价格是否低于阈值**

计算方式：

如果交易 token0：

```
(reserve1 * coefficient) / reserve0 <= threshold
```

如果交易 token1：

```
(reserve0 * coefficient) / reserve1 <= threshold
```

本质：

```
token price <= threshold
```

* * *

# 五、执行流程（Execution Flow）

### 1 部署合约

初始化参数  
订阅事件

* * *

### 2 监听事件

监听：

-   Uniswap Sync
    
-   Stop Order Stop
    

* * *

### 3 价格触发

当 pool reserves 满足：

```
price <= threshold
```

执行：

```
callback(stop order)
```

* * *

### 4 执行交易

Stop Order Contract 执行交易。

* * *

### 5 完成

捕获 Stop Event：

```
done = true
```

任务结束。

* * *

# 六、核心机制总结

Reactive Contract运行逻辑：

```
监听事件
      ↓
解析数据
      ↓
判断条件
      ↓
发送callback
      ↓
执行交易
      ↓
确认完成
```

* * *

# 七、一句话总结

该 Reactive Contract 通过**监听 Uniswap V2 的 Sync 事件**，当流动池价格达到设定阈值时，自动触发 **Stop Order 回调交易**，实现链上的**自动止损交易机制**。
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->











# Uniswap V2 学习笔记

## 1\. Uniswap V2 概述

Uniswap V2 是运行在 **Ethereum** 上的 **去中心化交易所（DEX）**，使用 **自动做市商（AMM）** 模型进行交易。

核心组成：

-   **Liquidity Pool（流动性池）**
    
-   **Smart Contracts（智能合约）**
    
-   **Constant Product Market Maker（恒定乘积公式）**
    

特点：

-   无传统做市商
    
-   交易完全通过智能合约执行
    
-   所有交易记录公开，可在 **Etherscan** 查看
    

* * *

# 2\. Liquidity Pools（流动性池）

流动性池 = **两种 Token 的储备池**

例如：

```
ETH / USDC pool
```

池子包含：

```
reserve0 → token0 数量
reserve1 → token1 数量
```

作用：

1️⃣ 用户可以 **swap token**  
2️⃣ 用户可以 **提供流动性赚手续费**

所有操作都通过 **智能合约执行**。

* * *

# 3\. 定价机制（Constant Product Formula）

Uniswap V2 使用公式：

x \\cdot y = k

其中：

-   **x** = token0 数量
    
-   **y** = token1 数量
    
-   **k** = 常数（池子总流动性）
    

### 公式含义

池子交易前：

```
x * y = k
```

交易后仍然必须满足：

```
x' * y' ≥ k
```

价格变化来源：

-   买入某个 token → 该 token reserve 减少
    
-   价格自动上涨
    

* * *

# 4\. Swap 函数核心逻辑

swap 函数主要流程：

### 1️⃣ 检查输出是否合法

```
require(amount0Out > 0 || amount1Out > 0);
```

必须至少输出一个 token。

* * *

### 2️⃣ 获取当前储备

```
(uint112 reserve0, uint112 reserve1,) = getReserves();
```

获取 pool 当前 token 数量。

* * *

### 3️⃣ 检查流动性

```
require(amount0Out < reserve0 && amount1Out < reserve1);
```

不能把池子掏空。

* * *

### 4️⃣ 计算输入 token

```
amount0In
amount1In
```

输入 =  
池子原有储备 − 新储备

* * *

### 5️⃣ 收取 0.3% 手续费

计算方式：

```
balanceAdjusted = balance * 1000 - amountIn * 3
```

解释：

```
fee = 0.3%
1000 - 3 = 997
```

* * *

### 6️⃣ 检查 k 是否成立

```
require(balanceAdjusted0 * balanceAdjusted1 >= reserve0 * reserve1 * 1000^2)
```

保证交易后：

```
x * y >= k
```

* * *

### 7️⃣ 发送 token

```
_safeTransfer(token, to, amount)
```

把 swap 得到的 token 发给用户。

* * *

### 8️⃣ 更新储备

```
_update(balance0, balance1)
```

更新 pool 的 reserve。

* * *

### 9️⃣ Flash Swap 回调（可选）

```
uniswapV2Call()
```

支持 **flash swap / flash loan**。

* * *

# 5\. Uniswap V2 重要事件

## 1️⃣ Swap Event

每次交易都会触发。

结构：

```
event Swap(
 address sender,
 uint amount0In,
 uint amount1In,
 uint amount0Out,
 uint amount1Out,
 address to
);
```

字段含义：

| 字段 | 含义 |
| --- | --- |
| sender | 发起 swap 的地址 |
| amount0In | token0 输入 |
| amount1In | token1 输入 |
| amount0Out | token0 输出 |
| amount1Out | token1 输出 |
| to | 接收 token 的地址 |

用途：

-   监听交易
    
-   分析交易数据
    
-   监控 DeFi activity
    

* * *

## 2️⃣ Sync Event

每次 **reserve 更新**都会触发。

结构：

```
event Sync(uint112 reserve0, uint112 reserve1);
```

含义：

| 字段 | 说明 |
| --- | --- |
| reserve0 | token0 新储备 |
| reserve1 | token1 新储备 |

作用：

-   更新池子状态
    
-   计算价格
    
-   计算 slippage
    

* * *

# 6\. Swap + Sync 关系

一次 swap 通常触发：

```
Swap event
↓
Sync event
```

流程：

```
用户交易
↓
Swap event 记录交易
↓
_update() 更新储备
↓
Sync event 记录新 reserve
```

* * *

# 7\. 为什么监控 Swap / Sync

在 DeFi 或 Reactive Contract 中：

常监听：

```
Swap events
```

原因：

-   包含每笔交易信息
    
-   适合实时监控市场
    

Sync 用于：

-   获取最新 liquidity
    
-   计算价格
    

* * *

# 8\. 核心总结

Uniswap V2 的关键点：

**1️⃣ Liquidity Pools**

```
token0 + token1
```

* * *

**2️⃣ 定价公式**

```
x * y = k
```

* * *

**3️⃣ 手续费**

```
0.3%
```

* * *

**4️⃣ 两个关键事件**

Swap  
Sync

* * *

**5️⃣ Swap核心流程**

```
检查参数
→ 计算输入
→ 扣手续费
→ 保证 x*y=k
→ 发送token
→ 更新reserve
→ 触发事件
```
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->













# Oracles 学习笔记

## 1\. Oracle 的作用

**Oracle（预言机）** 是连接 **区块链（on-chain）** 和 **现实世界数据（off-chain）** 的桥梁。

因为：

-   智能合约运行在 **确定性环境（deterministic environment）**
    
-   无法直接访问外部数据（例如 API、天气、价格）
    

所以需要 **Oracle 将链下数据带到链上**。

常见数据类型：

-   价格数据（ETH/USD）
    
-   天气数据
    
-   体育比赛结果
    
-   IoT设备数据
    
-   政府或公共数据库
    

* * *

# 2\. Oracle Problem（预言机问题）

问题核心：

> 如何在不破坏 **去中心化和信任最小化** 的情况下，把链下数据带到链上？

挑战：

-   数据来源是否可信
    
-   是否存在单点故障
    
-   是否可能被操纵
    

* * *

# 3\. Oracle 的工作流程

基本流程：

1️⃣ Oracle 从 **外部数据源** 获取数据  
例如：

-   API
    
-   交易所
    
-   IoT设备
    

2️⃣ Oracle 对数据进行验证或聚合

3️⃣ Oracle 使用 **私钥签名交易**

4️⃣ 将数据提交到 **区块链智能合约**

* * *

# 4\. 提高安全性的方式

为了防止数据被操控，Oracle网络通常采用：

### ① 多数据源聚合

从多个数据源获取数据，减少单点风险

### ② 多节点网络

多个 Oracle 节点提供数据

### ③ Multisig（多重签名）

需要多个节点签名才能提交数据

优点：

-   防止单个节点作恶
    
-   提高可信度
    

* * *

# 5\. 常见 Oracle 项目

| 项目 | 特点 |
| --- | --- |
| Chainlink | 最大的去中心化预言机网络 |
| Band Protocol | 跨链 Oracle 网络 |

* * *

# 6\. Oracle 的应用场景

### DeFi

价格预言机

用途：

-   借贷利率
    
-   清算
    
-   资产兑换
    

例子：  
ETH/USD price feed

* * *

### 保险

触发自动赔付

例子：

-   台风
    
-   地震
    
-   航班延误
    

* * *

### 体育竞猜

Oracle提供比赛结果

* * *

# 7\. Chainlink 示例（价格预言机）

智能合约获取 ETH/USD 价格：

```
function getLatestPrice() public view returns (int)
```

调用：

```
priceFeed.latestRoundData()
```

返回：

-   price
    
-   roundID
    
-   timestamp 等信息
    

作用：  
获取 **最新 ETH/USD 价格**

* * *

# 8\. Oracle 的限制

智能合约 **不能主动运行**。

必须由：

**EOA（Externally Owned Account）**  
发起交易。

所以：

-   Oracle数据更新
    
-   合约函数调用
    

都需要 **用户或外部交易触发**。

这意味着：

> 智能合约不能真正“实时响应”。

* * *

# 9\. Reactive Contracts（RC）

Reactive Contracts 的特点：

**监听事件 → 自动执行操作**

例如：

```
监听 Oracle 价格更新
→ 自动执行清算
→ 自动执行交易
```

RC 可以：

-   监听链上事件
    
-   跨链触发交易
    
-   自动执行逻辑
    

* * *

# 10\. Oracle + Reactive Contracts

结合后可以实现：

```
现实世界事件
        ↓
Oracle 上传数据
        ↓
Reactive Contract 监听事件
        ↓
自动执行链上操作
```

例子：

| 事件 | 自动操作 |
| --- | --- |
| ETH价格暴跌 | 自动清算 |
| 天气灾害 | 自动保险赔付 |
| 比赛结束 | 自动结算竞猜 |

* * *

# 11\. 核心总结

**Oracle**

-   连接链上与链下数据
    
-   解决智能合约无法访问外部世界的问题
    

**Oracle Problem**

-   如何保证数据可信
    

**解决方案**

-   多节点
    
-   多数据源
    
-   Multisig
    
-   去中心化 Oracle 网络
    

**Oracle + Reactive Contracts**

-   实现真正的 **实时自动化 Web3 应用**
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->















# Subscriptions

## 1\. 什么是 Subscription

**Subscription（订阅）** 是 Reactive Contracts 的核心机制，用来让合约**自动监听其他合约的事件并触发逻辑**。

流程：

1.  合约通过 `subscribe()` 注册要监听的事件
    
2.  当链上出现符合条件的 event log
    
3.  Reactive Network 通知订阅合约
    
4.  合约执行 `react()` 处理事件
    

简化流程：

```
Event 发生
     ↓
Reactive Network 检测到
     ↓
通知订阅的 Reactive Contract
     ↓
调用 react()
```

* * *

# 2\. 订阅服务接口（ISubscriptionService）

Reactive Contracts 通过 **系统合约 SubscriptionService** 进行订阅。

```
function subscribe(
    uint256 chain_id,
    address _contract,
    uint256 topic_0,
    uint256 topic_1,
    uint256 topic_2,
    uint256 topic_3
) external;
```

取消订阅：

```
function unsubscribe(...)
```

### 参数说明

| 参数 | 含义 |
| --- | --- |
| chain_id | 事件来源链 |
| _contract | 触发事件的合约 |
| topic_0~3 | event topics |

Topics 对应 Solidity 事件的 indexed 参数。

* * *

# 3\. 订阅条件规则

订阅是通过 **chain + contract + topics** 进行过滤。

### Wildcard（通配符）

| 类型 | 写法 |
| --- | --- |
| 任何 chain | 0 |
| 任何 contract | address(0) |
| 任何 topic | REACTIVE_IGNORE |

### 必须满足

**至少一个参数必须是具体值**

否则订阅没有意义。

* * *

# 4\. 常见订阅示例

## 订阅某个合约的所有事件

```
service.subscribe(
    CHAIN_ID,
    contractAddress,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE
);
```

* * *

## 订阅某个 Event 类型

例如 Uniswap Sync event

```
service.subscribe(
    CHAIN_ID,
    address(0),
    topic0,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE
);
```

* * *

## 同时限定合约 + event

```
service.subscribe(
    CHAIN_ID,
    contractAddress,
    topic0,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE
);
```

* * *

# 5\. Constructor 订阅（静态订阅）

通常在 **constructor 中初始化订阅**：

```
constructor(...) {
    if (!vm) {
        service.subscribe(...);
    }
}
```

原因：

Reactive Contract **会在两个环境部署**

| 环境 | 作用 |
| --- | --- |
| Reactive Network | 有 system contract |
| ReactVM | 没有 system contract |

所以要避免 ReactVM 执行 subscribe。

* * *

# 6\. IReactive 接口

Reactive Contracts 必须实现：

```
function react(LogRecord calldata log)
```

### LogRecord 结构

```
struct LogRecord {
    chain_id
    _contract
    topic_0
    topic_1
    topic_2
    topic_3
    data
    block_number
    block_hash
    tx_hash
}
```

作用：

提供完整的 **event log 信息**

* * *

# 7\. Callback 机制

当 Reactive Contract 处理事件后：

会触发 **Callback**

```
event Callback(
    uint256 chain_id,
    address _contract,
    uint64 gas_limit,
    bytes payload
);
```

作用：

让 Reactive Network 执行后续操作。

* * *

# 8\. 动态订阅（Dynamic Subscription）

Reactive Contracts 可以根据事件：

-   自动订阅
    
-   自动取消订阅
    

流程：

```
事件触发
 ↓
react() 执行
 ↓
emit Callback
 ↓
Reactive Network 调用 subscribe/unsubscribe
```

* * *

# 9\. 动态订阅示例逻辑

react() 中：

```
if topic == SUBSCRIBE
    -> emit callback 调用 subscribe()

if topic == UNSUBSCRIBE
    -> emit callback 调用 unsubscribe()

else
    -> 处理业务逻辑
```

示例：

```
if (log.topic_0 == SUBSCRIBE_TOPIC_0) {
    emit Callback(... subscribe payload ...);
}
```

* * *

# 10\. Subscription 限制

Reactive Network 对订阅有一些限制：

### 不支持

❌ 不支持范围匹配

```
<
>
range
bitwise
```

* * *

❌ 不支持复杂逻辑

例如：

```
(topicA OR topicB)
```

只能通过 **多次 subscribe** 实现。

* * *

❌ 不允许

```
所有 chain + 所有 contract
```

* * *

# 11\. 重复订阅

允许重复 subscribe。

但：

-   每次调用都会产生 gas
    
-   系统不会自动去重
    

开发者需要 **保证 idempotency**。

* * *

# 12\. unsubscribe 成本高

原因：

需要 **查找并删除存储中的 subscription**。

因此建议：

-   减少 unsubscribe
    
-   尽量设计长期订阅
    

* * *

# 13\. 最佳实践

### 1️⃣ constructor 初始化订阅

避免 runtime 注册。

* * *

### 2️⃣ 使用 topic 精确过滤

减少不必要事件。

* * *

### 3️⃣ 避免过多 subscription

否则会产生 **combinatorial explosion**。

* * *

### 4️⃣ react() 逻辑要轻量

因为 callback 有 **gas limit**。

* * *

# 14\. 总结

Reactive Contracts 订阅机制核心：

1️⃣ 使用 `subscribe()` 监听事件  
2️⃣ Reactive Network 捕获 event  
3️⃣ 调用 `react()`  
4️⃣ 合约处理事件并 emit Callback  
5️⃣ 网络执行后续操作

核心能力：

-   跨链事件监听
    
-   自动触发逻辑
    
-   动态订阅管理
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->

















# Reactive Contracts (RCs) 架构与执行机制

## 一、 双重执行环境：一个合约，两个状态

在 Reactive 网络中，每个部署的合约实际上运行在两个平行的实例中。它们代码完全相同，但**状态（变量）独立存储**。

1.  **Reactive Network (RN) 状态：**
    
    -   **角色：** 合约的“指挥部”。
        
    -   **职责：** 直接与系统合约交互，负责订阅/取消订阅原始链（ETH, Polygon 等）的事件。
        
2.  **ReactVM 状态：**
    
    -   **角色：** 隔离的“实验室”。
        
    -   **职责：** 专门执行业务逻辑（`react()` 函数），处理接收到的事件并生成回调请求。
        
    -   **限制：** 每个部署地址拥有独立的 ReactVM，不同地址的合约互不干扰。
        

* * *

## 二、 核心机制：如何识别与限制环境？

为了让同一套代码在两个环境中各司其职，系统通过 `detectVm()` 函数进行环境检测。

### 1\. 环境检测原理（Assembly Check）

系统通过检查特定地址 `0x...fffFfF`（系统合约地址）是否存在代码来判断环境：

-   **Code Size > 0** $\\rightarrow$ 在 **Reactive Network** 中（系统合约存在）。
    
-   **Code Size == 0** $\\rightarrow$ 在 **ReactVM** 中（受限环境，无系统合约）。
    

### 2\. 访问控制修饰器 (Modifiers)

-   `rnOnly`: 仅允许在 Reactive Network 执行（如：修改订阅列表、管理员暂停）。
    
-   `vmOnly`: 仅允许在 ReactVM 执行（如：响应事件逻辑）。
    

* * *

## 三、 变量与状态管理

由于合约在两个环境中运行，变量通常分为两组：

-   **RN 变量 (继承自** `AbstractReactive`**)：**
    
    -   `service`: 用于调用订阅服务的接口。
        
    -   `vm`: 布尔标志位，标记当前环境。
        
-   **ReactVM 变量 (业务逻辑相关)：**
    
    -   如 `triggered`, `done`, `threshold` 等。这些变量在 `react()` 逻辑中被修改，用于控制自动化行为。
        

* * *

## 四、 交易执行流 (Transaction Flow)

### 1\. 外部驱动交易 (Reactive Network 层)

-   **用户发起：** 管理员手动调用 `pause()` 或 `resume()`。这些函数使用 `rnOnly` 修饰，通过调用 `service.unsubscribe/subscribe` 来开关“传感器”。
    
-   **事件分发：** RN 监控到原始链事件后，将其派发给对应的 ReactVM 实例。
    

### 2\. 自动触发交易 (ReactVM 层)

-   **触发机制：** 无法由用户直接调用，仅在接收到订阅事件时由系统触发。
    
-   **执行逻辑：** 调用 `react()` $\\rightarrow$ 状态计算 $\\rightarrow$ 满足条件 $\\rightarrow$ 触发 **Callback**（回调交易）。
    
-   **最终行动：** Callback 请求被发回 Reactive Network，最终在目标链（如 Sepolia）上执行实际的链上动作（如执行 Swap）。
    

* * *

## 五、 典型代码逻辑梳理 (以 Uniswap 止损订单为例)

1.  **构造函数 (Constructor):**
    
    -   初始化所有变量。
        
    -   `if (!vm)`：如果是在 **RN** 环境，立即调用 `service.subscribe` 订阅 Uniswap 的 `Sync` 事件。
        
2.  **React 函数 (**`vmOnly`**):**
    
    -   接收事件日志 (`LogRecord`)。
        
    -   判断价格是否低于 `threshold`。
        
    -   如果满足，修改 `triggered = true` 并发出 `emit Callback(...)` 指令。
        

* * *

### 总结对比表

| 维度 | Reactive Network (RN) | ReactVM |
| 部署地址 | 0x... (主网地址) | 隔离的虚拟机环境 |
| 主要功能 | 管理订阅、行政管理 (Pause/Resume) | 运行业务逻辑、计算事件数据 |
| Gas 消耗 | 用户或系统支付 | 逻辑执行（通常极低或受限） |
| 系统调用 | 可调用 service.subscribe | 禁止直接调用订阅服务 |
| 输出结果 | 状态更新或订阅变更 | 触发跨链 Callback |
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->



















# Reactive Contracts (RCs) 基础与核心概念

## 一、 核心定义：什么是反应式合约？

**反应式合约 (Reactive Contracts, RCs)** 是一种能够**自主监控**区块链事件，并根据预设逻辑**自动触发**后续动作的智能合约。

-   **传统合约** = 被动（Passive）。像一个等待拨打的电话号码。
    
-   **反应式合约** = 主动（Reactive）。像一个全天候运行的自动化传感器。
    

* * *

## 二、 关键技术范式：控制反转 (Inversion of Control, IoC)

这是理解 RCs 的核心灵魂。

### 1\. 传统模式：外部驱动

-   **执行者：** 必须由外部账户 (EOA) 或中心化机器人 (Bot) 发起交易。
    
-   **痛点：** 依赖私钥持有者在线，且机器人服务通常是中心化的，存在单点故障。
    

### 2\. RC 模式：控制反转

-   **执行者：** 合约通过监听链上事件（Topic 0）自主决定何时执行。
    
-   **价值：** 实现了**去中心化的自动化**。不再需要模拟人类签名的外部实体，只要输入和输出都在链上，逻辑就在 Reactive Network 中闭环运行。
    

* * *

## 三、 反应式合约的工作原理

一个 RC 的生命周期包含以下三个阶段：

1.  **订阅与监听 (Monitor)：** 开发者指定感兴趣的链、合约地址和特定事件（如 `Transfer`, `Swap`, `FlashLoan` 等）。
    
2.  **逻辑处理 (React)：** \* RC 是**有状态的 (Stateful)**，可以跨时间存储数据。
    
    -   当事件触发时，RC 根据当前事件数据 + 历史状态数据进行计算。
        
3.  **自主执行 (Execute)：** 计算达成条件后，RC 自动在目标 EVM 链上发起交易。
    

* * *

## 四、 四大典型应用场景

* * *

## 五、 总结：RC 带来的四大变革

-   **动态响应：** 从“请求-响应”转向“事件-驱动”。
    
-   **完全去中心化：** 消除了对第三方托管机器人和私钥管理服务的依赖。
    
-   **跨链互通：** 原生支持跨多个 EVM 兼容链的事件编排。
    
-   **高效可靠：** 减少人工干预带来的延迟和安全风险。
    

# Events 与 Callbacks

## 1\. Events（事件）

在 **Ethereum** 中，**Event** 用于让智能合约向链外传递信息。

特点：

-   写入 **transaction logs**
    
-   **不会改变区块链状态**
    
-   可以被 **dApp / 后端监听**
    
-   EVM 会对 event 进行 **索引**
    

常见用途：

-   监听 Token Transfer
    
-   监听价格变化（Oracle）
    
-   监听合约状态更新
    

* * *

## 2\. 定义与触发 Event

### 定义 Event

```
event PriceUpdated(string symbol, uint256 newPrice);
```

### 触发 Event

```
emit PriceUpdated("ETH", newEthPrice);
```

触发后：

```
Smart Contract
      ↓
Transaction Log
      ↓
dApp / Backend 监听
```

* * *

## 3\. Oracle 示例

使用 **Chainlink** 获取价格：

```
Chainlink Oracle
      ↓
Smart Contract 更新价格
      ↓
emit PriceUpdated
      ↓
dApp / Reactive Contract 监听
```

* * *

# 4\. Reactive Contracts 处理事件

Reactive Contracts 必须实现 `IReactive` 接口。

核心方法：

```
function react(LogRecord calldata log) external;
```

作用：

```
Event
   ↓
Reactive Network 监听
   ↓
调用 react(log)
   ↓
执行合约逻辑
```

* * *

## LogRecord 结构

事件信息会被打包为：

```
struct LogRecord {
    uint256 chain_id;
    address _contract;
    uint256 topic_0;
    uint256 topic_1;
    uint256 topic_2;
    uint256 topic_3;
    bytes data;
    uint256 block_number;
    uint256 block_hash;
    uint256 tx_hash;
}
```

包含：

-   来源链
    
-   事件合约
    
-   topics
    
-   数据
    
-   block / tx 信息
    

* * *

# 5\. Callback（跨链回调）

Reactive Contract 可以通过 **Callback** 在其他链执行交易。

### Callback Event

```
event Callback(
    uint256 indexed chain_id,
    address indexed _contract,
    uint64 indexed gas_limit,
    bytes payload
);
```

参数：

| 参数 | 说明 |
| --- | --- |
| chain_id | 目标链 |
| _contract | 目标合约 |
| gas_limit | gas 限制 |
| payload | ABI 编码的函数调用 |

* * *

## 触发 Callback

```
emit Callback(chain_id, contractAddr, gasLimit, payload);
```

流程：

```
Reactive Contract
      ↓ emit Callback
Reactive Network
      ↓
目标链发送交易
      ↓
调用目标合约
```

* * *

# 6\. 安全机制

Reactive Network 会自动：

-   将 **payload 第一个参数**
    
-   替换为 **ReactVM 地址**
    

所以 callback 函数：

```
第一个参数 = ReactVM address
```

* * *

# 7\. 整体流程

```
1 用户交易
2 合约 emit Event
3 Reactive Network 监听
4 调用 react()
5 emit Callback
6 跨链执行交易
```
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->






















## 睿应层（Reactive Network）与传统 EVM 的区别

### 交互模式：被动（Passive）vs 主动（Reactive）

-   **传统 EVM（被动型）：** 智能合约本质上是“死代码”。除非外部账户（EOA）发起一笔交易并支付 Gas，否则合约永远不会执行。即使价格触发了清算线，如果没有“清算人”机器人主动发起交易，合约也不会自发运行。
    
-   **Reactive Network（主动型）：** 它引入了**状态监听与自动触发**机制。合约可以“订阅”其他链上的事件。一旦预设条件达成（如某个 DeFi 协议产生了一笔大额交易），Reactive 合约会自动在目标链上执行预定义的逻辑，无需人工干预。
    

### 跨链能力：孤岛 vs 全局观察者

-   **传统 EVM：** 每个 EVM 链都是一个独立的状态机。由于“预言机问题”和跨链桥的限制，一个合约很难实时、原生且无感地获知另一条链上的状态变化。
    
-   **Reactive Network：** 它的架构设计使其充当**跨链编排层**。它可以并行监控多个链（如 Ethereum, Polygon, BSC）的日志（Logs）和事件。这使其不仅仅是一个计算引擎，更像是一个跨链的“神经中枢”。
    

### 计算架构：同步 vs 异步流水线

-   **传统 EVM：** 采用同步执行模型。所有的交易都在一个区块内按顺序打包、执行，并改变状态。这种模式限制了复杂逻辑的处理效率。
    
-   **Reactive Network：** 采用**异步反应式编程模型**。它的执行逻辑是解耦的：
    
    1.  **捕获（Capture）：** 实时捕获源链事件。
        
    2.  **反应（React）：** 在 Reactive 层进行逻辑计算。
        
    3.  **执行（Execute）：** 将结果异步反馈回目标链。 这使得它能够处理极为复杂的自动化策略，而不会阻塞主链的共识。
        

### 经济模型与 Gas 消耗

-   **传统 EVM：** 用户为单次交互支付 Gas。对于需要频繁监控的操作（如网格交易），用户必须不断运行链下机器人，并支付高昂的维护和重复交易成本。
    
-   **Reactive Network：** 通过在底层协议级别整合自动化，它优化了\*\*“条件触发式”任务\*\*的成本。它减少了对第三方中心化机器人（Keeper）的依赖，从而降低了滑点风险和MEV攻击的可能性。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
