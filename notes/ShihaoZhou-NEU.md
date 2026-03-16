---
timezone: UTC+8
---

# David Zhou

**GitHub ID:** ShihaoZhou-NEU

**Telegram:**

## Self-introduction

Reactive 探索者

## Notes

<!-- Content_START -->
# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->
今天看一下 [https://dev.reactive.network/hyperlane](https://dev.reactive.network/hyperlane)
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->

# **Lesson 6: How Uniswap Works / Understanding Uniswap V2 Pools and Smart Contracts**

X \* y = k

# **Lesson 7: Implementing Basic Reactive Functions**

## **Key Components 关键组成部分**

### **Event Declarations 事件声明**

事件声明：诸如 `Subscribed` 、 `VM` 、 `AboveThreshold` 、 `CallbackSent` 和 `Done` 之类的事件用于记录和跟踪合约在区块链上的操作。

### **Contract Variables 合同变量**

`UNISWAP_V2_SYNC_TOPIC_0` 和 `STOP_ORDER_STOP_TOPIC_0` 分别是代表 Uniswap `Sync` 事件和合约 `Stop` `done` `triggered` `pair` 、 `stop_order` 、 `client` 、 `token0` 、 `coefficient` 和 `threshold` 等变量存储停止订单 `CALLBACK_GAS_LIMIT` 状态和配置。

-   **初始化** ：部署后，合约会订阅来自 Uniswap V2 交易对和止损单回调合约的必要事件。
    
-   **事件监控** ：合约监听来自 Uniswap 交易对的同步事件，以监控资金池的储备金变化；监听来自止损单合约的 `Stop` 事件，以跟踪订单的执行情况。
    
-   **止损单激活** ：当 `Sync` 事件指示资金池价格达到阈值时，合约通过回调函数发起止损单，在 Uniswap V2 上执行交易。
    
-   **完成** ：停止指令执行后，合约会捕获停止指令合约中的停止事件，并将该过程标记为已完成。
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->


# **Lesson 5: How Oracles Work**

**尝试使用漫画方式来理解**

![Reactive_Oracle_Magic_page-0001.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/ShihaoZhou-NEU/images/2026-03-14-1773478063194-Reactive_Oracle_Magic_page-0001.jpg)![Reactive_Oracle_Magic_page-0002.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/ShihaoZhou-NEU/images/2026-03-14-1773478077675-Reactive_Oracle_Magic_page-0002.jpg)![Reactive_Oracle_Magic_page-0003.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/ShihaoZhou-NEU/images/2026-03-14-1773478115346-Reactive_Oracle_Magic_page-0003.jpg)![Reactive_Oracle_Magic_page-0004.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/ShihaoZhou-NEU/images/2026-03-14-1773478131288-Reactive_Oracle_Magic_page-0004.jpg)![Reactive_Oracle_Magic_page-0005.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/ShihaoZhou-NEU/images/2026-03-14-1773478141042-Reactive_Oracle_Magic_page-0005.jpg)![Reactive_Oracle_Magic_page-0006.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/ShihaoZhou-NEU/images/2026-03-14-1773478151883-Reactive_Oracle_Magic_page-0006.jpg)![Reactive_Oracle_Magic_page-0007.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/ShihaoZhou-NEU/images/2026-03-14-1773478162281-Reactive_Oracle_Magic_page-0007.jpg)![Reactive_Oracle_Magic_page-0008.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/ShihaoZhou-NEU/images/2026-03-14-1773478207133-Reactive_Oracle_Magic_page-0008.jpg)![Reactive_Oracle_Magic_page-0009.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/ShihaoZhou-NEU/images/2026-03-14-1773478218106-Reactive_Oracle_Magic_page-0009.jpg)![Reactive_Oracle_Magic_page-0010.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/ShihaoZhou-NEU/images/2026-03-14-1773478227876-Reactive_Oracle_Magic_page-0010.jpg)![Reactive_Oracle_Magic_page-0011.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/ShihaoZhou-NEU/images/2026-03-14-1773478237840-Reactive_Oracle_Magic_page-0011.jpg)![Reactive_Oracle_Magic_page-0012.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/ShihaoZhou-NEU/images/2026-03-14-1773478247370-Reactive_Oracle_Magic_page-0012.jpg)![Reactive_Oracle_Magic_page-0013.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/ShihaoZhou-NEU/images/2026-03-14-1773478258817-Reactive_Oracle_Magic_page-0013.jpg)
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->



# **Lesson 4: How Subscriptions Work**

-   静态和动态地配置和管理订阅。
    
-   在智能合约中处理订阅和取消订阅事件。
    
-   了解在响应式合约中使用订阅的局限性和最佳实践。
    

## **如何实现订阅**

订阅是通过响应式网络系统合约中的 `subscribe` 方法设置的

响应式合约还必须处理由于在响应式网络（具有系统合约）和其部署者的私有 ReactVM（其中不存在系统合约）上部署而导致的回滚。

### **ISubscriptionService Interface**

```
 pragma solidity >=0.8.0;
 ​
 import './IPayable.sol';
 ​
 interface ISubscriptionService is IPayable {
     function subscribe(
         uint256 chain_id,
         address _contract,
         uint256 topic_0,
         uint256 topic_1,
         uint256 topic_2,
         uint256 topic_3
     ) external;
     
     function unsubscribe(
         uint256 chain_id,
         address _contract,
         uint256 topic_0,
         uint256 topic_1,
         uint256 topic_2,
         uint256 topic_3
     ) external;
 }
```

Unsubscribing is an expensive operation due to the necessity of searching and removing subscriptions. Duplicate or overlapping subscriptions are allowed, but clients must ensure idempotency.

### **IReactive Interface**

[IReactive](https://github.com/Reactive-Network/reactive-lib/blob/main/src/interfaces/IReactive.sol) 接口定义了响应式合约的标准，这些合约可以接收和处理与其订阅匹配的事件通知。它扩展了 [IPayer](https://github.com/Reactive-Network/reactive-lib/blob/main/src/interfaces/IPayer.sol) 接口，表明它包含了与支付相关的功能。

```
 pragma solidity >=0.8.0;
 ​
 import './IPayer.sol';
 ​
 interface IReactive is IPayer {
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
 ​
     event Callback(
         uint256 indexed chain_id,
         address indexed _contract,
         uint64 indexed gas_limit,
         bytes payload
     );
     
     function react(LogRecord calldata log) external;
 }
```

### **Constructor Subscribtion**

```
 // State specific to reactive network instance of the contract
 address private _callback;
 ​
 // State specific to ReactVM instance of the contract
 uint256 public counter;
 ​
 constructor(
         address _service,
         address _contract,
         uint256 topic_0,
         address callback
     ) payable {
         service = ISystemContract(payable(_service));
         if (!vm) {
             service.subscribe(
                 CHAIN_ID,
                 _contract,
                 topic_0,
                 REACTIVE_IGNORE,
                 REACTIVE_IGNORE,
                 REACTIVE_IGNORE
             );
         }
         _callback = callback;
     }
```

### **Subscription Criteria 订阅标准**

-   通配符用法：使用 `address(0)` 表示按任何合约地址过滤， `uint256(0)` 表示按任何链 ID 过滤，使用 `REACTIVE_IGNORE` 表示按任何主题过滤。
    
-   Concrete Values: At least one criterion must be a specific value to ensure meaningful subscriptions.
    

## **Dynamic Subscriptions 动态订阅**

响应式合约可以根据传入事件动态管理其订阅。由于负责管理订阅的系统合约只能从响应式网络访问，因此 ReactVM 的合约副本会处理这些操作，并使用回调与响应式网络通信。
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->




# **Lesson 3: ReactVM and Reactive Network As a Dual-State Environment**

响应式合约的另一个关键特性：它们在响应式网络和 ReactVM 中以两个具有独立状态的实例存在。

## **Differences Between the Reactive Network and ReactVM**

![reactvm-17a16c9fc446c26761db5e2e9619a585](https://dev.reactive.network/assets/images/reactvm-17a16c9fc446c26761db5e2e9619a585.jpg)

每个响应式合约都有两个实例——一个位于响应式网络上，另一个位于其独立的 ReactVM 中。需要注意的是，这两个实例都实际存储在每个网络节点上并由其执行。并行化响应式合约是一种架构决策，旨在确保即使处理大量事件也能保持高性能。

Reactive Network 的运行方式与典型的 EVM 区块链类似，但它增加了系统合约，允许用户订阅和取消订阅以太坊、BNB、Polygon 或 Optimism 上的源链事件。每个部署地址都拥有一个专用的 ReactVM。

ReactVM 是一个受限虚拟机，旨在隔离地处理事件。从一个地址部署的合约在同一个 ReactVM 中执行。它们可以相互交互，但不能与响应式网络上的其他合约交互。

单个 ReactVM 中的合约可以通过两种方式与外部世界交互，这两种方式都通过响应式网络实现：

-   它们会对订阅的特定事件做出反应，并在这些事件发生时执行。
    
-   ReactVM 根据事件输入执行代码，向响应式网络发送请求，以获取回调到目标链，从而执行相应的链上操作。
    

对于每个已部署的 RC，都会有两个实例，它们处于不同的状态，但代码相同。每个方法都预期在一个或两个环境中执行，并与一个或两个状态进行交互。

## **Identifying the Execution Context**

### **如何检测**

```
function detectVm() internal {
```

```
    uint256 size;
```

```
    // solhint-disable-next-line no-inline-assembly
```

```
    assembly { size := extcodesize(0x0000000000000000000000000000000000fffFfF) }
```

```
    vm = size == 0;
```

```
}
```

**设置 vm 标志** ：如果 `size == 0` ，则表示该地址处没有代码。这表明我们正在 ReactVM 实例中运行，因此 `vm` 设置为 `true` 。否则，如果 `size > 0` ，则表示存在系统合约，确认我们正在响应式网络上，因此 `vm` 保持为 `false` 。

**Enforcing Execution Context**

使用修饰符来确保每个函数只能在其预期的环境中调用。

```
modifier rnOnly() {
```

```
    require(!vm, 'Reactive Network only');
```

```
    _;
```

```
}
```

```
​
```

```
modifier vmOnly() {
```

```
    require(vm, 'VM only');
```

```
    _;
```

```
}
```

## **Managing Dual Variable Sets for Each State**

在响应式架构中，每个已部署的合约都可以在两种运行状态下运行：

### **Reactive Network State**

-   直接与系统合约交互。
    
-   使用 `service.subscribe(...)` 订阅事件。
    
-   使用注册和管理活动订阅所需的变量和方法。
    

### **ReactVM State**

-   包含对已订阅事件做出反应的业务逻辑。
    
-   使用在接收到事件时执行的变量和方法。
    

## **Reactive Network Variables**

如果一个响应式契约继承自 `AbstractReactive` ，则在后台可以使用以下变量和方法：

-   用于订阅事件的 `service` （ `ISubscriptionService` ）。
    
-   （布尔值），指示执行是在 ReactVM 上还是在 Reactive Network 上下文中发生。
    
-   额外的继承实用方法（例如， `service.subscribe(...)` ）。
    

在 [Uniswap Stop Order 响应式合约](https://github.com/Reactive-Network/reactive-smart-contract-demos/blob/main/src/demos/uniswap-v2-stop-order/UniswapDemoStopOrderReactive.sol)的构造函数中，您可以注意到 `if (!vm)` 会检查我们是否运行在响应式网络 (Reactive Network) 状态下。如果是，合约会注册接收来自 `pair` 和 `stop_order` 事件。订阅后，这些事件只有在我们处于 ReactVM 状态时才会触发我们的 `react()` 逻辑。

```
// State specific to ReactVM instance of the contract.
```

```
​
```

```
bool private triggered;
```

```
bool private done;
```

```
address private pair;
```

```
address private stop_order;
```

```
address private client;
```

```
bool private token0;
```

```
uint256 private coefficient;
```

```
uint256 private threshold;
```

```
​
```

```
constructor(
```

```
    address _pair,
```

```
    address _stop_order,
```

```
    address _client,
```

```
    bool _token0,
```

```
    uint256 _coefficient,
```

```
    uint256 _threshold
```

```
) payable {
```

```
    triggered = false;
```

```
    done = false;
```

```
    pair = _pair;
```

```
    stop_order = _stop_order;
```

```
    client = _client;
```

```
    token0 = _token0;
```

```
    coefficient = _coefficient;
```

```
    threshold = _threshold;
```

```
​
```

```
    if (!vm) {
```

```
        service.subscribe(
```

```
            SEPOLIA_CHAIN_ID,
```

```
            pair,
```

```
            UNISWAP_V2_SYNC_TOPIC_0,
```

```
            REACTIVE_IGNORE,
```

```
            REACTIVE_IGNORE,
```

```
            REACTIVE_IGNORE
```

```
        );
```

```
        service.subscribe(
```

```
            SEPOLIA_CHAIN_ID,
```

```
            stop_order,
```

```
            STOP_ORDER_STOP_TOPIC_0,
```

```
            REACTIVE_IGNORE,
```

```
            REACTIVE_IGNORE,
```

```
            REACTIVE_IGNORE
```

```
        );
```

```
    }
```

```
}
```

## **ReactVM Variables**

以下变量和方法专门用于在接收到事件之后：

```
bool private triggered;
```

```
bool private done;
```

```
address private pair;
```

```
address private stop_order;
```

```
address private client;
```

```
bool private token0;
```

```
uint256 private coefficient;
```

```
uint256 private threshold;
```

这些变量处理 `react()` 函数中的逻辑：

```
// Methods specific to ReactVM instance of the contract.
```

```
function react(LogRecord calldata log) external vmOnly {
```

```
    assert(!done);
```

```
​
```

```
    if (log._contract == stop_order) {
```

```
        if (
```

```
            triggered &&
```

```
            log.topic_0 == STOP_ORDER_STOP_TOPIC_0 &&
```

```
            log.topic_1 == uint256(uint160(pair)) &&
```

```
            log.topic_2 == uint256(uint160(client))
```

```
        ) {
```

```
            done = true;
```

```
            emit Done();
```

```
        }
```

```
    } else {
```

```
        Reserves memory sync = abi.decode(log.data, ( Reserves ));
```

```
        if (below_threshold(sync) && !triggered) {
```

```
            emit CallbackSent();
```

```
            bytes memory payload = abi.encodeWithSignature(
```

```
                "stop(address,address,address,bool,uint256,uint256)",
```

```
                address(0),
```

```
                pair,
```

```
                client,
```

```
                token0,
```

```
                coefficient,
```

```
                threshold
```

```
            );
```

```
            triggered = true;
```

```
            emit Callback(log.chain_id, stop_order, CALLBACK_GAS_LIMIT, payload);
```

```
        }
```

```
    }
```

```
}
```

-   一旦满足阈值条件， `triggered` 将阻止多次回调。
    
-   表示最终停止已经发生。
    
-   `pair` 、 `stop_order` 和 `client` 引用外部合约和用户数据。
    
-   `token0` 、 `coefficient` 和 `threshold` 定义了何时触发停止的数学原理。
    

实际逻辑（检查流动性储备和发出回调）位于 ReactVM 内部。由于 `react()` `被标记为`vmOnly\` ，因此它**仅**在 ReactVM 上下文中，且事件日志匹配时才会被底层系统调用。

## **Transaction Execution**

在使用响应式合约 (RC) 时，交易主要发生在两个环境中：响应式网络和 ReactVM。

### **Reactive Network Transactions**

反应式网络上的交易可以通过两种方式发起：直接由用户发起，或由源链上的事件触发。

**用户发起的交易**

用户可以调用响应式网络 (Reactive Network) 的 RC 实例上的方法来执行管理功能或更新合约状态。例如，暂停事件订阅可以通过调用 `pause()` 函数来实现：

**事件触发交易**

即使用户没有直接发起交易，响应式网络也会监控源链上的事件。当出现感兴趣的事件时，响应式网络会将其分发给所有活跃的订阅者，通常是专门的 ReactVM 实例。这种分发会触发订阅者执行进一步的操作或状态更改。

### **ReactVM Transactions**

在 ReactVM 中，用户不能直接调用事务。相反，当响应式网络从源链转发相关事件时，事务会自动触发：

-   源链上发出的事件
    
-   反应网络调度事件
    
-   ReactVM 接收并处理事件
    

当运行在 ReactVM 中的 RC 接收到事件时，它通常会调用其核心反应函数 `react()` 来处理该事件。`react` react()\` 函数包含以下业务逻辑：

-   根据接收到的事件更新内部状态。
    
-   向目标链发出回调，然后可以触发这些链上的交易。
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->





# **Lesson 2: How Events and Callbacks Work**

本课重点讲解事件和回调在智能合约中的作用。通过学习如何发出、处理和监听事件，开发者可以创建能够实时响应区块链变化的动态去中心化应用（dApp）。我们还将探讨响应式合约如何使用 `react()` 方法处理事件，并通过回调发起跨链交易，从而增强响应式网络的功能。

## **EVM 事件如何运作**

当智能合约发出事件时，事件数据会存储在交易日志中。这些日志附加到区块链的区块上，但不会直接影响区块链的状态。相反，它们提供了一种基于事件参数记录和检索信息的方法。

开发者在智能合约中使用 `event` 关键字定义事件，后跟事件名称和要记录的信息的数据类型。要发出事件，智能合约使用 `emit` 关键字，后跟事件名称和要记录的数据。

外部应用程序（例如去中心化应用或后端服务）可以监听这些事件。通过指定事件签名以及（可选的）过滤参数，这些应用程序可以在事件发生时订阅实时更新。这种机制对于创建响应迅速且交互性强的区块链应用程序至关重要。

```
 pragma solidity >=0.8.0;
 ​
 import './IPayer.sol';
 ​
 interface IReactive is IPayer {
     struct LogRecord { // 定义了一种结构化数据类型 LogRecord ，用于包含有关事件日志的详细信息：
         uint256 chain_id; // 事件发生的区块链的 ID
         address _contract; // 发出事件的合约地址
         uint256 topic_0; // 日志的索引主题
         uint256 topic_1;
         uint256 topic_2;
         uint256 topic_3;
         bytes data; // 事件日志中未建立索引的数据
         uint256 block_number; // 事件发生的区块编号
         uint256 op_code; // Potentially denotes an operation code.
         uint256 block_hash; // 用于追踪事件来源和上下文的附加标识符。
         uint256 tx_hash; // 用于追踪事件来源和上下文的附加标识符。
         uint256 log_index; // 用于追踪事件来源和上下文的附加标识符。
     }
 ​
     event Callback( // 回调事件 ：用于通知订阅者特定事件发生的事件：
         uint256 indexed chain_id, // 事件的区块链 ID
         address indexed _contract, // 发出合约的地址
         uint64 indexed gas_limit, // 回调函数分配的最大 gas 量
         bytes payload // 随回调一起返回的编码数据
     );
     
     function react(LogRecord calldata log) external; // 处理传入事件通知的关键函数
 }
```

响应式网络持续监控事件日志，并将其与响应式合约中定义的订阅条件进行匹配。当检测到符合条件的事件时，网络会触发 `react()` 方法，并传递相关详细信息。

响应式合约可以访问所有标准的 EVM 功能。但是，它们运行在私有的 ReactVM 环境中，这限制了它们只能与同一部署者部署的合约进行交互。这种隔离机制确保响应式合约在处理来自响应式网络的事件时，能够维持一个受控且安全的环境。

## **Callbacks to Destination Chains**

响应式合约可以通过以特定格式发出日志记录，在目标链上发起交易。这些记录会被响应式网络接收，然后响应式网络会在相关的链上执行所需的交易。

### **Emitting Callback Events 发出回调事件**

event Callback( // 回调事件 ：用于通知订阅者特定事件发生的事件： <- 这个事件被 emit

### **Processing the Callback 处理回调**

当 `Callback` 事件发出时，响应式网络会检测到该事件并处理 `payload` ，该有效载荷以特定格式编码交易详情。然后，响应式网络使用提供的 `chain_id` 和 `gas_limit` 将交易提交到目标链上指定的合约。

### **Important Note on Authorization**

For security and authorization purposes, the Reactive Network automatically replaces the first 160 bits of the call arguments within the `payload` with the RVM ID (equivalent to the ReactVM address) of the calling reactive contract. This RVM ID is identical to the contract deployer's address. As a result, the first argument in your callback will always be the ReactVM address (of type `address`), regardless of the variable name you use in your Solidity code.

### **Encoding and Emitting the Callback Event**

要在目标链上发起操作，您可以将交易详情编码到 `payload` 中并发出 `Callback` 事件。例如，在 Uniswap 止损单演示中，此过程用于通过目标链合约触发代币销售：

```
 bytes memory payload = abi.encodeWithSignature(
    "stop(address,address,address,bool,uint256,uint256)",
    address(0),  // The ReactVM address
    pair,        // The Uniswap pair address involved in the transaction
    client,      // The address of the client initiating the stop order
    token0,      // The address of the first token in the pair
    coefficient, // A coefficient determining the sale price
    threshold    // The price threshold at which the sale should occur
 );
 emit Callback(chain_id, stop_order, CALLBACK_GAS_LIMIT, payload);
```
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->






# 学习计划

1.  学习：[https://dev.reactive.network/education/module-1/reactive-contracts](https://dev.reactive.network/education/module-1/reactive-contracts)
    
2.  看一下 Workshop 视频
    

# **Lesson 1: Reactive Contracts**

## **RCs 与传统合约有何不同**

一个被动一个主动，RCs 接收到链上事件的信号，自主执行预定义的区块链操作

## **Inversion of Control 控制反转**

RCs 的关键概念是控制反转（IoC）。

传统 -> EOA 用户或机器人发起。

RC -> 根据预定义事件的发生自主决定何时执行。

如果没有响应式合约，需要设置一个独立的实体（例如机器人）来监控区块链，并使用现有的、很可能是中心化的数据解决方案。该机器人将持有管理资金的私钥，并通过其 EOA 地址在 EVM 链上发起交易。虽然此类系统确实有用，但对于某些用例来说可能并非最佳选择，而对于另一些用例则完全不适用。

控制反转使我们能够避免托管模拟人类签署交易的额外实体。如果您预先定义了一个场景，概述了链上事件发生后的交易序列，那么您应该能够以完全去中心化的方式运行此逻辑，因为您的输入和输出都保留在区块链上。响应式网络赋予智能合约一项它们从一开始就缺失的特性——无需人（或机器人）签署交易，仅基于其他链上事件即可自动执行。

## **What Happens Inside a Reactive Contract 反应式合约内部发生了什么**

1.  指定链，合约，事件。
    
2.  持续监控，当检测到指定事件的时候启动执行（这些事件可以包括简单的货币或代币转账、去中心化交易所 (DEX) 兑换、贷款、闪电贷、投票、巨鲸操作或任何其他智能合约活动）。
    
    1.  一旦检测到感兴趣的事件，响应式网络会自动执行您在响应式合约中实现的逻辑。这可能涉及基于事件数据进行计算。响应式合约是有状态的，这意味着它们拥有一个状态，用于存储和更新值。您可以随着时间的推移在状态中积累数据，然后在历史数据和新的区块链事件的组合满足指定条件时执行操作。
        
    2.  事件发生后，RC 会更新其状态，保持最新状态，并可在 EVM 区块链上发起交易。整个过程在响应式网络内以无需信任的方式运行，确保自动、快速且可靠地执行。
        

## **用例**

### **从多个预言机收集数据**

为了使响应能力能够应对更广泛的事件，包括链下事件，RC 会与预言机集成。预言机是第三方服务，可将可信的外部数据输入区块链。

RC 可以监控来自不同智能合约、兼容 EVM 的区块链的数据，它可以整合来自多个预言机的数据，从而获得更精确、更去中心化的信息。在这种情况下，RC 监控的事件是相应预言机更新后的事件。RC 内部的计算将涉及整合来自不同预言机的数据（例如，取平均值）。最终结果可能是一个基于篮球比赛结果的无需信任的支付。

### **止损单**

区块链上另一个可靠的数据源是交易池，例如 Uniswap 交易池。它甚至比预言机更可靠，因为它完全由链上数据构成，不依赖第三方。

在这种架构下，一个响应式合约会监控指定 UniSwap 资金池中的互换交易，计算流动性和汇率。当汇率达到预设价格时，响应式合约会执行互换交易，从而在现有去中心化交易所（DEX）的基础上实现无需信任的止损单。

### **DEX 套利**

响应式合约将监控多个不同的资金池，寻找价格差异并从中获利。单链和跨链两种方式都可行。单链方式可以使用闪电贷；跨链方式则需要多条链的流动性，但可以获得更多的套利机会。

这种解决方案的优势在于它的去中心化特性，这与传统的机器人方法截然不同。

### **Pools Rebalancing 资金池重新平衡**

从头就设计响应式的系统，并利用响应式网络的功能来构建以太坊智能合约。

这种方法使我们能够创建流动性池，并在多个交易所之间自动进行再平衡。RC 将监控所有相关链上的流动性，并根据需要通过增加或减少资金来重新平衡它们。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->








# **Overview**

> 事件发生在一条链上，而驱动这些事件的逻辑却存在于另一条链上。标准合约无法弥合这种鸿沟。

单一链上自动化层取代

1.  跨链延伸，Cross-Chain Reach：订阅任何受支持的 EVM 链上的事件。通过一个合约做出响应
    
2.  Auto-Execution 自动执行：没有调用者，没有持有者，被动合约在满足条件时触发
    
3.  Composable Logic 可组合逻辑：将链式响应式合约组合在一起，构建多步骤、事件驱动的工作流。
    
4.  Built to Scale 按规模建造：事件处理在独立的 ReactVM 实例中运行，无需复杂的协调开销。
    
5.  Direct Log Access 直接日志访问：响应式合约直接从日志订阅中读取区块链事件。无需预言机，也无需中间件。
    

[https://blog.reactive.network/reactive-x-rogues-studio-bringing-seamless-on-chain-incentives-to-web3-gaming/](https://blog.reactive.network/reactive-x-rogues-studio-bringing-seamless-on-chain-incentives-to-web3-gaming/)

可以作为一个 idea，我们的 EIP Playground 也可以参考这个来做一些有趣的动态 NFT

## **第一步：Reactive Basics**

1.  Origins and Destinations：了解始发地和目的地链及其回调代理地址（Callback Proxy addresses）。
    
2.  Hyperlane：传输跨链回调
    
3.  Reactive Contracts：响应式合约如何订阅事件并触发操作
    
4.  ReactVM：响应式执行的工作原理
    
5.  经济性：了解回调支付和 Reactive 的经济性
    

首先，智能合约无法在没有外部指令（无论是来自用户还是像交易机器人这样的自动化脚本）的情况下自主发起操作或响应区块链事件。这就需要持有私钥并引入中心化的控制点。响应式合约（RC）的出现正是为了解决这一限制。RC 旨在自主响应以太坊虚拟机（EVM）中的事件，并触发区块链生态系统中的后续操作。这种能力使得复杂的逻辑得以实现，它可以从多个链获取信息，并在无需中央监管的情况下跨平台执行更改或交易。

听起来很有用，虽然 EVM 已经是图灵完备，但是只是在合约内部，让合约与合约之间也上一个条件判断，但是这跟我单独写一个合约把这部分逻辑放进去差不多吧，或者说这个响应式更方便的是跨链传递消息

Gemini：

1.  **突破传统合约的“被动”限制（告别链下机器人）** EVM 确实是图灵完备的，但传统智能合约有一个致命弱点：它们是被动的，只有在用户或外部脚本（如自动交易机器人）发起交易时才会执行。如果你只是单独写一个包含条件判断的传统合约，它仍然无法自主发起操作或对区块链事件做出反应，必须依赖外部的唤醒。为了让普通合约实现自动化，开发者通常需要依赖链下计算、运行脚本机器人并保管私钥，这引入了中心化控制的风险。 相反，**RSCs 是主动的、事件驱动的**。它们能够不间断地持续监控 EVM 链上的事件日志，并在满足特定条件时自动决定发送跨链回调交易并触发后续逻辑，完全不需要等待用户或机器人来触发。例如，在 Uniswap 的止损/止盈应用中，RSC 可以自主监听交易对的汇率变动事件（Sync events），并在达到阈值时直接自动触发代币交换，无需用户盯盘或运行机器人。
    
2.  **赋予合约“时间观念”（内置定时任务 CRON）** 单独写的传统合约本身是没有主动的“时间观念”的，只能等待被调用。而 Reactive 网络引入了内置的定时任务（CRON）机制。系统的智能合约会在固定的区块间隔发出 cron 事件，相当于给智能合约提供了一个“内置时钟”。这意味着合约可以自行按照时间表定期执行操作，比如定期更新数据、分发奖励，或者在 Aave 上定期自动检查用户的借贷健康度以防止爆仓（自动存入抵押品或偿还债务），这一切都不需要任何外部的触发。
    
3.  **不仅是传递消息，而是真正的“跨链自动化”** 正如你所说，跨链是 RSC 的极大优势，但它的作用远不止“传递消息”，而是构建**无缝的条件式跨链工作流**。 例如在 Web3 游戏《World of Rogues》中，RSC 被用来构建自动化的玩家奖励系统：系统在一条低成本链上记录玩家的连续活跃度，然后自动在玩家首选的另一条链上为他们铸造 NFT 奖励。玩家无需繁琐地去进行手动领取（Claim）的仪式，NFT 会自动发送到他们的钱包中，降低了摩擦和 gas 费用开销，实现了类似 Web2 的丝滑体验。
    

-   **去中心化：** RC 在区块链上独立运行，消除了中心化的控制点，并通过降低操纵或故障的风险来提高安全性。
    
-   **自动化：** RC 会根据链上事件自动执行智能合约逻辑，从而减少人工干预的需要，并实现高效的实时响应。
    
-   **跨链互操作性：** RC 可以与多个区块链进行交互，从而实现复杂的跨链交互，增强多功能性并弥合网络之间的差距。
    
-   **增强效率和功能：** 通过对实时数据做出反应，RC 提高了智能合约的效率，支持复杂金融工具、动态 NFT 和创新 DeFi 应用等高级功能。
    
-   **DeFi 及其他领域的创新：** RC 为 DeFi 和其他区块链应用带来了新的可能性，例如自动化交易和动态治理，从而创建了一个更具响应性和互联性的区块链生态系统。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
