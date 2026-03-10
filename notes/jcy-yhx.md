---
timezone: UTC+8
---

# jcy-yhx

**GitHub ID:** jcy-yhx

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->
# Basic Demo 实操

## Basic Demo 三个合约详解与流程合约角色

### 1、Origin Contract（BasicDemoL1Contract）

-   receive()：收到 ETH → emit Received(origin, sender, value) → 退回 ETH
    
-   作用：事件触发器
    

### 2、Reactive Contract（BasicDemoReactiveContract）

-   constructor：调用 subscribe() 订阅源链 Received 事件（topic\_0 固定）
    
-   react()：收到 log → if value ≥ 0.001 ETH → emit Callback（payload 编码 callback(address)）
    
-   作用：大脑 + 反应逻辑
    

### 3、Destination Callback（BasicDemoL1Callback）

-   继承 AbstractCallback
    
-   callback(sender)：验证 authorizedSenderOnly + rvmIdOnly → emit CallbackReceived
    
-   作用：跨链接收端 + 授权验证
    

测试命令完整流程（cast send 0.001 ETH）

1.  转 0.001 ETH → Origin receive() 触发
    
2.  emit Received + 立即退回 ETH
    
3.  Reactive 扫描 → 调用 react()
    
4.  条件成立 → emit Callback（替换 sender 为 ReactVM ID）
    
5.  系统跨链 → Proxy 调用 Destination callback()
    
6.  Destination emit CallbackReceived（证明成功）
    

浏览器可见标志：

-   源链：Received 事件 + ETH 转入/退回
    
-   目标链：CallbackReceived 事件 + 来自 Proxy 的调用交易
    

## 一些概念

### **1\. 事件Topic**

-   **topic\_0**: 事件签名哈希（常数，如`Received`事件的hash）
    
-   **topic\_1-3**: indexed参数的值
    
    text
    
    ```
    event Received(origin, sender, value)
    topic_1 = origin地址
    topic_2 = sender地址  
    topic_3 = value金额
    ```
    

### **2\. rvmIdOnly修饰器**

solidity

```
// 特性：
- 自动验证调用者是Reactive VM
- 自动替换sender参数为真实Reactive合约地址
- 安全机制，防止伪造

// 效果：
你传入：address(0) 或 0x1234...
实际收到：你的Reactive合约真实地址
```

### **3\. 订阅函数**

```
function subscribe(
    uint256 chain_id,           // 要监听的链ID
    address contract_address,   // 要监听的合约地址
    uint256 topic_0,            // 事件签名哈希（必填）
    uint256 topic_1,            // 第一个indexed参数的过滤值
    uint256 topic_2,            // 第二个indexed参数的过滤值
    uint256 topic_3             // 第三个indexed参数的过滤值
) external;
```
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->

## **什么是睿应层（Reactive Network）**

**Reactive Network**是一个兼容 EVM 的链**，**引入了一种全新的合约类型，叫**Reactive Contracts，RC** 与传统智能合约的主要区别在于反应性。**传统智能合约**是**被动的，**等待别人调用函数去执行，而**RC**是主动的**，**可以主动监听事件，自动触发。

## **Reactive Contracts是怎么做到“主动”的**

创建反应式合约时，首先需要指定感兴趣的链、合同以及事件（events)，如：价格达到阈值、某个交易对流动性变化、某笔借贷抵押比例触发清算条件，一旦检测到感兴趣的事件，反应式网络会自动执行用户预先在反应式合同中实现的逻辑。

流程大概如下：

1、Reactive Smart Contract（RSC）在部署/配置时声明订阅，在 Solidity 代码里通过特定的接口，react() 函数 + 订阅参数，相当于在 Reactive Network 上注册了一个“事件订阅”。

2、Reactive Network 作为底层网络，本身是一个专用的执行层，它的节点会持续监听所有它支持的 EVM 兼容链的区块和日志（logs），实时扫描新区块里的事件日志，对比所有已注册的 RSC 订阅条件，看有没有匹配的 log，一旦匹配上，就把这条 log以交易的形式推送到 Reactive Network 自己的链上，调用对应 RSC 的 react() 函数，把事件数据作为参数传进去。.

3、在 Reactive Network 上执行 react()，RSC 的 react() 函数收到事件数据后，用 Solidity 逻辑判断是否要行动（if 条件、阈值等），如果要行动，它可以生成 callback 交易（发到目标链），或者更新自身状态、触发其他逻辑，整个执行发生在 Reactive Network 的 ReactVM（隔离的执行环境）里，gas 由 RSC 拥有者或经济模型支付。

## **How EVM Events Work**

当智能合约发出事件时，事件数据会存储在交易日志中。这些日志附着在区块链的区块上，但不会直接影响区块链状态。相反，它们提供了一种基于事件参数记录和检索信息的方法。开发者通过**event**关键词定义智能合约中的事件，随后是事件名称和他们希望记录的信息类型。智能合约在发送事件时使用 **emit**关键字，随后输入事件名称和待记录的数据。外部应用程序，如 dApps 或后端服务，可以监听这些事件。通过指定事件签名并可选地过滤参数，这些应用程序可以在事件发出时订阅实时更新。

## **Event Processing in Reactive Contracts**

反应式网络持续监控事件日志，并将其与反应式合同中定义的订阅标准进行匹配。当检测到符合条件的事件时，网络会触发**react()**方法，并传递相关信息。反应式合同可以访问所有标准的 EVM 功能。然而，它们运行在一个私有的 ReactVM，限制它们只能与同一部署器部署的合同交互。这种隔离确保反应式合约在处理反应式网络事件时保持受控和安全的环境。

## **如何向目标链发起回调**

Reactive 合约运行在 Reactive Network 这个专用链上（ReactVM 环境），当它收到事件、跑完 react() 逻辑后，如果需要“干点事”，但这个事要在另一条链上完成，就不能直接调用（因为 Reactive 链和目标链是分开的）。这时Reactive 合约 emit（发出）一个特殊的 Callback 事件。Reactive Network 的系统（节点/基础设施）看到这个事件 → 自动帮你在目标链上发一条交易，调用你指定的合约、执行指定的函数。

## Callback 事件的结构（event Callback）

```solidity
event Callback(
    uint256 indexed chain_id,       // 目标链的 chain ID（EIP-155，比如 Ethereum=1，Base=8453）
    address indexed _contract,      // 目标链上要调用的合约地址
    uint64 indexed gas_limit,       // 给这次跨链交易设置的 gas 上限（防止 gas 爆炸）
    bytes payload                   // 编码好的调用数据（告诉要调用哪个函数、传什么参数）
);
```

**payload 是最关键的：它是用 abi.encodeWithSignature 编码的，格式通常是 "函数名(参数类型列表)" + 实际参数。**

## 安全机制：RVM ID 自动替换（最重要的一点防作恶）

Reactive Network 强制在处理 payload 时，会把 payload 的前 160 bits（前 20 字节） 自动替换成 RVM ID（ReactVM ID）。RVM ID 其实就是这个 Reactive 合约的部署者（deployer）地址（或 ReactVM 实例的标识）。**结果：不管你在 Solidity 里第一个参数写什么（哪怕写 address(0)），实际发到目标链时，第一个参数都会变成你的 ReactVM 地址。**

**这样设计的目的是：**授权控制，目标链上的合约（比如你的 Stop Order 合约）可以检查 msg.sender（或第一个参数）是不是授权的 ReactVM 地址，只有“自己人”才能调用。防止滥用，别人不能伪造你的 Reactive 合约去乱发回调。必须留第一个参数给它，文档反复强调，payload 至少要有一个 address 类型参数（第一个槽位），否则调用会失败。

## 跨链回调处理总结

处理流程总结（Processing the Callback）

1.  Reactive 合约在 react() 里 emit Callback 事件。
    
2.  Reactive Network 监听到这个事件（因为它是系统级事件）。
    
3.  系统解析 chain\_id、\_contract、gas\_limit、payload。
    
4.  自动替换 payload 前 20 字节为 RVM ID。
    
5.  用提供的 gas\_limit，在目标链上发一条交易：调用 \_contract 的对应函数，传入解码后的 payload（已替换）。
    
6.  目标链执行成功 → 完成跨链自动化。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
