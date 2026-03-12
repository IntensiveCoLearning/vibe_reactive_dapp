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
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
# Reactive Contract 三个模板完整总结

* * *

## 模板1：BasicDemoL1Contract（事件触发源）

solidity

````solidity
// SPDX-License-Identifier: GPL-2.0-or-later
pragma solidity >=0.8.0;

contract BasicDemoL1Contract {

    // ================================================================
    // 事件定义
    // ================================================================
    // 作用：有人转ETH进来时，广播这个事件
    // 模板3的 Reactive Contract 会监听这个事件
    event Received(
        // tx.origin: 最初发起交易的人类钱包地址
        // 例：用户的MetaMask地址 0x742d...
        // indexed: 可以被链下程序快速过滤搜索
        address indexed origin,

        // msg.sender: 直接调用本合约的地址
        // 直接转账时 = tx.origin（同一个人）
        // 通过中间合约转账时 = 中间合约地址（和origin不同）
        address indexed sender,

        // msg.value: 本次转入的ETH数量（单位：wei）
        // 1 ETH = 10^18 wei
        // 模板3会用这个值判断要不要触发回调
        uint256 indexed value
    );

    // ================================================================
    // 接收ETH的特殊函数
    // ================================================================
    // 触发条件：有人直接向本合约地址转ETH（calldata为空）
    // external: 只能从外部调用
    // payable:  允许接收ETH，没有payable就无法收款
    receive() external payable {

        // 广播事件，把三个关键信息记录到区块链日志
        // 模板3的 Reactive Contract 订阅了这个事件
        // 一旦emit，模板3的 react() 就会被自动触发
        emit Received(
            tx.origin,  // 最初发起者的钱包地址
            msg.sender, // 直接调用者地址
            msg.value   // 转入的ETH数量(wei)
        );

        // 把收到的ETH原路退回给最初发起者
        // payable(tx.origin): 把地址转换成可收款类型
        // .transfer(msg.value): 发送ETH
        payable(tx.origin).transfer(msg.value);
    }
}
```

### 模板1的职责
```
接收ETH
    → 广播 Received 事件（让模板3知道发生了什么）
    → 退回ETH
    → 工作结束，不参与后续
````

* * *

## 模板2：BasicDemoL1Callback（最终执行层）

solidity

````solidity
// SPDX-License-Identifier: GPL-2.0-or-later
pragma solidity >=0.8.0;

// AbstractCallback 提供：
// ① authorizedSenderOnly 修饰器（验证Callback Proxy）
// ② rvmIdOnly 修饰器（验证ReactVM身份）
// ③ pay() / coverDebt() 支付功能
// ④ rvm_id 变量（存储授权的ReactVM地址）
// ⑤ vendor 变量（存储Callback Proxy地址）
import '../../../lib/reactive-lib/src/abstract-base/AbstractCallback.sol';

contract BasicDemoL1Callback is AbstractCallback {

    // ================================================================
    // 事件定义
    // ================================================================
    // 作用：回调成功执行后广播，记录三个关键地址
    event CallbackReceived(
        // tx.origin: 最初触发整个流程的地址
        // 可能是用户钱包，也可能是自动化机器人地址
        address indexed origin,

        // msg.sender: 直接调用callback()的地址
        // 永远是 Callback Proxy 的地址
        // 固定值：0x0000000000000000000000000000000000fffFfF
        address indexed sender,

        // reactive_sender: 发出回调指令的 ReactVM 地址
        // 就是模板3 Reactive Contract 部署后的地址
        // 由 Reactive Network 自动填入，不可伪造
        address indexed reactive_sender
    );

    // ================================================================
    // 构造函数
    // ================================================================
    // _callback_sender: Callback Proxy 的地址
    //     → 固定值：0x0000000000000000000000000000000000fffFfF
    //     → Reactive Network 官方系统合约
    //     → 主网测试网都是这个地址，永远不变
    //
    // payable: 部署时可以存入ETH
    //     → 这些ETH用于支付回调服务费
    //     → 余额不足时回调会停止执行
    constructor(address _callback_sender) 
        AbstractCallback(_callback_sender)  // 父合约构造函数，做三件事：
                                            // ① rvm_id = msg.sender
                                            //   （记住部署者=Reactive Contract地址）
                                            // ② vendor = Callback Proxy地址
                                            //   （记住服务商，用于还债）
                                            // ③ addAuthorizedSender(Callback Proxy)
                                            //   （给Callback Proxy发白名单通行证）
        payable 
    {}

    // ================================================================
    // 核心回调函数（最终执行层）
    // ================================================================
    // 这里是整个流程的终点，真正执行操作的地方
    // 自动清算的话：清算逻辑就写在这里
    //
    // sender: 由 Reactive Network 自动填入的 ReactVM 地址
    //     → 就是模板3 Reactive Contract 部署后的地址
    //     → 用于 rvmIdOnly 验证身份
    function callback(address sender)
        external
        // 第一道验证：来自 AbstractPayer
        // 检查 msg.sender（Callback Proxy）是否在白名单
        // 只有 0x0000000000000000000000000000000000fffFfF 能通过
        authorizedSenderOnly
        // 第二道验证：来自 AbstractCallback
        // 检查 sender（ReactVM地址）是否等于 rvm_id
        // 只有部署此合约的那个 Reactive Contract 能通过
        rvmIdOnly(sender)
    {
        // 回调成功，广播事件记录三个地址
        emit CallbackReceived(
            tx.origin,  // 最初触发者地址（用户/机器人钱包）
            msg.sender, // Callback Proxy地址（0x0000...fffFfF）
            sender      // Reactive Contract地址（模板3的地址）
        );

        // ↑ 自动清算合约中，这里替换成：
        // → 检查仓位抵押率
        // → 没收抵押品
        // → 发放清算奖励
        // → 更新仓位状态
    }
}
```

### 模板2的职责
```
接收来自模板3的回调指令
    → 第一道验证：Callback Proxy 是官方的吗？
    → 第二道验证：指令来自授权的 Reactive Contract 吗？
    → 两道验证通过 → 执行真正的操作
    → 这里是整个流程的终点
````

* * *

## 模板3：BasicDemoReactiveContract（监听+决策层）

solidity

````solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity >=0.8.0;

// IReactive 提供：
// → react() 函数接口（必须实现）
// → Callback 事件定义（用于触发回调）
// → LogRecord 结构体定义（接收事件数据）
import '../../../lib/reactive-lib/src/interfaces/IReactive.sol';

// AbstractReactive 提供：
// → vm 变量（判断是否在ReactVM环境）
// → vmOnly 修饰器（只在ReactVM里执行）
// → rnOnly 修饰器（只在Reactive Network里执行）
// → service 变量（系统合约，用于订阅事件）
// → REACTIVE_IGNORE 常量（订阅时忽略某个topic）
import '../../../lib/reactive-lib/src/abstract-base/AbstractReactive.sol';

// ISystemContract 提供：
// → subscribe() 接口（订阅事件）
// → unsubscribe() 接口（取消订阅）
import '../../../lib/reactive-lib/src/interfaces/ISystemContract.sol';

contract BasicDemoReactiveContract is IReactive, AbstractReactive {

    // ================================================================
    // 状态变量
    // ================================================================

    uint256 public originChainId;       // 监听哪条链的事件
                                        // 例：11155111 = Ethereum Sepolia

    uint256 public destinationChainId;  // 回调发送到哪条链
                                        // 例：11155111 = 同一条链

    // 回调最多消耗的gas上限，固定值
    // 太低 → 回调执行失败
    // 太高 → 浪费钱
    uint64 private constant GAS_LIMIT = 1000000;

    // 回调目标合约地址
    // 就是模板2 BasicDemoL1Callback 部署后的地址
    // 由部署者传入，部署后不变
    address private callback;

    // ================================================================
    // 构造函数
    // ================================================================
    constructor(
        // _service: Reactive Network 官方系统合约地址
        //     → 固定值：0x0000000000000000000000000000000000fffFfF
        //     → 主网测试网永远不变
        //     → 用于调用 subscribe() 订阅事件
        address _service,

        // _originChainId: 要监听的源链ID
        //     → 不固定，根据需求变化
        //     → 例：1=ETH主网, 11155111=Sepolia测试网
        uint256 _originChainId,

        // _destinationChainId: 回调目标链ID
        //     → 不固定，根据需求变化
        //     → 可以和originChainId相同（同链）
        //     → 也可以不同（跨链操作）
        uint256 _destinationChainId,

        // _contract: 要监听的目标合约地址
        //     → 不固定，根据监听目标变化
        //     → 就是模板1 BasicDemoL1Contract 部署后的地址
        //     → 自动清算时填借贷协议合约地址
        address _contract,

        // _topic_0: 要监听的事件签名哈希
        //     → 不固定，根据监听事件类型变化
        //     → 例：keccak256("Received(address,address,uint256)")
        //     → 自动清算时填价格更新事件的哈希
        uint256 _topic_0,

        // _callback: 回调目标合约地址
        //     → 不固定，每次重新部署模板2后都会变
        //     → 就是模板2 BasicDemoL1Callback 部署后的地址
        //     → 自动清算时填清算执行合约地址
        address _callback

    ) payable {
        // 系统合约地址包装成 ISystemContract 接口类型
        // payable: 系统合约需要能收ETH（支付服务费用）
        service = ISystemContract(payable(_service));

        originChainId      = _originChainId;
        destinationChainId = _destinationChainId;

        // 记住回调目标（模板2的地址）
        callback = _callback;

        // if(!vm): 只在 Reactive Network 主网环境执行订阅
        //          在 ReactVM 环境里跳过（ReactVM里无法调用subscribe）
        if (!vm) {
            service.subscribe(
                originChainId,   // 监听哪条链
                _contract,       // 监听哪个合约（模板1的地址）
                _topic_0,        // 监听哪种事件（事件签名哈希）
                REACTIVE_IGNORE, // topic_1: 不过滤，任意值都接受
                REACTIVE_IGNORE, // topic_2: 不过滤，任意值都接受
                REACTIVE_IGNORE  // topic_3: 不过滤，任意值都接受
            );
        }
    }

    // ================================================================
    // 核心监听函数
    // ================================================================
    // 每次监听到订阅的事件，Reactive Network 自动调用此函数
    // vmOnly: 只能在 ReactVM 环境里执行
    //         防止任意人手动调用
    function react(LogRecord calldata log) external vmOnly {

        // log.topic_3: 对应模板1事件里的 value（转入ETH数量）
        // 条件：只有转入金额 >= 0.001 ETH 才触发回调
        // 自动清算时改成：if(抵押率 < 150%) 触发清算
        if (log.topic_3 >= 0.001 ether) {

            // 构造回调数据：告诉L1合约执行哪个函数
            // "callback(address)": 模板2里的函数名+参数类型
            // address(0): 占位符！
            //     → Reactive Network 会自动替换成 ReactVM 地址
            //     → 也就是本合约（模板3）的地址
            //     → L1合约用这个地址做 rvmIdOnly 验证
            bytes memory payload = abi.encodeWithSignature(
                "callback(address)",
                address(0)  // 占位符，自动被替换成本合约地址
            );

            // 发出回调事件，Reactive Network 检测到后自动执行
            emit Callback(
                destinationChainId, // 去哪条链执行回调
                callback,           // 调用哪个合约（模板2的地址）
                GAS_LIMIT,          // 给多少gas（固定100万）
                payload             // 调用什么函数+参数
            );
        }
    }
}
```

### 模板3的职责
```
订阅模板1发出的事件
    → 每次事件发生，react() 自动被调用
    → 判断条件是否满足
    → 满足 → emit Callback → 通知模板2执行操作
    → 不满足 → 什么都不做，继续等待
```

---

## 所有地址汇总表

| 地址 | 是谁的 | 是否固定 | 出现在哪里 |
|------|-------|---------|----------|
| `0x0000...fffFfF` | Reactive Network 官方系统合约 | ✅ 永远固定 | 模板2的`_callback_sender`、模板3的`_service` |
| 模板1的地址 | 你部署的事件触发合约 | ❌ 每次部署都变 | 模板3构造函数的`_contract` |
| 模板2的地址 | 你部署的回调执行合约 | ❌ 每次部署都变 | 模板3构造函数的`_callback`、模板3的`callback`变量 |
| 模板3的地址 | 你部署的Reactive Contract | ❌ 每次部署都变 | 模板2的`rvm_id`（自动设置）、回调时的`sender`参数 |
| `tx.origin` | 最初发起交易的人类钱包 | ❌ 每次交易都不同 | 模板1和模板2的事件里 |
| `msg.sender` | 直接调用者 | ❌ 根据调用者变化 | 各合约内部验证用 |

---

## 三个模板完整流程图
```
用户钱包（tx.origin）
    │
    │ 转入 >= 0.001 ETH
    ▼
┌─────────────────────────────┐
│  模板1 BasicDemoL1Contract  │  ← 部署在 Ethereum L1
│                             │
│  receive() 触发             │
│  emit Received(             │
│      origin,  ← 用户钱包   │
│      sender,  ← 用户钱包   │
│      value    ← ETH数量    │
│  )                          │
│  退回ETH给用户              │
└─────────────┬───────────────┘
              │ Reactive Network 检测到事件
              ▼
┌─────────────────────────────────────┐
│  模板3 BasicDemoReactiveContract    │  ← 部署在 Reactive Network
│                                     │
│  react(log) 自动触发                │
│  log.topic_3 >= 0.001 ether？是！   │
│                                     │
│  emit Callback(                     │
│      destinationChainId, ← 目标链  │
│      callback,  ← 模板2地址        │
│      GAS_LIMIT, ← 100万gas         │
│      payload    ← callback(模板3地址)│
│  )                                  │
└─────────────────┬───────────────────┘
                  │ Reactive Network 执行回调
                  │ 通过 Callback Proxy (0x0000...fffFfF)
                  ▼
┌─────────────────────────────────────┐
│  模板2 BasicDemoL1Callback          │  ← 部署在 Ethereum L1
│                                     │
│  callback(sender) 触发              │
│  msg.sender = 0x0000...fffFfF ✓    │  ← authorizedSenderOnly
│  sender == rvm_id ✓                 │  ← rvmIdOnly
│                                     │
│  emit CallbackReceived(             │
│      tx.origin, ← 最初触发者       │
│      msg.sender,← Callback Proxy   │
│      sender     ← 模板3地址        │
│  )                                  │
│                                     │
│  ← 自动清算逻辑写在这里！           │
└─────────────────────────────────────┘
```

---

## 下一步：改造成自动清算合约需要修改的地方
```
模板1 改造方向：
→ 改成借贷协议合约
→ 用户存入抵押品，记录仓位
→ 价格更新时 emit 价格事件（让模板3监听）

模板3 改造方向：
→ react() 里的条件改成：
   if (计算出的抵押率 < 150%) → 触发清算回调

模板2 改造方向：
→ callback() 里写具体清算逻辑：
   → 验证仓位确实低于清算线
   → 没收抵押品
   → 发放清算奖励给清算人
   → 关闭仓位
````

## 1️⃣ 系统架构概览

**目标：事件驱动自动化执行**

```
Origin Chain Event
        ↓
  Reactive Network
        ↓
      ReactVM
        ↓
     Callback → Destination Chain
```

-   **Origin Chain**：事件源，例如 ETH、BNB、Polygon
    
-   **Reactive Network**：跨链事件监听 + 调度层
    
-   **ReactVM**：自动执行逻辑的虚拟机
    
-   **Callback**：执行交易或操作的最终落地
    

**核心理念：**

> “事件发生 → 自动判断 → 自动执行交易”

* * *

## 2️⃣ Reactive Network 核心功能

**作用：跨链事件自动化层**

1.  **监听事件**
    
    -   支持多链：ETH、BNB、Polygon 等
        
    -   事件类型：Transfer、Swap、Liquidation 等
        
2.  **管理订阅**
    
    -   Reactive Contract 可注册感兴趣事件：
        
        ```
        subscribe(chain, contract, topic0);
        ```
        
    -   网络负责匹配事件并触发执行
        
3.  **调度执行**
    
    -   匹配事件后派发给 ReactVM
        
    -   相当于：
        
        ```
        Reactive Network = Event Router + Task Scheduler
        ```
        

* * *

## 3️⃣ ReactVM

**作用：执行 react() 函数的沙盒环境**

-   **工作内容**
    
    -   解析事件 log
        
    -   判断条件
        
    -   触发 callback
        
-   **特点**
    
    -   独立 VM，权限和状态隔离
        
    -   出错不会影响网络或其他合约
        
-   **性能**
    
    -   react() 执行 ≈ 0.1 ms — 2 ms（几乎可忽略）
        

* * *

## 4️⃣ 双状态架构

Reactive Contract 实际存在两个实例：

| 实例 | 作用 |
| --- | --- |
| Reactive Network instance | 管理订阅、事件调度 |
| ReactVM instance | 执行 react() 逻辑 |

> 这就是 **Dual State Architecture**，确保事件监听和逻辑执行安全分离。

* * *

## 5️⃣ react() 执行流程

```
事件发生
↓
Reactive Network 捕获
↓
ReactVM 执行 react()
    ├─解析 log
    ├─判断条件
    └─emit callback
↓
Destination Chain 执行交易
```

-   ReactVM 只处理逻辑，不直接持有资金
    
-   资金操作通过 callback 完成
    

* * *

## 6️⃣ 7 秒延迟的原因

-   **Reactive Network 出块时间 ≈7s**
    
-   原因：
    
    1.  防止链重组（确认事件可靠）
        
    2.  跨链事件同步（ETH/BNB/Polygon 等）
        
    3.  共识稳定性（共识层参数）
        

> react() 执行时间 ≈1 ms，远小于网络延迟

* * *

## 7️⃣ 安全隔离与资金风险

-   ReactVM **保护**：
    
    -   Reactive Network 系统
        
    -   网络稳定性
        
    -   其他合约状态
        
-   ReactVM **不保护**：
    
    -   用户资金
        
    -   交易逻辑错误仍可能导致损失
        
-   **资金风险来源**：
    
    -   Callback 执行交易逻辑错误
        
    -   滑点过大、重复触发、价格滞后、交易金额过大
        

* * *

## 8️⃣ react() 防护设计

开发建议：

1.  **状态锁**
    
    ```
    bool executed;
    ```
    
2.  **滑点保护**
    
    ```
    require(amountOut >= minAmount);
    ```
    
3.  **多条件判断**
    
    ```
    if(price < threshold && liquidity > minLiquidity)
    ```
    
4.  **交易规模限制**
    
    ```
    uint maxTrade;
    ```
    

> 保障策略逻辑安全，避免资金损失
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->

# 使用AI演示和分析了reactive contract的工作原理，图片和简单动画演示

[Reactive\_Contract步骤分析](https://may-tonk.github.io/html_may_tonk_web/reactive_contract_image.html/second_reactiveframe.html)

[Reactive\_Contract简单动画演示](https://may-tonk.github.io/html_may_tonk_web/reactive_contract_image.html/third_reactive_fame.html)

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
