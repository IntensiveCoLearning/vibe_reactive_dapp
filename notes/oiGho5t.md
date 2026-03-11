---
timezone: UTC+8
---

# oiGhost

**GitHub ID:** oiGho5t

**Telegram:** @2040668117

## Self-introduction

菜鸟一枚

## Notes

<!-- Content_START -->
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
**事件与回调的工作原理**

**EVM 事件如何工作**

当智能合约发出事件时，事件数据会被存储在交易的日志中。这些日志被附加到区块链的区块上，但不会直接影响区块链的状态，它们提供了一种根据事件参数记录和检索信息的方式，开发者使用 `event` 关键字在智能合约中定义事件，后跟事件名称以及他们想要记录的信息的数据类型。要发出事件，智能合约使用 `emit` 关键字，后跟事件名称和要记录的数据。  
外部应用程序，例如 dApp 或后端服务，可以监听这些事件。通过指定事件签名，并可选择过滤参数，这些应用程序可以在事件发出时订阅实时更新。

**例如：**  
Chainlink 的去中心化预言机网络为各种加密货币、商品和其他链外数据提供实时数据馈送，直接将数据送入智能合约。  
**定义价格更新事件**  
一个需要实时价格信息来执行其逻辑的智能合约，该合约可能会定义一个如下的事件：

`event PriceUpdated(string symbol, uint256 newPrice);`  
该事件旨在在价格更新时记录资产的代码及其新价格。  
**发出事件**  
当智能合约从 Chainlink 预言机接收到新的价格更新时，它会发出 `PriceUpdated` 事件：  
`emit PriceUpdated("ETH", newEthPrice);`  
在这行代码中，`newEthPrice` 是从 Chainlink 获取的以太坊更新后的价格，Chainlink 的预言机会定期更新。

**响应式合约中的事件处理**  
响应式合约必须实现 `IReactive` 接口来处理传入的事件。

```solidity
pragma solidity >=0.8.0;

import './IPayer.sol';

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
    event Callback(
        uint256 indexed chain_id,
        address indexed _contract,
        uint64 indexed gas_limit,
        bytes payload
    );
    
    function react(LogRecord calldata log) external;
}
```

响应式网络持续监控事件日志，并将其与响应式合约中定义的订阅条件进行匹配。当检测到符合条件的事件时，网络会触发 `react()` 方法，传入相关的详细信息。

响应式合约可以访问所有标准的EVM功能。但是，它们在私有的 ReactVM 中运行，这限制它们只能与同一部署者部署的合约进行交互。这种隔离确保了响应式合约在处理来自响应式网络的事件时，能够维持一个受控且安全的环境。

**处理回调**  
当 `Callback` 事件被发出时，响应式网络会检测到它并处理 `payload`，其中以特定格式编码了交易细节。然后，响应式网络使用提供的 `chain_id` 和 `gas_limit`，向目标链上的指定合约提交一笔交易。

**关于授权的注意事项**  
出于安全和授权目的，响应式网络会自动将 `payload` 内调用参数的前 160 位替换为调用该响应式合约的 RVM ID（等同于 ReactVM 地址）。这个 RVM ID 与合约部署者的地址相同。因此，无论你在 Solidity 代码中使用什么变量名，回调函数中的第一个参数始终是 ReactVM 地址（`address` 类型）。

**编码和发出回调事件**  
为了在目标链上发起操作，你可以将交易细节编码到 `payload` 中并发出 `Callback` 事件。例如，在 Uniswap 止损订单演示中，该过程用于通过目标链合约触发代币销售：

```solidity

bytes memory payload = abi.encodeWithSignature(
   "stop(address,address,address,bool,uint256,uint256)",
   address(0),  // ReactVM地址
   pair,        // 涉及的Uniswap交易对地址
   client,      // 发起止损订单的客户端地址
   token0,      // 交易对中第一个代币的地址
   coefficient, // 决定销售价格的系数
   threshold    // 触发销售的价格阈值
);
emit Callback(chain_id, stop_order, CALLBACK_GAS_LIMIT, payload);
```
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

反应式合约：

**RCs 与传统智能合约的区别:**  
两者的主要区别在于“反应性”。传统智能合约是被动的，只在直接的EOA交易（外部账户交易）触发时执行。相比之下，RCs 是_反应性的_，会持续监控区块链中感兴趣的事件，并在检测到这些事件时，自动执行预定义的区块链操作做出响应。

理解反应式合约（RC）的一个关键概念是控制反转（IoC）。传统智能合约在直接控制模式下运行，其函数的执行由外部参与者（EOA用户或机器人）发起。然而，反应式合约通过基于预定义事件的发生自主决定何时执行，反转了这种控制。这种控制反转范式改变了应用与区块链交互的方式，实现了更具动态性和响应性的系统。  
控制反转使我们能够避免托管那些模拟人类签署交易的额外实体。一旦检测到感兴趣的事件，反应式网络会自动执行你在反应式合约中实现的逻辑。这可能涉及基于事件数据进行计算。RC是有状态的，意味着它们有一个可以存储和更新值的状态。你可以随时间在状态中积累数据，然后在历史数据和新区块链事件的组合满足指定标准时采取行动。

作为事件的结果，RC更新其状态，保持最新状态，并可以在EVM区块链上发起交易。整个过程在反应式网络中无需信任地运行，确保自动、快速和可靠的执行。

**反应式合约内部发生了什么**  
创建反应式合约，先指定感兴趣的链、合约和事件，RC将监控这些地址以查找指定事件，并在检测到事件时启动执行，比如：简单的货币或代币转移。

**预言机**是将可信外部数据馈送到区块链的第三方服务。  
**总结：**

-   **反应式合约 vs. 传统合约**：与传统智能合约不同，RC自主监控区块链事件并在无需用户干预的情况下执行操作，提供了更具动态性和响应性的系统。  
    
-   **控制反转**：RC通过允许合约本身基于预定义事件决定何时执行来反转传统执行模型，消除了对外部触发器（如机器人或用户）的需求。  
    
-   **去中心化自动化**：RC支持完全去中心化的操作，自动化如数据收集、DEX交易和流动性管理等流程，无需中心化中介。  
    
-   **跨链交互**：RC可以与多个区块链和数据源交互，实现如跨链套利和多预言机数据聚合等复杂用例。  
    
-   **实际应用**：RC具有多种应用，包括从预言机收集数据、实现Uniswap止损单、执行DEX套利以及跨交易所自动再平衡池。
    

**事件与回调的工作原理**

**EVM 事件如何工作**

当智能合约发出事件时，事件数据会被存储在交易的日志中。这些日志被附加到区块链的区块上，但不会直接影响区块链的状态，它们提供了一种根据事件参数记录和检索信息的方式
<!-- DAILY_CHECKIN_2026-03-10_END -->
<!-- Content_END -->
