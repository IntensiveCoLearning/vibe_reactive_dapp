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
# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->
# 今天的学习总结

# ！！！！！！！！！！！啊啊啊啊啊找了好久发现

脚本 ✅ 完全没问题

合约 ✅ 完全没问题

问题只有一个：

# Hyperlane 不支持 Lasna Testnet → BSC Testnet 这条路 ❌

## 一、合约理解方面

**问题1：两层 decode 的原因**

-   `trigger()` 传入 `bytes` 类型数据
    
-   以太坊记录事件时会对 `bytes` 再做一次 ABI 编码
    
-   所以 `handle()` 收到的是两层编码，需要两层 decode 才能取出原始数据
    

**问题2：**`dispatch()` **和** `handle()` **的关系**

-   `dispatch()` 是发送方调用，指定目标链、目标合约、消息内容
    
-   `handle()` 是被动接收，由 Hyperlane Mailbox 自动调用
    
-   两者参数一一对应，Hyperlane 负责中间传递
    

**问题3：**`_send()` **函数三行的含义**

-   第一行：地址格式转换 `address → bytes32`
    
-   第二行：`quoteDispatch()` 先查询手续费
    
-   第三行：`dispatch{value: fee}` 附带手续费发送消息
    

**问题4：**`log.data` **是怎么来的**

-   不是手动赋值的
    
-   是 Reactive Network 从链上日志里自动读取并注入到 `react()` 里的
    
-   你只需要 `emit`，Reactive Network 自动监听并调用 `react(log)`
    

* * *

## 二、代币和费用方面

**问题5：两套费用系统**

| 费用 | 币种 | 用途 |
| --- | --- | --- |
| Reactive 订阅费 | REACT / lREACT | 激活合约订阅，支付监听费用 |
| Hyperlane 手续费 | lREACT（原生币） | dispatch() 发送跨链消息 |

**问题6：lREACT 和 REACT 的关系**

-   lREACT 是 Lasna 链的原生币
    
-   REACT 是订阅计价单位
    
-   `sendTransaction {value}` 发送 lREACT，系统自动换算成 REACT 余额
    

**问题7：合约里需要充什么**

-   Lasna 合约需要同时有两种费用
    
-   部署时 `{value}` 充入 lREACT，同时覆盖订阅费和 Hyperlane 手续费
    
-   `coverDebt` 是额外补充订阅费用，不是必须单独执行
    

* * *

## 三、部署方面

**问题8：常见代码错误**

-   `origin.deployed()` → 应改为 `origin.waitForDeployment()`
    
-   `origin.address` → 应改为 `await origin.getAddress()`
    
-   `ethers.ethers.parseEther` → 应改为 `ethers.parseEther`
    
-   `waitForDeployment(2)` 不接受参数 → 等待区块确认应用 `tx.wait(2)`
    
-   `catch` 里用逗号 → 应改为分号或换行
    

**问题9：合约名和文件名的区别**

-   Hardhat 找的是合约名（`contract CrossChainOrigin`），不是文件名
    
-   两者不一致会报 `HH700: Artifact not found` 错误
    
-   解决方法：先 `npx hardhat compile`，再确认合约名
    

**问题10：RPC 连接超时**

-   原因：Lasna 的 RPC URL 失效
    
-   解决：换成 `https://kopli-rpc.reactive.network`
    

* * *

## 四、部署顺序和流程

**正确的部署流程：**

```
第一步：部署 Origin → BSC Testnet
第二步：部署 Reactive → Lasna（带上 {value} 充入 lREACT）
第三步：coverDebt 激活订阅（如果部署时已充值可跳过）
第四步：调用 trigger() → BSC Testnet
第五步：等待 1~3 分钟
第六步：验证结果
  → ReactScan 查看 RVM TRANSACTIONS
  → BSCScan 查看 Events 标签
```

* * *

## 五、验证结果的方法

**ReactScan 看什么：**

-   CONTRACT STATUS = ACTIVE ✅  
    合同状态 = 有效 ✅
    
-   RVM TRANSACTIONS 里有新记录
    
-   DESTINATION TRANSACTION 显示 BNB SMART CHAIN TESTNET
    

**BSCScan 看什么：**

-   Events 标签里有 `TransactionReceived` 事件
    
-   Events 标签里有 `Received` 事件
    

* * *

## 六、今天遇到的 Bug

**Bug1：lREACT 余额不足**

-   现象：Callback 被送回 Lasna 而不是 BSC
    
-   原因：`dispatch()` 手续费不够
    
-   解决：往合约补充更多 lREACT（至少 0.5）
    

* * *

## 七、扩展思路

**跟单机器人的可行性：**

-   可以监听 PancakeSwap 的 `Swap` 事件
    
-   判断金额是否超过阈值
    
-   超过则触发通知或自动跟单
    
-   限制：只能监听有 `emit` 事件的合约，无法监听普通转账
    

# 代码问题补充总结

## 一、Hardhat ethers v6 新旧写法对比

| 旧版写法（会报错） | 新版写法（正确） |
| --- | --- |
| await contract.deployed() | await contract.waitForDeployment() |
| contract.address | await contract.getAddress() |
| ethers.utils.parseEther() | ethers.parseEther() |
| ethers.utils.formatEther() | ethers.formatEther() |
| ethers.utils.AbiCoder | ethers.AbiCoder.defaultAbiCoder() |

* * *

## 二、等待区块确认的正确写法

javascript

```javascript
// 错误：waitForDeployment 不接受参数
await contract.waitForDeployment(2)

// 正确：部署后用 deploymentTransaction().wait(n) 等待区块
await contract.waitForDeployment()
const tx = contract.deploymentTransaction()
await tx.wait(2)  // 等待2个区块确认
```

* * *

## 三、main() 函数结构

javascript

```javascript
// 错误：main() 调用写在函数内部
async function main() {
    // ...
    main().catch((error) => {  // ❌ 不能在里面调用自己
        console.log(error),    // ❌ 逗号不对
        process.exitCode = 1
    })
}

// 正确：main() 调用写在函数外部
async function main() {
    // ...
}

main().catch((error) => {      // ✅ 写在外面
    console.error(error)       // ✅ 用分号或换行
    process.exitCode = 1
})
```

* * *

## 四、部署新合约 vs 连接已有合约

javascript

```javascript
// 部署新合约（创建一个新的）
const Factory = await ethers.getContractFactory("CrossChainOrigin")
const contract = await Factory.deploy(参数)

// 连接已有合约（找到已部署的）
const contract = await ethers.getContractAt(
    "CrossChainOrigin",          // 合约名
    "0xddeC2eE0...2307a6"        // 合约地址
)
```

* * *

## 五、带 ETH 部署合约

javascript

```javascript
// 构造函数有 payable 时，部署时可以附带原生币
const contract = await Factory.deploy(
    param1,
    param2,
    { value: ethers.parseEther("0.1") }  // ✅ 附带 0.1 原生币
)

// 注意：ethers.ethers.parseEther 是错误的！
{ value: ethers.ethers.parseEther("0.1") }  // ❌ 多写了一个 ethers
{ value: ethers.parseEther("0.1") }          // ✅ 正确
```

* * *

## 六、ABI 编码和解码

javascript

```javascript
// 编码：把多个参数打包成 bytes
const message = ethers.AbiCoder.defaultAbiCoder().encode(
    ["address", "address", "uint256"],   // 类型数组
    [senderAddr, receiverAddr, amount]   // 值数组
)

// 解码：把 bytes 还原成多个参数
const [sender, receiver, amt] = ethers.AbiCoder.defaultAbiCoder().decode(
    ["address", "address", "uint256"],
    message
)
```

* * *

## 七、查询余额

javascript

```javascript
// 查询钱包余额
const balance = await ethers.provider.getBalance(deployer.address)
console.log(ethers.formatEther(balance))

// 查询合约余额
const contractBalance = await ethers.provider.getBalance(contractAddress)
console.log(ethers.formatEther(contractBalance))

// 注意：address 是字符串，没有 .balance() 方法
const address = await contract.getAddress()
address.balance()  // ❌ 报错！
ethers.provider.getBalance(address)  // ✅ 正确
```

* * *

## 八、Solidity 中的地址类型转换

solidity  坚固性

```solidity
// address(20字节) → bytes32(32字节)
// 不能直接转换，必须经过三步
bytes32 result = bytes32(uint256(uint160(originAddress)));
//               ↑第三步  ↑第二步  ↑第一步

// bytes32 → address
address result = address(uint160(uint256(bytes32Value)));
```

* * *

## 九、{value} 语法

solidity  坚固性

```solidity
// Solidity 中调用 payable 函数时附带原生币
mailbox.dispatch{value: fee}(param1, param2, param3);
//              ↑ 这是附带手续费的语法，不是参数

// 等价于
// 调用 dispatch 函数，同时转入 fee 数量的原生币
```

* * *

## 十、常见报错和解决方法

| 报错信息 | 原因 | 解决方法 |
| --- | --- | --- |
| HH700: Artifact not found | 合约名写错 | 检查 contract XXX 名字是否一致，重新 compile |
| ConnectTimeoutError | RPC URL 失效 | 换一个 RPC URL |
| insufficient funds | 余额不足 | 充值对应链的原生币 |
| Not authorized | 调用者不是 owner | 检查调用账户是否正确 |
| revert | value 不足 | 先用 quoteDispatch 查询手续费再发送 |
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->


## 今天学习的内容

### 一、跨链架构理解

```
Demo架构（回环）：
[Base链] → [Lasna] → [Base链]
发出事件   监听转发   接收执行

你的架构（跨链）：
[BSC Testnet] → [Lasna] → [BSC Testnet]
发出事件        监听转发    接收执行
```

* * *

### 二、为什么需要Reactive Network

```
没有Reactive：
事件发生 → 需要人工发现 → 手动触发跨链

有了Reactive：
事件发生 → 自动监听 → 自动触发跨链
           全程无人值守！
```

* * *

### 三、Hyperlane的作用

```
Callback Proxy → 只支持有限的链
Hyperlane      → 支持50+条链

BSC Testnet没有Callback Proxy
所以必须用Hyperlane作为跨链传输层
```

* * *

### 四、整个跨链流程

```
① 你调用 trigger(abi.encode(sender,receiver,amount))
        ↓
② emit Trigger事件写入BSC链上日志
        ↓
③ Reactive合约监听到 → react()被触发
        ↓
④ emit Callback → 系统调用callback()
        ↓
⑤ callback() → _send()
        ↓
⑥ mailbox.dispatch() 发出跨链消息
        ↓
⑦ Hyperlane Relayer搬运消息到BSC Testnet
        ↓
⑧ BSC Testnet的Mailbox调用handle()
        ↓
⑨ handle()解码数据，存储交易记录
```

* * *

### 五、两个合约的分工

```
CrossChainOrigin（BSC Testnet）：
  → trigger()：只需要emit事件，不需要dispatch()
  → handle()：接收消息，两层decode，存储交易记录
  → mailbox：只用address类型，只做安全验证

CrossChainReactive（Lasna）：
  → 和Demo完全一样，只改部署参数
  → mailbox：用IMailbox类型，需要调用dispatch()
  → react()：收到事件后发出Callback指令
  → _send()：真正调用Hyperlane发消息的地方
```

* * *

### 六、关键概念掌握

```
✅ msg.sender是动态的，每次调用都不同
✅ onlyOwner → 验证调用者是你的钱包
✅ onlyMailbox → 验证调用者是Hyperlane Mailbox
✅ handle()的调用者是Mailbox，不是owner
✅ bytes32→address的转换：address(uint160(uint256(sender)))
✅ IMailbox接口的作用：让合约能调用dispatch()
✅ address类型 vs 接口类型的区别
✅ abi.encode打包 / abi.decode解包
✅ react()在ReactVM里，不能直接调用外部合约
✅ callback()是react()和_send()之间的桥梁
✅ 两层encode → 需要两层decode
```

* * *

## 今天感到困惑的地方

### 困惑一：msg.sender的理解

```
困惑：以为msg.sender是固定的一个值
解答：msg.sender是动态的，取决于谁发起这次调用
     trigger() → msg.sender = 你的钱包
     handle()  → msg.sender = Hyperlane Mailbox
```

### 困惑二：handle()是怎么被调用的

```
困惑：不理解handle()是怎么和Mailbox联系起来的
解答：handle()是被动调用的，不需要你主动触发
     Hyperlane Mailbox在收到消息后自动调用它
     就像快递员送货上门，不需要你去取
```

### 困惑三：为什么需要callback()这个桥梁

```
困惑：为什么不在react()里直接调用mailbox.dispatch()
解答：react()运行在ReactVM里，是轻量级虚拟机
     无法直接调用外部合约
     需要通过emit Callback → 系统调用callback()
     → callback()在普通网络里运行，可以调用Mailbox
```

### 困惑四：Origin合约需不需要IMailbox接口

```
困惑：以为Origin合约也需要IMailbox接口
解答：Origin合约不需要调用dispatch()
     只需要emit事件，Reactive自动监听
     mailbox只用来做安全验证，address类型就够了
```

### 困惑五：为什么需要两层decode

```
困惑：不理解为什么需要decode两次
解答：trigger()传入bytes类型的数据
     事件触发时以太坊对bytes再做一次ABI编码
     所以log.data = 外层编码(内层编码(三个字段))
     需要两层decode才能取出原始数据
```

### 困惑六：rvm\_id是什么地址

```
困惑：以为rvm_id是0x0000...fffFfF
解答：rvm_id是发出回调的Reactive合约地址
     传address(0)表示不做严格限制，任何来源都接受
```

* * *

## 今天的成果

```
✅ 编译通过：Compiled 3 Solidity files successfully
✅ 完成了CrossChainOrigin合约
✅ 完成了CrossChainReactive合约
✅ 理解了完整的跨链消息传递流程
✅ 下一步：部署到BSC Testnet和Lasna
```

* * *

今天学习得非常扎实！虽然过程中遇到了很多困惑，但每个问题都搞清楚了。明天可以直接开始部署和测试！
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->



## 一、CRON 和 HF 相关

**CRON 什么时候有优势**

```
用户操作触发  → 用普通事件订阅（更实时）
价格波动触发  → 用 CRON（价格变化不会产生事件）
最佳方案      → 两者结合使用
```

**ReactVM 的限制**

```
❌ 无法直接读取链上状态
❌ 无法调用 view 函数
✅ 只能读取事件日志里的数据
✅ 只能发出 Callback 指令
```

**怎么把 HF 传给 ReactVM**

```
方法一（最推荐）：借贷合约直接 emit 包含 HF 的事件
方法二：ReactVM → Callback → L1查询HF → emit新事件 → ReactVM读取
方法三：监听 Chainlink 价格更新事件，间接计算 HF
```

**可以同时监控多个用户吗**

```
可以！subscribe() 里不指定用户地址（填 REACTIVE_IGNORE）
= 监听所有用户的事件
react() 里逐一判断每个用户的 HF 是否 <= 1
```

* * *

## 核心公式（最重要）

```
你的合约 = AbstractReactive（已整合一切）

构造函数：
  service.subscribe() → 告诉系统监听什么

react() 里：
  第一步：从 log 提取数据
  第二步：业务判断（HF <= 1 ?）
  第三步：abi.encodeWithSignature 构造 payload
  第四步：emit Callback 发出跨链指令

L1Callback：
  实现对应的函数（liquidate 等）
  加上 rvmIdOnly 修饰器保证安全
```

-   **HyperlaneOrigin.sol**：部署在 Base Mainnet 的 EVM 侧端点。  
    **HyperlaneOrigin.sol** ：部署在 Base Mainnet 的 EVM 侧端点。
    
-   **HyperlaneReactive.sol**：部署在 Reactive Mainnet 的响应合约（继承 AbstractReactive 和 AbstractCallback）。  
    **HyperlaneReactive.sol** ：部署在 Reactive Mainnet 的响应合约（继承 AbstractReactive 和 AbstractCallback ）。
    
-   [**README.md**](http://README.md)：完整部署指南、环境变量、测试命令（你学习部署时主要参考的就是这个文件）。
    
-   三、付款机制相关
    
    **pay() 和 coverDebt() 的区别**
    
    ```
    pay()       → 系统合约主动来扣款（被动）
    coverDebt() → 你自己查询并主动还清（主动）
    两者最终都调用 _pay() 执行实际转账
    ```
    
    **\_pay() 在哪里实现**
    
    ```
    在 AbstractPayer.sol 里实现
    是底层工具函数，pay() 和 coverDebt() 都复用它
    前缀 _ 是约定俗成，表示内部函数
    ```
    
    **vendor 是什么**
    
    ```
    ReactiveContract 里：vendor = 系统合约（收订阅费）
    L1Callback 里：      vendor = Callback Proxy（收回调费）
    都是 0x0000...fffFfF，但在不同链上是不同合约
    ```
    

### 两个核心合约的详细解析（附完整源码关键点）

1\. HyperlaneOrigin.sol（Base 侧，简单 EVM 合约）

solidity

```
// SPDX-License-Identifier: GPL-2.0-or-later
pragma solidity >=0.8.0;

contract HyperlaneOrigin {
    event Trigger(bytes message);
    event Received(uint32 indexed chain_id, address indexed sender, bytes message);

    address public owner;
    address public mailbox;

    constructor(address _mailbox) { ... }
    modifier onlyOwner() { ... }
    modifier onlyMailbox() { ... }

    function trigger(bytes calldata message) external onlyOwner { emit Trigger(message); }

    function handle(uint32 chain_id, bytes32 sender, bytes calldata message) external payable onlyMailbox {
        emit Received(chain_id, address(uint160(uint256(sender))), message);
    }
}
```

-   **作用**：
    
    -   trigger(bytes)：只有 owner 可以调用，发出 Trigger 事件（供 Reactive 订阅）。
        
    -   handle(...)：Hyperlane Mailbox 投递消息时调用，记录接收到的 payload。
        
-   **Hyperlane 集成**：依赖 Base 的官方 Mailbox 地址（0xeA87ae93Fa0019a82A727bfd3eBd1cFCa8f64f1D）。
    

2\. HyperlaneReactive.sol（Reactive 侧，核心自动化合约）

solidity

```
// SPDX-License-Identifier: UNLICENSED
pragma solidity >=0.8.0;

import '../../../lib/reactive-lib/src/abstract-base/AbstractReactive.sol';
import '../../../lib/reactive-lib/src/abstract-base/AbstractCallback.sol';

interface IMailbox { /* dispatch + quoteDispatch */ }

contract HyperlaneReactive is AbstractReactive, AbstractCallback {
    event Trigger(bytes message);

    uint256 public constant TRIGGER_TOPIC_0 = 0x53a2e0b3...;  // Trigger 事件的 topic
    uint64 public constant GAS_LIMIT = 1000000;

    address public owner;
    IMailbox public mailbox;
    uint256 public chain_id;        // Base chainId = 8453
    address public origin;          // HyperlaneOrigin 地址

    constructor(IMailbox _mailbox, uint256 _chain_id, address _origin) 
        AbstractCallback(address(SERVICE_ADDR)) payable { ... }

    // 订阅 Base 的 Trigger 事件 + 本合约自己的 Trigger 事件
    function react(LogRecord calldata log) external vmOnly { ... }  // 发出 Callback
    function callback(...) external authorizedSenderOnly { _send(message); }
    function send(bytes calldata message) external onlyOwner { _send(message); }
    function trigger(bytes calldata message) external onlyOwner { emit Trigger(message); }

    function _send(bytes memory message) internal {
        uint256 fee = mailbox.quoteDispatch(...);
        mailbox.dispatch{value: fee}(...);   // 使用 Hyperlane 跨链发送
    }
}
```

-   **关键机制**：
    
    -   构造函数中自动订阅 Base 的 origin 合约（TRIGGER\_TOPIC\_0）和本合约自身事件。
        
    -   当 Base 发出 Trigger → Reactive 的 RVM 捕获 log → 调用 react() → 发出 Callback → 执行 \_send() 通过 Hyperlane Mailbox 把消息发回 Base。
        
    -   反向也支持：直接调用 send() 或 trigger()（owner 手动触发）。
        
-   **Hyperlane 集成**：使用 Reactive Mainnet 的 Mailbox（0x3a464f746D23Ab22155710f44dB16dcA53e0775E），并通过 quoteDispatch + dispatch 支付 on-chain 费用（需要合约持有 REACT token）。
    

### 工作流程（双向消息传递）

1.  **Base → Reactive**：Base owner 调用 trigger(payload) → 发出事件 → Reactive 订阅并自动 react → 通过 Hyperlane 发送回执（或处理逻辑）。
    
2.  **Reactive → Base**：Reactive owner 调用 send() 或 trigger() → 直接通过 Mailbox 跨链投递 → Base 的 handle 接收并 emit Received。  
    **Reactive → Base** ：Reactive owner 调用 send() 或 trigger() → 直接通过 Mailbox 跨链投递→ Base 的 handle 接收并 emit Received 。
    
3.  **实时性**：完全 on-chain，无需 off-chain relayer，Reactive 的 log automation 保证低延迟。
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->




## 顺序安排思路

```
先懂事件结构(topic) → 再懂怎么订阅 → 再懂react()怎么处理 → 再懂回调怎么发出 → 最后懂怎么部署
```

* * *

## 第一步：LogRecord 里的 topic0~3 分别是什么？

以太坊每个事件日志的结构是这样的：

```
event Transfer(address indexed from, address indexed to, uint256 value);
```

当这个事件触发时，日志里会有：

| 字段 | 内容 |
| --- | --- |
| topic0 | 事件签名的哈希，永远是这个，固定的 |
| topic1 | 第一个 indexed 参数，即 from |
| topic2 | 第二个 indexed 参数，即 to |
| topic3 | 第三个 indexed 参数（如果有的话） |
| data | 非 indexed 参数，即 value |

**所以规律是：**

-   `topic0` = 永远是事件本身的"名字"（签名哈希）
    
-   `topic1~3` = 你在事件里标了 `indexed` 的参数，按顺序排列
    
-   没有 `indexed` 的参数 → 在 `data` 里，不在 topic 里
    

* * *

## 第二步：subscribe() 参数怎么填？填 0 是什么意思？

solidity  坚固性

```solidity
service.subscribe(
    chain_id,      // 监听哪条链（如Sepolia = 11155111）
    contract,      // 监听哪个合约地址
    topic0,        // 监听哪种事件（事件签名哈希）
    topic1,        // 过滤条件：from地址
    topic2,        // 过滤条件：to地址
    topic3         // 过滤条件：第三个indexed参数
);
```

**填** `REACTIVE_IGNORE` **的意思是"不过滤这个字段"，即任何值都接受。**

例子：

solidity  坚固性

```solidity
// 只监听Transfer事件，不管from/to是谁
service.subscribe(
    11155111,
    tokenAddress,
    keccak256("Transfer(address,address,uint256)"),
    REACTIVE_IGNORE,  // 任何from都行
    REACTIVE_IGNORE,  // 任何to都行
    REACTIVE_IGNORE
);
```

**一个合约可以同时订阅多个事件吗？可以！** 在构造函数里多次调用 `subscribe()` 即可：

solidity  坚固性

```solidity
constructor() {
    service.subscribe(...); // 订阅事件A
    service.subscribe(...); // 订阅事件B
    service.subscribe(...); // 订阅事件C
}
```

# 一、Reactive Library 整体架构总览

Reactive Network 的基础库由 **6 个核心接口 / 抽象合约**组成，它们构成了 Reactive Contract 运行的 **底层协议层（Protocol Layer）**。

这些合约主要解决三个问题：

1️⃣ **谁需要付钱（Payer）**  
2️⃣ **钱付给谁（Payable）**  
3️⃣ **如何订阅事件（Subscription）**

换句话说：

> Reactive Network 本质是一个 **“事件订阅 + 自动执行 + 费用结算” 的系统**

因此整个库围绕 **事件系统 + 费用系统**设计。

* * *

# 二、核心合约关系结构

你给出的结构实际上可以整理为 **两条核心体系**：

### 1️⃣ 费用系统（Payment System）

```
IPayable
   ↑
IPayer
   ↑
AbstractPayer
```

作用：

```
Reactive Contract 支付费用
```

* * *

### 2️⃣ Reactive 执行系统

```
ISubscriptionService
        ↑
ISystemContract
        ↑
IReactive
```

作用：

```
Reactive Contract 订阅事件
Reactive Network 调用 react()
```

* * *

# 三、完整依赖关系（最准确结构）

整理后的真实结构如下：

```
IPayable
     ↑
ISubscriptionService
     ↑
ISystemContract
     
IPayer
     ↑
IReactive
     ↑
AbstractPayer
```

功能解释：

| 合约 | 类型 | 作用 |
| --- | --- | --- |
| IPayable | interface | 被支付对象 |
| IPayer | interface | 支付者 |
| ISubscriptionService | interface | 事件订阅 |
| ISystemContract | interface | Reactive系统 |
| IReactive | interface | Reactive Contract接口 |
| AbstractPayer | abstract contract | 自动支付实现 |

* * *

# 四、IPayable.sol — 债务查询接口

## 1️⃣ IPayable 的核心概念

IPayable 定义：

> **谁可以收钱**

在 Reactive Network 中，主要有两个对象可以收钱：

1️⃣ **System Contract**  
2️⃣ **Callback Proxy**

它们都实现了 **IPayable 接口**。

* * *

## 2️⃣ IPayable 完整结构

```
interface IPayable {

    receive() external payable;

    function debt(
        address _contract
    ) external view returns (uint256);
}
```

接口只有两个功能：

| 函数 | 作用 |
| --- | --- |
| receive() | 接收ETH |
| debt() | 查询欠款 |

* * *

# 五、receive() 的作用

```
receive() external payable;
```

这是 Solidity 的 **特殊函数**。

作用：

```
允许合约接收ETH
```

触发条件：

当有人向该合约 **直接转账ETH** 时触发。

例如：

```
payable(systemContract).transfer(1 ether);
```

系统合约就会执行：

```
receive()
```

* * *

### 在 Reactive Network 中的意义

Reactive Contract 在运行时会产生费用，例如：

| 行为 | 费用来源 |
| --- | --- |
| 事件监听 | subscription fee |
| react()执行 | execution fee |
| callback交易 | gas fee |

如果费用没有及时支付：

```
系统会记录债务
```

然后：

```
暂停你的订阅
```

直到你还清。
<!-- DAILY_CHECKIN_2026-03-13_END -->

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
