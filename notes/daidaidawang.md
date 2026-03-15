---
timezone: UTC+8
---

# daidaidawang

**GitHub ID:** daidaidawang

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->
# 抽象合约

## AbstractCallback

### 作用

为反应式合约提供回呼授权功能

### 关键变量

· rvm\_id - 授权的 ReactVM 标识符

· vendor - 回调代理地址

### 修饰符

```
modifier rvmIdOnly(address _rvm_id) {
```

```
    require(rvm_id == address(0) || rvm_id == _rvm_id, 'Authorized RVM ID only');
```

```
    _;
```

```
}
```

### 构造函数

```
constructor(address _callback_sender) {
```

```
rvm_id = msg.sender; // 部署者设为授权发送方
```

```
vendor = IPayable(payable(_callback_sender)); // 注册回调代理
```

```
addAuthorizedSender(_callback_sender); // 添加为授权发送者
```

```
}
```

## AbstractPausableReactive

### 作用

提供可暂停的事件订阅功能

### 核心结构

```
struct Subscription {
```

```
uint256 chain_id; // 链ID
```

```
address _contract; // 合约地址
```

```
uint256 topic_0; // 事件主题0
```

```
uint256 topic_1; // 事件主题1
```

```
uint256 topic_2; // 事件主题2
```

```
uint256 topic_3; // 事件主题3
```

```
}
```

### 主要函数

函数 功能 权限

pause() 取消所有可暂停订阅 仅持有者

resume() 恢复所有暂停的订阅 仅持有者

实现逻辑：

· pause(): 遍历getPausableSubscriptions()并取消订阅

· resume(): 遍历相同列表并重新订阅

\-–

3\. AbstractPayer

作用：提供支付和债务和解功能

修饰符

```
modifier authorizedSenderOnly() {
```

```
require(senders[msg.sender], ‘Authorized sender only’);
```

```
_;
```

```
}
```

### 核心函数

函数 描述

pay(uint256 amount) 向授权发送方转账

coverDebt() 清偿供应商债务

addAuthorizedSender(address) 添加授权发送者

removeAuthorizedSender(address) 移除授权发送者

receive() 接受直接转账

### 内部支付逻辑

```
function _pay(address payable recipient, uint256 amount) internal {
```

```
require(address(this).balance >= amount, ‘Insufficient funds’);
```

```
if (amount > 0) {
```

```
(bool success,) = payable(recipient).call{value: amount}(new bytes(0));
```

```
require(success, ‘Transfer failed’);
```

```
}
```

```
}
```

## AbstractReactive

### 作用

所有反应式合约的基础合约

### 执行模式

· vmOnly - ReactVM 执行环境

· rnOnly - 反应式网络执行环境

### 关键常量

· SERVICE\_ADDR - 系统服务合约地址

### 构造函数

```
constructor() {
```

```
vendor = service = SERVICE_ADDR; // 初始化系统合约
```

```
addAuthorizedSender(address(SERVICE_ADDR)); // 授权支付
```

```
detectVm(); // 检测执行模式
```

```
}
```

### 模式检测

```
function detectVm() internal {
```

```
uint256 size;
```

```
assembly { size := extcodesize(0x0000000000000000000000000000000000fffFfF) }
```

```
vm = size == 0; // 无代码则为VM模式
```

```
}
```

# 接口定义

## IPayable

```
interface IPayable {
```

```
receive() external payable; // 接受付款
```

```
function debt(address _contract) external view returns (uint256); // 查询债务
```

```
}
```

## IPayer

```
interface IPayer {
```

```
function pay(uint256 amount) external; // 发起支付
```

```
receive() external payable; // 接受转账
```

```
}
```

## IReactive

### 核心事件

```
event Callback(
```

```
uint256 indexed chain_id,
```

```
address indexed _contract,
```

```
uint64 indexed gas_limit,
```

```
bytes payload
```

```
);
```

## 日志结构

```
struct LogRecord {
```

```
uint256 chain_id; // 链ID
```

```
address _contract; // 合约地址
```

```
uint256 topic_0; // 主题0
```

```
uint256 topic_1; // 主题1
```

```
uint256 topic_2; // 主题2
```

```
uint256 topic_3; // 主题3
```

```
bytes data; // 事件数据
```

```
uint256 block_number; // 区块号
```

```
uint256 op_code; // 操作码
```

```
uint256 block_hash; // 区块哈希
```

```
uint256 tx_hash; // 交易哈希
```

```
uint256 log_index; // 日志索引
```

```
}
```

## 核心函数

```
function react(LogRecord calldata log) external; // 处理事件通知
```

## ISubscriptionService

```
interface ISubscriptionService {
```

```
function subscribe( // 订阅事件
```

```
uint256 chain_id, address _contract,
```

```
uint256 topic_0, uint256 topic_1,
```

```
uint256 topic_2, uint256 topic_3
```

```
) external;
```

```
function unsubscribe( // 取消订阅
```

```
uint256 chain_id, address _contract,
```

```
uint256 topic_0, uint256 topic_1,
```

```
uint256 topic_2, uint256 topic_3
```

```
) external;
```

```
}
```

## ISystemContract

组合 IPayable + ISubscriptionService，代表系统合约接口。

# 系统合约

## SystemContract

### 职责

· 处理反应式合约付款

· 管理合约访问控制（白名单/黑名单）

· 周期性触发克隆事件

## CallbackProxy

### 职责

· 传递回调交易到目标合约

· 管理存款、准备金和债务

· 限制回调仅限授权合约

· 计算gas成本和回扣

## AbstractSubscriptionService

### 职责

· 管理活动订阅

· 支持链、合约、主题过滤

· 支持通配符 REACTIVE\_IGNORE

· 发布订阅更新事件

# CRON 功能

SystemContract提供基于时间的自动化机制：

间隔 区块数 近似时间 topic\_0

-   Cron1 1 ~7秒 0xf02d6ea5c22a71cffe930a4523fcb4f129be6c804db50e4202fb4e0b07ccb514
    
-   Cron10 10 ~1分钟 0x04463f7c1651e6b9774d7f85c85bb94654e3c46ca79b0c16fb16d4183307b687
    
-   Cron100 100 ~12分钟 0xb49937fb8970e19fd46d48f7e3fb00d659deac0347f79cd7cb542f0fc1503c70
    
-   Cron1000 1000 ~2小时 0xe20b31294d84c3661ddc8f423abb9c70310d0cf172aa2714ead78029b325e3f4
    
-   Cron10000 10,000 ~28小时 0xd214e1d84db704ed42d37f538ea9bf71e44ba28bc1cc088b2f5deca654677a56
    

## 特点

· 无需轮询或外部自动化

· 基于区块号整除性触发

· 每个事件包含当前区块号参数

# 继承关系

AbstractPayer

↑

AbstractReactive ( implements IReactive )

↑

AbstractCallback

↑

AbstractPausableReactive

# 权限修饰符

| 修饰符 | 作用 |
| --- | --- |
| rvmIdOnly | 仅限授权RVM |
| authorizedSenderOnly | 仅限授权发送者 |
| rnOnly | 仅限反应式网络 |
| onlyOwner | 仅限合约持有者 |
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->

# 反应式合约

## **部署**

反应式网络（RNK）EOA与合约互动公链，管理订阅

ReactVM（RVM）私有执行环境，事件处理

> 两者使用相同字节码，各自独立运行

## **州分离**

合约可以通过调用系统合约检测ReactVM内部执行，在ReactVM外部回退

## **验证**

通过Sourcify端点验证。Sourcify是去中心化验证服务，将字节码和源代码匹配，使合约可审计透明

### **部署时验证**

```
forge create `
--verifier sourcify `
--verify `
--chain-id $CHAIN_ID（反应式主网/Lasna Testnet 1597/5318007） `
--private-key $PRIVATE_KEY（部署秘钥） `
$PATH
```

### **部署后验证**

```
forge verify-contract `
--verifier sourcify `
--chain-id $CHAIN_ID（反应式主网/Lasna Testnet 1597/5318007） `
$CONTRACT_ADDR $CONTRACT_NAME（合约名称）
```

## **事件处理**

IReactive

LogRecord结构

| chain_id | 事件源链ID |
| --- | --- |
| _contract | 发布事件合约地址 |
| topic_0到topic_3 | 索引主题 |
| data | 非索引数据 |
| block_number | 区块高度 |
| tx_hash | 交易哈希 |
| log_index | 日志索引 |

### **Callback事件**

-   跨链交易请求
    

```
event Callback(
  uint256 indexed chain_id,
  address indexed _contract,
  uint64 indexed gas_limit,
  bytes payload
);
```

### **react() 函数**

-   事件处理入口
    

```
function react(LogRecord calldata log) external;
```

## **跨链回调**

回调触发流程

```
//编码目标链交易
bytes memory payload = abi.encodeWithSignature(
"functionName(param1,param2,...)",
address(0),//ReactVM地址，合约部署者地址
otherParams...
);
​
​
//发射Callback事件
emit Callback(
targetChainId,
targetContract,
gasLimit,
payload
);
```

## **环境检测**

> 检查系统合约地址代码大小

```
address constant SYSTEM_CONTRACT = ;
function detectVm() internal {
uint256 size;
assembly { size := extcodesize(SYSTEM_CONTRACT)}
vm = size == 0;//true = ReactVM,反之反应式网络
}
```

## **订阅**

### **ISubscriptionService接口**

> 事件订阅服务：根据特定标准订阅特定事件，在事件发生时接收通知

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

> chain\_id A表示时间源链ID uint256 EIP155
> 
> \_contract 发送事件的起始链合约地址
> 
> topic\_0, , , 事件主题

### **无效接口**

IReactive接口

### **标准**

-   万用符用途：用于表示按任意合约地址筛选，表示任何链ID，以及主题按任意主题过滤。address(0) uint256(0) REACTIVE\_IGNORE
    
-   具体价值：至少有一个标准必须是具体的数值，以确保有意义的订阅。
    

# **ReactVM**

## **状态变量**

```
bool private triggered;            //是否已触发
bool private done;                 //是否已完成
address private pair;              //Uniswap交易对地址
address private stop_order;        //止损订单地址
address private client;            //客户端地址
bool private token0;               //监控token为0还是1
uint256 private coefficient;       //计算系数
uint256 private threshold;         //是否触发阙值
```

## **环境修饰符**

> vmOnly 事件处理

```
modifier vmOnly() {
require(vm,'VM only');
_;
}
```

## **双态环境**

ReactVM状态 订阅事件发生时自动更新

反应式网络状态 EOA调用合约函数时更新

> 大多数自动化逻辑运行ReactVM内部

## **ReactVM局限性**

在内部，反应式合约不能直接访问外部系统。从反应式网络接受事件日志，向目的链发送回调，但无法从外部RPC断点或链外服务交互

![image-20260314200217317](https://daidaidawang.oss-cn-beijing.aliyuncs.com/typora/image-20260314200217317.png)

# **反应式网络**

## **变量**

继承AbstractReactive可用资源

```
service(ISubscriptionService)://管理事件订阅服务接口
```

```
vm(bool)://执行环境标识符
 true//在ReactVM内部执行
 false//在反应式网络中执行
```

```
service.subscribe(...)//订阅特定链上合约事件
```

## **环境修饰符**

> rnOnly 订阅管理，配置更新

```
modifier rnOnly() {
require(!vm,'Reactive Network only');
_;
}
```

## **环境检测**

```
if(!vm) {
service.subscribe(...);
}
```

部署阶段（!vm = true） 在反应是网络中注册事件订阅

运行阶段（vm =true） 在ReactVM中接受事件并触发react()
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->


花费了近一周的时间，今天完成了任务一。从学习部署合约，向地址转账，到最后回调成功。感觉收获很多，印象最深的是最后的回调一直在回滚问了AI了解到是`BasicDemoL1Callback` 构造函数接收了 `_callback_sender`。由于之前在Callback合约中开启了 `authorizedSenderOnly`，合约会执行： `require(msg.sender == _callback_sender)`

如果之前传入的 `$env:DESTINATION_CALLBACK_PROXY_ADDR` **不等于** `0xc9f36411C9897e7F959D99ffca2a0Ba7ee0D7bDA`，那么合约就会 Revert，回调回滚

稍微修改了一下原Callback合约不过并不太安全，后续仍需要再完善

-   重新加上 `authorizedSenderOnly` 修饰符
    
-   **精确部署：** 重新部署时，确保 `--constructor-args` 传入的是这次抓到的这个地址：`0xc9f36411C9897e7F959D99ffca2a0Ba7ee0D7bDA`（Reactive Network 官方在 Sepolia 测试网上部署的回调代理合约）
    

```solidity
// SPDX-License-Identifier: GPL-2.0-or-later

pragma solidity >=0.8.0;

import "reactive-lib/abstract-base/AbstractCallback.sol";

contract BasicDemoL1Callback is AbstractCallback {
    event CallbackReceived(
        address indexed origin,
        address indexed sender,
        address indexed reactive_sender
    );

   constructor(address _callback_sender) AbstractCallback(_callback_sender) payable {}

    function callback(address sender) external {
        emit CallbackReceived(
            tx.origin,
            msg.sender,
            sender
        );
    }
}
```

同时今天学习总结了下Reactive的笔记

# **Reactive Contracts**

## **区别传统合约**

传统合约被动，只在直接EOA交易时执行

RC持续监控事件并自动执行预定义的区块链动作响应

## **优点**

-   **控制倒置：**RC通过允许合同本身根据预定义事件决定何时执行，从而反转了传统执行模型，无需外部触发器如机器人或用户。
    
-   **去中心化自动化：**RC实现了完全去中心化的运营，自动化数据收集、DEX交易和流动性管理等流程，无需集中式中介。
    
-   **跨链交互作用：**RC可以与多个区块链和多个源进行交互，实现跨链套利和多预言机数据聚合等复杂用例。
    

## **实际应用**

包括从预言机收集数据、实现UniSwap止损单、执行DEX套利以及自动在交易所间重新平衡池。

# **跨链自动化系统**

> 源链: 触发事件 ↓ 中继器: 监听并转发 ↓ 目标链: cast send $CALLBACK\_ADDR (您当前命令) ↓ 回调合约: 确认跨链消息完成

源合约

目标合约

反应式合约

# **RVM交易**

在REACT中获得资金

## **Payment**

### **源合约直接转账**

资助源合约

```
cast send $env:CONTRACT_ADDR `
  --rpc-url $env:REACTIVE_RPC `
  --private-key $env:REACTIVE_PRIVATE_KEY `
  --value 0.1ether
```

### **回调付款**

**直接转账**

资助回调合同

```
cast send $env:CALLBACK_ADDR `
  --rpc-url $env:DESTINATION_RPC `
  --private-key $env:DESTINATION_PRIVATE_KEY `
  --value 0.1ether
```

**结清未偿还债务**

```
cast send `
  --rpc-url $DESTINATION_RPC `
  --private-key $DESTINATION_PRIVATE_KEY `
  $CALLBACK_ADDR "coverDebt()"
```

## **Deposits**

### **系统合约存款**

> 存入资金 → 检查债务 → 如有债务则自动回拨偿还 → 剩余资金存入

**主动触发执行**

```
cast send `
  --rpc-url $env:REACTIVE_RPC `
  --private-key $env:REACTIVE_PRIVATE_KEY `
  $env:SYSTEM_CONTRACT_ADDR "depositTo(address)" `
  $env:CONTRACT_ADDR `
  --value 0.1ether
```

> 系统合同和回调代理共享相同地址
> 
> 0x0000000000000000000000000000000000fffFfF

### **回调代理存款**

> 用户存款 1 ETH ↓ 系统检查合约A的债务 0.3 ETH ↓ 自动回拨 0.3 ETH 偿还债务 ↓ 剩余 0.7 ETH 存入余额

> 被动，触发协议实现 债务自动偿还扣除

```
cast send `
  --rpc-url $DESTINATION_RPC `
  --private-key $DESTINATION_PRIVATE_KEY `
  $CALLBACK_PROXY_ADDR "depositTo(address)" `
  $CALLBACK_ADDR `
  --value 0.1ether
```

## **Balance**

### **回调合约余额**

检索回调合约余额

```
cast balance $CONTRACT_ADDR --rpc-url $DESTINATION_RPC
```

### **反应式合约余额**

检索反应式合约余额

```
cast balance $CONTRACT_ADDR --rpc-url $REACTIVE_RPC
```

## **Debt**

### **系统合约债务**

```
cast call $SYSTEM_CONTRACT_ADDR "debts(address)" $CONTRACT_ADDR --rpc-url $REACTIVE_RPC | cast to-dec
```

### **回调合约债务**

```
cast call $CALLBACK_PROXY_ADDR "debts(address)" $CONTRACT_ADDR --rpc-url $DESTINATION_RPC | cast to-dec
```

## **Reserves**

### **取回系统合约储备**

```
cast call $SYSTEM_CONTRACT_ADDR "reserves(address)" $CONTRACT_ADDR --rpc-url $REACTIVE_RPC | cast to-dec
```

### **查询回调代理储备余额**

```
cast call $CALLBACK_PROXY_ADDR "reserves(address)" $CONTRACT_ADDR --rpc-url $DESTINATION_RPC | cast to-dec
```
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->



这几天主要学习了如何部署reactive合约，包括设置地址，转账等一系列操作，也对reactive合约的实现流程有了个大致了解

**\[源链（如Sepolia）\] -> \[Reactive Network\] -> \[目标链（如Sepolia）\]**

**(事件发生地) (自动化处理中心) (动作执行地)**

最关键的一步也是reactive合约特色的流程

**event  
↓**

**Reactive监听**

**↓**

**REACT（）**

**↓**

**emitCallback**

**↓**

**目标链 Callback（）**

下面是我部署例题的最后一个的过程，其中涉及了对地址网络的配置，合约部署完成后对合约生成的地址进行转账，产生日志触发回调。

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/daidaidawang/images/2026-03-12-1773331085178-image.png)![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/daidaidawang/images/2026-03-12-1773331114395-image.png)
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->




作为web3新手，第一次正式的跟着学习。今天的任务只完整了部署前两个阶段，卡在了第三个阶段，因为缺少kREACT还没有部署上。目前正在转换，明天继续部署。

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/daidaidawang/images/2026-03-09-1773064550959-image.png)![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/daidaidawang/images/2026-03-09-1773064484606-image.png)![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/daidaidawang/images/2026-03-09-1773064359719-image.png)
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
