---
timezone: UTC+8
---

# vergissxie

**GitHub ID:** vergissxie

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->
## 📚 Basic Demo L1 项目实战总结

### 1\. **Basic Demo 项目概览**

-   **核心功能**：跑通 Reactive 跨链通信的最基础链路，实现“源链触发转账 -> 抛出特定事件 -> 跨链自动监听 -> 目标链验证并执行回调”的完整闭环。
    
-   **三个主要合约**：
    
    -   `BasicDemoL1Contract`：源链触发合约（Sepolia 上运行，负责接收转账并发射事件）
        
    -   `BasicDemoReactiveContract`：响应式机器脑（Reactive Network 上运行，负责死盯源链事件并下达跨链指令）
        
    -   `BasicDemoL1Callback`：目标链回调合约（Sepolia 上运行，负责验证官方代理身份并执行最终动作）
        

### 2\. **跨链通信的工作流程**

Plaintext

```
用户发送 0.001 ETH 到源链合约 
  ↓ (触发 receive() 函数)
源链合约 emit Received 事件
  ↓ (同时触发原路退款逻辑)
ReactVM 监听到特定 topic_0 的事件
  ↓
向 Sepolia 的官方 Callback Proxy 下达指令
  ↓
Proxy 代理调用目标链的 Callback 合约
  ↓ 
验证身份：require(msg.sender == CALLBACK_PROXY_ADDR)
  ↓ (是官方代理)
目标合约向 Proxy 支付微量 ETH 跑腿费
  ↓
跨链流程完美闭环 (可通过 Etherscan 内部交易查证)
```

### 3\. **关键数据定义：事件签名**

Solidity

```
// 源链合约中定义的事件
event Received(
    address indexed origin,
    address indexed sender,
    uint256 indexed value
);
```

### 4\. `topic_0` **与事件哈希的真相** ⚠️

-   **重要发现**：Reactive 机器人是个“脸盲”，它根本不认识 `Received` 这个名字，它只认这串 32 字节的哈希指纹：`0x8cabf31d2b1b11ba52dbb302817a3c9c83e4b2a5194d35121ab1354d69f6a4cb`。
    
-   **计算规则**：`topic_0` 是通过 **Keccak-256** 算法计算得出的。
    
-   **正确做法**：提取事件的名称和参数类型（严格去除变量名、修饰符和空格），拼接成 `Received(address,address,uint256)`，然后再进行哈希计算。可以使用命令行 `cast keccak "Received(address,address,uint256)"` 验证。
    

### 5\. **Windows 终端与 Foundry 部署对比**

| 特性 | Linux/Mac 终端 (官方文档) | Windows PowerShell (你的实战) |
| 环境变量调用 | $VAR_NAME | $env:VAR_NAME |
| 加载 .env 文件 | source .env | 需通过一段 PowerShell 脚本循环注入 |
| 广播交易指令 | 常被省略，默认兼容 | 必须强制添加 --broadcast 或 --legacy |

### 6\. **资金流转与赛博黑洞**

-   **退款机制**：源链合约在 `receive()` 函数末尾写了 `payable(tx.origin).transfer(msg.value)`，因此触发事件的本金会被**原路退回**。
    
-   **资金黑洞**：部署目标链合约时预存了 `0.02 ETH`，但由于合约源码中**没有编写** `withdraw()` **提现函数**，剩余资金将永久锁定在链上无法取出。
    

### 7\. **部署与测试流程（7步）**

1.  编写并配置 `.env` 文件（保持三个私钥一致，配置好 RPC 和 Proxy 地址）。
    
2.  在 PowerShell 中使用脚本将 `.env` 变量注入系统内存。
    
3.  部署 **源链合约** (Origin)，保存其生成的地址。
    
4.  部署 **目标链合约** (Callback)，附带 `--value 0.02ether` 预存 Gas，并传入 Proxy 地址作为构造参数。
    
5.  部署 **响应式合约** (Reactive)，附带 `--value 0.1ether` 订阅费，传入源链地址、`topic_0` 哈希和目标链地址。
    
6.  使用 `cast send` 向源链合约转账 `0.001 ETH` 扣动扳机。
    
7.  打开 Etherscan，在目标链合约的 **Internal Transactions (内部交易)** 中抓取跨链回调成功的铁证。
    

* * *

## 🎯 核心知识点

| 概念 | 关键理解 |
| 跨链身份验证 | 目标合约必须校验 msg.sender 是否为官方指定的 CALLBACK_PROXY_ADDR，防止恶意伪造跨链消息。 |
| Event Signature | topic_0 = keccak256("EventName(type1,type2)")。 |
| 交易广播 (--broadcast) | forge create 想要把数据真正写进以太坊，必须带上广播指令，否则只是本地内存演习。 |
| 智能合约提现 | 任何接收 ETH 的合约，如果没有配套的提出函数，就会变成吞噬资金的黑洞。 |

* * *

## 💡 学到的最重要的一点

**“Web3 跨链的核心在于精准的监听与严格的权限控制”**：

在多链宇宙中，事件 (Event) 是跨链通信的唯一信使。源链只管无脑发事件，Reactive 网络依靠 `topic_0` 精准捕捉信使，而目标链则必须像保安一样死死守住 `require(msg.sender == 代理地址)` 这道防线，以此才能构建出安全、自动化的跨链高速公路。

## 📚 今日学习内容总结

### 1\. **Uniswap V2 Stop Order Demo 项目概览**

\- **核心功能**：实现响应式智能合约，监控 Uniswap V2 池价格，当汇率降低到预设阈值时自动触发止损单

\- **三个主要合约**：

  - `UniswapDemoToken`：ERC-20 测试代币

  - `UniswapDemoStopOrderReactive`：响应式合约（Reactive Network 上运行）

  - `UniswapDemoStopOrderCallback`：止损回调合约（目标链上运行）

\### 2. **响应式合约的工作流程**

\`\`\`

Uniswap池价格变化 

  ↓ (Sync事件)

ReactVM 监听事件

  ↓

react() 函数被触发

  ↓

检查价格是否低于阈值

  ↓ (是)

发送 Callback 到目标链

  ↓

目标合约执行 token swap

  ↓ (Stop事件)

ReactVM 再次触发 react()

  ↓

流程完成 (done = true)

\`\`\`

\### 3. **关键数据结构：LogRecord**

\`\`\`solidity

struct LogRecord {

    uint256 chain\_id;      // 源链 ID (如 11155111 for Sepolia)

    address \_contract;     // 事件来源合约地址

    uint256 topic\_0;       // 事件签名

    uint256 topic\_1;       // 第一个 indexed 参数

    uint256 topic\_2;       // 第二个 indexed 参数

    uint256 topic\_3;       // 第三个 indexed 参数

    bytes data;            // 非 indexed 参数的编码数据

}

\`\`\`

\### 4. **Sync 事件解析的真相** ⚠️

\`\`\`solidity

event Sync(uint112 indexed reserve0, uint112 indexed reserve1);

\`\`\`

\- **重要发现**：Sync 事件的两个参数都被 `indexed`，所以：

  - `reserve0` 存储在 `topic_1` 中

  - `reserve1` 存储在 `topic_2` 中

  - `log.data` 是 **空的**

\- **当前代码的问题**`abi.decode(log.data, (Reserves))` 尝试解码空数据

\- **正确做法**：直接从 `topic_1` 和 `topic_2` 提取

\### 5. **calldata 与内存**

| 特性 | calldata | memory |

|------|----------|--------|

| **访问** | 只读 | 可读写 |

| **来源** | 由 Reactive Network 提供 | 函数内部创建 |

| **Gas** | 便宜 | 昂贵 |

| **用途** | 函数参数 | 局部变量 |

\### 6. **ABI 编码与解码**

\- `abi.encode()` / `abi.encodePacked()`：值 → 字节

\- `abi.decode()`：字节 → 值

\- 只有 **非 indexed** 的参数才会被编码到 `log.data`

\### 7. **部署与测试流程（7步）**

1\. 配置环境变量（RPC、私钥等）

2\. 部署或配置测试代币和 Uniswap 对

3\. 部署目标链上的回调合约

4\. 向流动池添加流动性

5\. 部署响应式合约到 Reactive Network

6\. 授权代币支出

7\. 调整汇率触发止损单

\---

\## 🎯 核心知识点

| 概念 | 关键理解 |

|------|--------|

| **Reactive Network** | 专门用于跨链事件监听的 L2 网络 |

| **Event Indexing** | indexed 参数存在 topic 中，未 indexed 存在 data 中 |

| **LogRecord** | 链上事件在 Reactive Network 中的标准表示 |

| **回调机制** | 通过 emit Callback 事件将指令传回目标链 |

| **止损逻辑** | 监控储备比率，低于阈值时触发交易 |

\---

\## 💡 学到的最重要的一点

**Solidity 中 Event 的 indexed 和非 indexed 参数有本质的存储差异**：

\- **indexed** → topic（可快速检索，但需要从 LogRecord 的 topicN 中读取）

\- **Non-indexed** → data（需要 ABI 解码，但你当前代码中数据是空的）

这解释了为什么 `abi.decode(log.data, (Reserves))` 在这个合约中问题不大——虽然得不到数据，但代码反正是通过 `below_threshold()` 检查 topic 数据的。

\---

希望这个总结涵盖了你今天学到的所有内容！有任何需要深入的地方，继续提问即可。

结合今天学习的内容以及和你今天的对话，生成综合两部分的学习笔记

* * *
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->

作为技术小白，今天主要配置了环境，并且部署了demo合约

## 📌 一、 核心架构：Hardhat + Foundry 双擎混血

在实际的顶尖项目中，两者不是竞争关系，而是完美互补的搭档。

-   **Foundry (底层引擎)**：基于 Rust，速度极快。专精于写合约 (`/contracts`) 和写测试代码 (`/test`)。它的 Fuzzing (模糊测试) 能力是找 Bug 神器。
    
-   **Hardhat (外部通信)**：基于 JS/TS，生态庞大。专精于写复杂的自动化多链部署脚本 (`/scripts`) 以及与前端页面的交互联调。
    

## 🛠️ 二、 环境踩坑与终极解决方案 (Windows PowerShell 专属)

### 1\. Foundry 找不到依赖库 (`os error 2`)

-   **病因**：在混血项目中，Foundry 找不到 `node_modules` 下的真实代码路径，自动生成的映射有时会漏掉 `src/` 目录。
    
-   **解药**：手动创建/修改项目根目录的 `remappings.txt`，将路径精确指向 `src/`：
    
    Plaintext
    
    ```
    forge-std/=node_modules/forge-std/src/
    ```
    
-   **注意**：修改后必须执行 `forge clean` 和 `forge cache clean` 清除旧的错误记忆。
    

### 2\. 屏蔽烦人的废弃语法警告 (Warning 2424)

-   **病因**：依赖库使用了老版本的汇编语法，新版 Solidity 编译器疯狂报黄字警告。
    
-   **解药**：在 `foundry.toml` 中添加配置直接屏蔽：
    
    Ini, TOML
    
    ```
    ignored_error_codes = [2424]
    ```
    

### 3\. GitHub 443 超时与 `git submodule` 报错

-   **解药 1**：在终端让 Git 走全局代理：`git config --global http.proxy http://127.0.0.1:端口号`。
    
-   **解药 2**：如果手动下载 ZIP 压缩包，解压后目录里没有 `.git` 文件夹，执行 `forge install` 会报错。必须先执行 `git init` 为其“注入灵魂”，然后再 install。
    

### 4\. PowerShell 读取 `.env` 变量失效

-   **病因**：Windows 终端不支持 Linux 的 `$VAR_NAME` 语法，且 `source .env` 经常报错。
    
-   **解药**：使用 PowerShell 脚本将 `.env` 注入系统环境，且调用变量时必须加上 `$env:` 前缀（例如 `$env:ORIGIN_RPC`）。
    

* * *

## 🌐 三、 Reactive Network 跨链基础模型 (Basic Demo)

跨链事件监听和响应的核心生命周期，由三个部署在不同链上的智能合约组成：

1.  **Origin Contract (源链合约 - Sepolia)**
    
    -   **作用**：发射信号。收到用户的资金或调用后，抛出一个带有特定哈希指纹的事件 (Event)。
        
2.  **Callback Contract (目标链合约 - Sepolia)**
    
    -   **作用**：接收信号并执行。必须预存 Gas 费 (`--value 0.02ether`)，且需要配置 `CALLBACK_PROXY_ADDR` 以验证调用者身份，防止伪造消息。
        
3.  **Reactive Contract (响应式机器人 - Lasna)**
    
    -   **作用**：中间的大脑。时刻监听源链，一旦发现对应的事件指纹，立刻触发目标链合约。部署时需要支付订阅费 (`--value 0.1ether`)。
        

* * *

## 💻 四、 黄金部署命令 (强制广播)

在 Foundry 中，为了确保交易真的被发送到区块链上，部署命令必须携带特定的参数：

-   必须带上 `--broadcast` 强行把交易广播上链。
    
-   如果依然无法广播，可以加上 `--legacy` 使用传统的兼容模式交易结构。
    

**完整的带参部署范例 (PowerShell 格式)：**

PowerShell

```
forge create --broadcast --rpc-url $env:DESTINATION_RPC --private-key $env:DESTINATION_PRIVATE_KEY src/demos/basic/BasicDemoL1Callback.sol:BasicDemoL1Callback --value 0.02ether --constructor-args $env:CALLBACK_PROXY_ADDR
```
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->


# Reactive Library

## 🌟 1. 概述 (Overview)

**Reactive Library** 是一套由 Reactive Network 官方提供的抽象合约（Abstract Contracts）和接口（Interfaces）集合。它旨在提供订阅、回调、支付和系统交互的基础架构，大幅减少开发者的样板代码 (Boilerplate)。

**安装方式 (Foundry)**

`forge install Reactive-Network/reactive-lib`

* * *

## 🏗️ 2. 抽象合约基类 (Abstract Contracts)

### 2.1 `AbstractReactive` (响应式核心基类)

这是编写运行在 Reactive Network 上（监听端）合约的**最核心基类**。它继承了 `AbstractPayer` 并实现了 `IReactive` 接口。

-   **核心功能**：自动检测环境（双状态模型），并提供相应的访问控制修饰符。  
    
-   **源码解析**：
    

Solidity

```
// 初始化：自动绑定系统合约，并授权它扣款。同时检测当前是否在 VM 沙盒中。
constructor() {
    vendor = service = SERVICE_ADDR;
    addAuthorizedSender(address(SERVICE_ADDR));
    detectVm();
}

// 核心魔法：环境检测机制 (通过检查系统合约地址的字节码大小)
function detectVm() internal {
    uint256 size;
    // solhint-disable-next-line no-inline-assembly
    assembly { size := extcodesize(0x0000000000000000000000000000000000fffFfF) }
    vm = size == 0; // 如果 size 为 0，说明在沙盒里
}
```

-   **提供的修饰符**：
    
    -   `vmOnly`：限定只能在 ReactVM 沙盒中执行（如 `react` 函数）。
        
    -   `rnOnly`：限定只能在 RN 主网执行（如管理功能）。  
        

### 2.2 `AbstractPausableReactive` (可暂停响应基类)

继承自 `AbstractReactive`，允许管理员随时暂停和恢复合约的监听状态（应急开关）。

-   **源码解析**：  
    

Solidity

```
// 初始化拥有者
constructor() {
    owner = msg.sender;
}

// 暂停监听：将合约中定义的所有 pausable 订阅全部取消
function pause() external rnOnly onlyOwner {
    require(!paused, 'Already paused');
    Subscription[] memory subscriptions = getPausableSubscriptions();
    for (uint256 ix = 0; ix != subscriptions.length; ++ix) {
        service.unsubscribe(
            subscriptions[ix].chain_id, subscriptions[ix]._contract,
            subscriptions[ix].topic_0, subscriptions[ix].topic_1,
            subscriptions[ix].topic_2, subscriptions[ix].topic_3
        );
    }
    paused = true;
}

// 恢复监听：重新订阅
function resume() external rnOnly onlyOwner {
    require(paused, 'Not paused');
    Subscription[] memory subscriptions = getPausableSubscriptions();
    for (uint256 ix = 0; ix != subscriptions.length; ++ix) {
        service.subscribe(
            subscriptions[ix].chain_id, subscriptions[ix]._contract,
            subscriptions[ix].topic_0, subscriptions[ix].topic_1,
            subscriptions[ix].topic_2, subscriptions[ix].topic_3
        );
    }
    paused = false;
}
```

### 2.3 `AbstractPayer` (支付结算基类)

处理在 Reactive Network 上的 Gas 费用结算和跨链代付逻辑。

-   **源码解析**：  
    

Solidity

```
// 授权检查修饰符
modifier authorizedSenderOnly() {
    require(senders[msg.sender], 'Authorized sender only');
    _;
}

// 授权管理
function addAuthorizedSender(address sender) internal { senders[sender] = true; }
function removeAuthorizedSender(address sender) internal { senders[sender] = false; }

// 接受原生代币充值 (用于预存 Gas 费)
receive() virtual external payable {}

// 向授权者付款
function pay(uint256 amount) external authorizedSenderOnly {
    _pay(payable(msg.sender), amount);
}

// 结清欠款 (系统合约的回调代理)
function coverDebt() external {
    uint256 amount = vendor.debt(address(this));
    _pay(payable(vendor), amount);
}

// 内部转账底层逻辑
function _pay(address payable recipient, uint256 amount) internal {
    require(address(this).balance >= amount, 'Insufficient funds');
    if (amount > 0) {
        (bool success,) = payable(recipient).call{value: amount}(new bytes(0));
        require(success, 'Transfer failed');
    }
}
```

### 2.4 `AbstractCallback` (目标链回调基类)

这是部署在**目标执行链**上，用来接收跨链指令的基类。

-   **核心功能**：验证跨链请求的合法性（确认是由你自己的 RVM 沙盒发出的）。  
    
-   **源码解析**：  
    

Solidity

```
// 部署时绑定：你的 RVM ID 和 官方的 Callback Proxy (Vendor)
constructor(address _callback_sender) {
    rvm_id = msg.sender; // 记录部署者的 RVM ID
    vendor = IPayable(payable(_callback_sender)); // 记录官方代理地址
    addAuthorizedSender(_callback_sender);
}

// 核心安全修饰符：拦截所有非你 RVM 发出的指令
modifier rvmIdOnly(address _rvm_id) {
    require(rvm_id == address(0) || rvm_id == _rvm_id, 'Authorized RVM ID only');
    _;
}
```

## 🔌 3. 核心接口扩展 (Interfaces Continued)

除了 `IReactive` 和 `ISubscriptionService`，底层的经济模型（Gas 代付与结算）依赖于以下几个核心接口：

### 3.3 `IPayable` (查询与收款接口)

定义了合约如何接收资金以及如何查询自己的欠款。

-   `receive() external payable;`：允许合约直接接收原生代币（用于给你的 RC 预充值 Gas 费）。  
    
-   `function debt(address _contract) external view returns (uint256);`：查询某个合约当前欠了系统多少 Gas 费。  
    

Solidity

```
interface IPayable {
    receive() external payable;
    function debt(address _contract) external view returns (uint256);
}
```

### 3.4 `IPayer` (付款标准接口)

这是一个极其精简的接口，定义了主动发起付款的标准动作。`IReactive` 接口实际上继承了它。

-   `pay(uint256 amount)`：主动发起一笔付款。  
    

Solidity

```
interface IPayer {
    function pay(uint256 amount) external;
    receive() external payable;
}
```

### 3.5 `ISystemContract` (主网系统总接口)

这是 Reactive Network 主网上那个无所不能的“大管家”（地址 `0x000...fffFfF`）的真实面貌。

它通过多重继承，**同时融合了支付 (**`IPayable`**) 和行政管理 (**`ISubscriptionService`**) 的功能**。当你调用 `service.subscribe(...)` 时，你实际上就是在与这个总接口交互。

Solidity

```
import './IPayable.sol';
import './ISubscriptionService.sol';

interface ISystemContract is IPayable, ISubscriptionService {}
```

## 🏛️ 4. 底层架构：三大系统合约 (System Contracts)

在 Reactive Network 的主网上，整个协议的运转并不是靠单一的黑盒完成的，而是由**三个各司其职的核心系统合约**协同管理的。它们构成了整个自动化网络的基础设施：

### 1\. System Contract (系统总管合约)

它是整个网络的入口，处理最高级别的行政指令。

-   **职能 A**：处理所有响应式合约的资金支付与预充值。  
    
-   **职能 B**：管理合约的访问控制（白名单/黑名单机制，防止恶意合约滥用网络）。  
    
    * * *
    
-   **职能 C (定时器)**：**定期抛出 CRON 事件（如** `Cron1`**、**`Cron1000`**），作为全网定时任务的“节拍器”。**  
    

### 2\. Callback Proxy (回调代理 / 跨链快递员)

它是负责把你的指令送到目标链上的执行者。

-   **职能 A**：将跨链 Callback 交易准确投递给目标链的业务合约。  
    
-   **职能 B**：管理你的存款、准备金和欠款（Debts）。  
    
-   **职能 C**：计算这笔跨链交易到底在目标链消耗了多少 Gas，并收取相应的费用（Kickbacks）。  
    
-   **职能 D (安全门)**：在目标链上，限制只有经过授权的 Reactive 合约发来的 Callback 才会被放行。  
    

### 3\. AbstractSubscriptionService (订阅服务引擎)

它是整个网络的“监控配置中心”。

-   **职能 A**：管理全网所有的事件订阅列表（增、删、改）。  
    
-   **职能 B**：支持基于 Chain ID、合约地址、以及 Topic 0-3 的多维度过滤。  
    
-   **职能 C**：支持 `REACTIVE_IGNORE` 这种通配符匹配（Wildcard matching）。  
    
-   **职能 D**：当你的合约调用 `subscribe` 或 `unsubscribe` 时，它会抛出相应的状态更新事件，通知底层的节点集群去更新内存里的监听规则。  
    

## ⏱️ 5. 系统进阶：定时任务 (CRON Functionality)

如果你不需要监听外部事件，而是希望合约**每隔一段时间自动执行一次**（如每天自动复投），可以使用系统级 Cron。

-   **机制**：系统合约会基于区块编号的整除性，定期抛出特定的 `Cron` 事件。你的 RC 只要订阅这些特定的 Topic，就能实现定时唤醒。  
    

| 频率 | 大致时间 | 订阅 Topic 0 |
| --- | --- | --- |
| Cron1 (每个区块) | ~7 秒 | 0xf02d6ea5c22a71cffe...b514 |
| Cron10 (每10个区块) | ~1 分钟 | 0x04463f7c1651e6b977...b687 |
| Cron100 (每100个区块) | ~12 分钟 | 0xb49937fb8970e19fd4...3c70 |
| Cron1000 (每1000个区块) | ~2 小时 | 0xe20b31294d84c3661d...e3f4 |
| Cron10000 (每万区块) | ~28 小时 | 0xd214e1d84db704ed42...7a56 |

**使用示例**

如果你想每两小时执行一次逻辑，只需在 `constructor` 中调用：

`service.subscribe(REACTIVE_CHAIN_ID, SYSTEM_CONTRACT, CRON_1000_TOPIC, ...)`

### 💡 核心解惑：CRON 事件到底是怎么运作的？

**你的疑惑**：“订阅接口只能订阅一个 cron 事件，那难道要重定义 cron 去监听其他事件吗？不然只是个定时器，并没有定时去监听哪个事件。”

**核心真相是：CRON 本身就是一个“闹钟”，它不需要再去监听别的事件。**

在传统 Web2 中，定时任务（Cron Job）是让服务器“每隔一小时去执行某段代码”。

在 Reactive Network 中，当你订阅了 `Cron1000`（大约每2小时触发一次）时，底层的运作逻辑是：

1.  **系统发信号**：每过 1000 个区块，主网的系统合约就会自己抛出一个 `Cron1000` 的事件日志。
    
2.  **唤醒沙盒**：你的 RC 合约因为订阅了这个事件，沙盒瞬间被唤醒，执行你的 `react(log)` 函数。
    
3.  **执行动作 (重点！)**：在 `react` 函数里，你并不是去“监听”别的事件，而是**直接去干活（发起回调指令）！**
    

**代码示例：CRON 的真实用法**

Solidity

```
function react(LogRecord calldata log) external override vmOnly {
    // 闹钟响了！判断唤醒我的是不是那个 2 小时响一次的闹钟
    if (log.topic_0 == CRON_1000_TOPIC) {
        
        // 既然闹钟响了，我就直接打包跨链指令
        bytes memory payload = abi.encodeWithSignature("harvestYield()");
        
        // 发送给目标链的收益聚合器，让它自动复投！
        emit Callback(TARGET_CHAIN_ID, YIELD_CONTRACT, GAS_LIMIT, payload);
    }
}
```

**总结**：CRON 充当的是**“触发源（Trigger）”**。它把你的合约从“被动等别人砸盘”变成了“主动定时去收菜”。

# Reactive Economy

## 🌟 1. 核心经济公式：为什么这么算钱？

Reactive Network 采用异步扣费模型，其费用由两部分组成。

### A. RVM 算力费计算

fee = BaseFee \\cdot GasUsed

-   **BaseFee (基础费)**：取自**交易执行后的下一个区块**。
    
    -   _解释_：因为 RVM 执行是实时的，而记账是异步的。系统通过引用后一个区块的费用来防止开发者利用内存状态操纵 Gas 价格。  
        
-   **GasUsed (消耗量)**：单次执行上限为 **900,000**。
    
    -   _解释_：这是为了防止由于逻辑死循环（Infinite Loop）导致节点计算资源被无休止占用。  
        

### B. 跨链回调 (Callback) 定价

p\_{callback} = p\_{base} \\cdot C \\cdot (g\_{callback} + K)

-   **C (网络系数)**：目标链定价系数。
    
    -   _解释_：不同公链（如 Ethereum vs Polygon）的本币汇率和安全成本不同，该系数用于平衡跨链成本。  
        
-   **K (固定附加费)**：
    
    -   _解释_：这是支付给底层中继节点执行身份验证、签名比对等“协议开销”的固定报酬。  
        

## 💻 2. 命令行工具 `cast` 参数详解

在操作 Reactive 合约时，我们使用 Foundry 旗下的 `cast` 工具。以下是常用参数的深度解释：

### 2.1 基础标志位 (Flags) 含义

| 参数 | 全称 | 作用与为什么需要它 |
| --- | --- | --- |
| --rpc-url | RPC 节点地址 | 告诉工具连接哪条链（Reactive 主网还是目标测试网）。 |
| --private-key | 私钥 | 用于对交易进行签名。注意： 发起交易的地址必须有钱支付这笔交易本身的 Gas。 |
| --value | 交易面值 | 你打算往合约里充入多少原生代币（REACT 或 ETH）。 |
| cast to-dec | 转换为十进制 | 链上返回的是 16 进制，该参数将其转为人类可读的数字。 |

* * *

## 🛠️ 3. 核心功能代码与逻辑拆解

### 3.1 存款与自动债务结清 (`depositTo`)

这是最推荐的充值方式。

Bash

```
cast send $SYSTEM_CONTRACT_ADDR "depositTo(address)" $CONTRACT_ADDR \
  --rpc-url $REACTIVE_RPC \
  --private-key $REACTIVE_PRIVATE_KEY \
  --value 0.1ether
```

-   **参数解释**：
    
    -   `$SYSTEM_CONTRACT_ADDR`: 统一系统合约地址 `0x...fffFfF`。
        
    -   `"depositTo(address)"`: 调用函数名，指定要把钱存入哪个地址的名下。
        
    -   `$CONTRACT_ADDR`: 你的响应式合约地址。  
        
-   **为什么这么用？**
    
    -   系统合约维护了一个映射表（Mapping），记录了每个合约的 `reserves`（准备金）。
        
    -   调用 `depositTo` 会触发系统内部逻辑，自动对比 `debts`（债务），并**立即用新充入的钱抵消欠款**，使合约恢复 `active` 状态。  
        

### 3.2 手动清偿债务 (`coverDebt`)

如果你是直接通过 `transfer` 转账给合约地址，系统不会自动结账。

Bash

```
cast send $CONTRACT_ADDR "coverDebt()" \
  --rpc-url $REACTIVE_RPC \
  --private-key $REACTIVE_PRIVATE_KEY 
```

-   **为什么这么用？**
    
    -   Reactive 合约通常继承了 `AbstractPayer`。
        
    -   `coverDebt()` 的逻辑是：查看系统合约中记录的本合约债务 -> 从合约余额中划转等额代币给系统合约。  
        

### 3.3 债务与准备金查询

Bash

```
# 查询欠款
cast call $SYSTEM_CONTRACT_ADDR "debts(address)" $CONTRACT_ADDR --rpc-url $REACTIVE_RPC | cast to-dec

# 查询准备金
cast call $SYSTEM_CONTRACT_ADDR "reserves(address)" $CONTRACT_ADDR --rpc-url $REACTIVE_RPC | cast to-dec
```

-   **逻辑解释**：
    
    -   `cast call` 发起的是**只读调用**，不消耗 Gas。
        
    -   如果 `debts > reserves`，说明你的合约处于危险边缘，随时可能因为欠费被拉黑。  
        

## 🏛️ 4. 系统合约的“二位一体”设计

在官方文档中提到，系统合约和回调代理（Callback Proxy）共享同一个地址：`0x0000000000000000000000000000000000fffFfF`。

**为什么这么设计？**

1.  **简化开发**：开发者只需要记住一个地址，就能同时处理“订阅管理”和“资金结算”。
    
2.  **原子化结算**：当回调代理在目标链执行指令产生费用时，它可以直接访问同一个合约内的账本数据，实时更新债务，避免了跨合约调用的额外延迟。
    

## 💡 总结：自动化运行的“油耗”管理

-   **RVM 层面**：你的 `react()` 逻辑越复杂，执行费越高（最高 900k Gas 封顶）。
    
-   **Callback 层面**：目标链越拥堵，你的跨链成本越高。
    
-   **运维逻辑**：通过 `depositTo` 保持 `reserves > debts` 是确保你的跨链机器人 7x24 小时不断电运行的唯一准则。
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->



完整实战代码：去中心化止损机器人 (`UniswapDemoStopOrderReactive.sol`)

Solidity

```
// SPDX-License-Identifier: MIT
pragma solidity >=0.8.0;

// 引入 Reactive Network 的官方基础库
import '../../../lib/reactive-lib/src/interfaces/IReactive.sol';
import '../../../lib/reactive-lib/src/abstract-base/AbstractReactive.sol';

// 定义 Uniswap V2 的资金池储备量结构体，用于解码 Sync 事件的数据
struct Reserves {
    uint112 reserve0;
    uint112 reserve1;
}

contract UniswapDemoStopOrderReactive is IReactive, AbstractReactive {
    
    // ==========================================
    // 📢 事件声明 (Events)：用于在区块链浏览器上留下运行痕迹
    // ==========================================
    event Subscribed(address indexed service_address, address indexed _contract, uint256 indexed topic_0);
    event VM();
    event AboveThreshold(uint112 indexed reserve0, uint112 indexed reserve1, uint256 coefficient, uint256 threshold);
    event CallbackSent();
    event Done();

    // ==========================================
    // 🪨 常量区 (Constants)：硬编码的网络参数与事件指纹
    // ==========================================
    uint256 private constant SEPOLIA_CHAIN_ID = 11155111; // 目标链：Sepolia 测试网
    
    // Uniswap V2 "Sync(uint112,uint112)" 事件的哈希指纹
    uint256 private constant UNISWAP_V2_SYNC_TOPIC_0 = 0x1c411e9a96e071241c2f21f7726b17ae89e3cab4c78be50e062b03a9fffbbad1;
    
    // 目标链执行合约 "Stop(...)" 事件的哈希指纹
    uint256 private constant STOP_ORDER_STOP_TOPIC_0 = 0x9996f0dd09556ca972123b22cf9f75c3765bc699a1336a85286c7cb8b9889c6b;
    
    uint64 private constant CALLBACK_GAS_LIMIT = 1000000; // 跨链回调的最大 Gas 限制

    // ==========================================
    // 🧠 状态变量区 (State)：狙击手的记忆库 (在 RVM 沙盒中维护)
    // ==========================================
    bool private triggered;      // 保险栓 1：是否已经开过枪 (发起过回调)
    bool private done;           // 保险栓 2：目标是否已被确认击毙 (任务彻底结束)
    address private pair;        // 盯盘目标：Uniswap 资金池地址
    address private stop_order;  // 协同队友：目标链上负责真正卖币的合约地址
    address private client;      // 老板：委托此止损单的用户地址
    bool private token0;         // 方向：我们要卖的是 token0 还是 token1？
    uint256 private coefficient; // 精度放大器：用于处理价格的小数计算
    uint256 private threshold;   // 扳机底线：老板设定的止损价格阈值

    // ==========================================
    // 🛠️ 初始化与订阅 (Constructor)：戴上监控眼镜
    // ==========================================
    constructor(
        address _pair,
        address _stop_order,
        address _client,
        bool _token0,
        uint256 _coefficient,
        uint256 _threshold
    ) payable {
        // 1. 记录老板交代的任务参数
        triggered = false;
        done = false;
        pair = _pair;
        stop_order = _stop_order;
        client = _client;
        token0 = _token0;
        coefficient = _coefficient;
        threshold = _threshold;

        // 2. 环境判断：如果当前是在 RN 主网 (!vm)
        if (!vm) {
            // 任务 A：死盯 Uniswap 池子的价格变化 (Sync)
            service.subscribe(
                SEPOLIA_CHAIN_ID,
                pair,
                UNISWAP_V2_SYNC_TOPIC_0,
                REACTIVE_IGNORE, REACTIVE_IGNORE, REACTIVE_IGNORE
            );
            // 任务 B：死盯队友是否完成了卖币操作 (Stop)
            service.subscribe(
                SEPOLIA_CHAIN_ID,
                stop_order,
                STOP_ORDER_STOP_TOPIC_0,
                REACTIVE_IGNORE, REACTIVE_IGNORE, REACTIVE_IGNORE
            );
        }
    }

    // ==========================================
    // ⚡ 核心大脑 (React)：RVM 沙盒里的自动触发逻辑
    // ==========================================
    function react(LogRecord calldata log) external override vmOnly {
        // 如果任务早就彻底完结了，直接忽略后续的所有噪音
        assert(!done);

        // 🌟 分支 1：如果事件来自队友 (目标链的 stop_order 合约)
        if (log._contract == stop_order) {
            // 确认：我已经开过枪了(triggered) + 确实是Stop事件 + 确实是这个池子 + 确实是这个老板
            if (
                triggered &&
                log.topic_0 == STOP_ORDER_STOP_TOPIC_0 &&
                log.topic_1 == uint256(uint160(pair)) &&
                log.topic_2 == uint256(uint160(client))
            ) {
                // 确认击杀，任务彻底终结！打卡下班。
                done = true;
                emit Done();
            }
        } 
        // 🌟 分支 2：如果事件来自盯盘目标 (Uniswap 资金池)
        else {
            // 1. 从事件的附加数据(data)中解码出池子最新的资金储备量
            Reserves memory sync = abi.decode(log.data, ( Reserves ));
            
            // 2. 战术判断：价格是否跌破了底线？ AND 我的枪膛里还有子弹吗(未触发过)？
            if (below_threshold(sync) && !triggered) {
                emit CallbackSent(); // 记录日志
                
                // 3. 打包指令：告诉队友 (stop_order) 各种参数，准备按计划执行卖币
                // 注意第一个参数是 address(0)，底层会自动替换为你的 RVM ID 确保安全
                bytes memory payload = abi.encodeWithSignature(
                    "stop(address,address,address,bool,uint256,uint256)",
                    address(0), pair, client, token0, coefficient, threshold
                );
                
                // 4. 拉下保险栓：我已经开枪了，不管以太坊再怎么暴跌，别让我开第二枪 (保证幂等性)
                triggered = true;
                
                // 5. 砰！发射跨链指令！(目标链 ID, 队友地址, Gas 限制, 指令数据)
                emit Callback(log.chain_id, stop_order, CALLBACK_GAS_LIMIT, payload);
            }
        }
    }

    // ==========================================
    // 🧮 数学引擎：价格判断逻辑
    // ==========================================
    function below_threshold(Reserves memory sync) internal view returns (bool) {
        // 利用交叉相乘，完美避开 Solidity 无法计算小数的缺陷
        if (token0) {
            // 相当于：(reserve1 / reserve0) * 放大系数 <= 止损阈值
            return (sync.reserve1 * coefficient) / sync.reserve0 <= threshold;
        } else {
            // 相当于：(reserve0 / reserve1) * 放大系数 <= 止损阈值
            return (sync.reserve0 * coefficient) / sync.reserve1 <= threshold;
        }
    }
}
```

### 💡 业务逻辑全景复盘

1.  **部署时 (Deploy)**：管理员把 Uniswap 池子地址、目标链操作合约地址、止损价格写进合约。主网自动记录下监听规则。
    
2.  **蛰伏期 (Standby)**：合约静静地呆在 Reactive Network 上，不消耗任何主网 Gas。
    
3.  **危机降临 (Trigger)**：有人在 Uniswap 砸盘。协议层瞬间捕捉到 `Sync` 事件，在 RVM 内存中克隆出你的合约并执行 `react()`。
    
4.  **精确计算 (Math)**：`below_threshold()` 发现价格确实跌破了止损线。
    
5.  **跨链制裁 (Callback)**：RVM 立刻发出 `Callback` 指令，并把 `triggered` 设为 `true`，防止重复发单。
    
6.  **落幕 (Done)**：目标链上的真实合约收到指令，卖掉用户的币止损成功，发出 `Stop` 事件。RVM 再次被唤醒，验证无误后将 `done` 设为 `true`。任务完美结束。
    

## 🎯 总结：全自动化闭环工作流 (Execution Flow)

1.  **部署 (Initialization)**: 合约在 RN 注册，开始监听 Uniswap 资金池。
    
2.  **盯盘 (Event Monitoring)**: 有人在 Uniswap 砸盘，池子抛出 `Sync` 事件。
    
3.  **判断 (React & Math)**: RVM 瞬间苏醒，解码 `Sync` 数据，发现价格触发了 `threshold`。
    
4.  **行动 (Callback)**: RVM 发出 Callback 并将 `triggered` 设为 `true`。
    
5.  **执行 (Destination Chain)**: Reactive 代理在目标链执行真实的 `stop` 卖出代币操作。
    
6.  **归档 (Completion)**: 目标链卖出成功抛出 `Stop` 事件，RVM 再次苏醒，将 `done` 设为 `true`，机器人完美下班。
    

# hyperlane机制

\*\*路由（Routing）与解耦（Decoupling）\*\*问题！

这两个问题非常硬核，直接决定了你的跨链 dApp 架构走向。我给你一个明确的答案：

1.  **Hyperlane 的底层中继（Relayer）是个“死脑筋”，它到达目标链后，绝对不会去调用你业务逻辑里的** `swap`**、**`mint` **或** `stop` **函数。它唯一认识、且只会调用的函数，就只有** `handle`**。**
    
2.  `handle` **函数绝对不需要、也不应该总是写在你的核心业务合约内部！在真实生产环境中，我们通常会引入一个“网关（Gateway）”模式。**
    

* * *

### 🏛️ 模式一：单体架构 (Integrated) —— 适合全新开发的小项目

在这种模式下，你的业务合约自己兼任了“收发室大爷”。

-   **逻辑**：`handle` 函数直接写在你的业务合约（比如一个 NFT 铸造合约）内部。
    
-   **流程**：Mailbox 调用你合约的 `handle` -> `handle` 函数验证发件人 -> 验证通过后，在 `handle` 函数的代码**内部**，直接去修改合约的状态（比如 `balances[user] += 100`）。
    
-   **缺点**：代码高度耦合。如果你的业务合约非常复杂，把跨链验证逻辑也塞进去，会让合约显得非常臃肿。
    

* * *

### 🚪 模式二：网关/代理架构 (Gateway / Router Pattern) —— 行业绝对主流

想象一下，如果你想用 Reactive 合约，跨链去调用目标链上的 **Uniswap V2 Router** 来买币。

**大问题来了：Uniswap 的合约是几年前部署的，代码早就死锁了，里面根本没有** `handle` **函数！** Mailbox 怎么可能把信送给它？

这时候，就必须使用**网关架构**。你需要专门写一个全新的合约，充当“收发室前台（Gateway）”。

运作流程：

1.  **收信**：目标链上的 Hyperlane Mailbox 找到你的 **Gateway 合约**，并调用它的 `handle` 函数。
    
2.  **拆信与分发 (Dispatch)**：Gateway 合约的 `handle` 验证完安全性后，拆开信件（解码 `_message`）。发现信里写着：“请去 Uniswap 帮我买 1 个 ETH”。
    
3.  **本地调用**：Gateway 合约作为以太坊上的一个普通本地用户，转身去调用 Uniswap 的 `swapExactTokensForTokens` 函数。
    
4.  **完成**：Uniswap 执行交易，完全不知道、也不需要知道这笔交易其实是别的链遥控过来的。
    

💻 核心代码演示：网关模式 (CrossChainGateway.sol)

Solidity

```
// 这是部署在目标链上的网关合约，专门负责收信并指挥其他合约
contract CrossChainGateway is IMessageRecipient {
    address public immutable mailbox;
    address public immutable myReactiveContract;

    constructor(...) { ... }

    // Hyperlane 唯一认识的入口
    function handle(
        uint32 _origin,
        bytes32 _sender,
        bytes calldata _message
    ) external override {
        // 1. 严格的安全校验 (防线)
        require(msg.sender == mailbox, "Only Mailbox");
        require(address(uint160(uint256(_sender))) == myReactiveContract, "Wrong Sender");

        // 2. 动态路由解析 (魔法在这里！)
        // 假设我们在 RC 发信时，把想调用的目标合约地址、想调用的函数签名和参数，全部打包进了 _message
        (
            address targetBusinessContract, // 真正要干活的合约 (比如 Uniswap)
            bytes memory functionCallData   // 真正要执行的函数及参数 (比如 swap...)
        ) = abi.decode(_message, (address, bytes));

        // 3. 执行本地调用 (Low-level call)
        // Gateway 合约代替 RC，向真正的业务合约发起调用
        (bool success, ) = targetBusinessContract.call(functionCallData);
        
        // 4. 确认结果
        require(success, "Business transaction failed on target chain!");
    }
}
```

* * *

### 💡 核心总结：什么是“跨链调用特定的函数”？

回到你的第一个问题。由于 Mailbox 只能调 `handle`，我们是怎么做到\*\*“回调目标链上特定合约的特定函数”\*\*的呢？

答案是：**Payload (载荷) 嵌套编码。**

你在 Reactive 合约（发送端）构建 `Callback` 的时候，其实是做了两层打包：

1.  **内层打包**：`abi.encodeWithSignature("swap(...)")`，这串二进制数据是给 Uniswap 看的。
    
2.  **外层打包**：把 Uniswap 的地址和内层数据打包在一起，塞进给 Hyperlane 的信封（`_message`）里。
    

目标链上的 **Gateway 合约的** `handle` **函数**，唯一的工作就是撕开外层信封，掏出内层的调用指令，然后像一个无情的机器人一样，把指令原封不动地 `call` 给最终的目标合约。
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->






这是一份为你系统性整理的 **Reactive Network 全栈开发与核心机制** 的 Obsidian 学习笔记。

这份笔记将你提供的官方文档、GitHub 代码库以及我们之前的讨论进行了深度整合，完美覆盖了你的四大核心学习目标。你可以直接将其复制到你的 Obsidian 知识库中。

* * *

## 🎯 目标一：理解 Reactive Contract 的组成结构

传统的智能合约是“被动”的（依赖用户发交易），而响应式合约（Reactive Contract, RC）是“主动”的（由事件驱动）。RC 的代码结构天然反映了这种“控制反转（Inversion of Control）”。

### 核心组成部分

1.  **基础依赖**：必须引入并继承官方的 `IReactive` 接口和 `AbstractReactive` 抽象类。
    
2.  `constructor` **(初始化区)**：用于在部署时设定基础参数，并向系统**注册订阅规则**。
    
3.  `react()` **(逻辑响应区)**：RC 的灵魂。当订阅的事件发生时，底层系统会自动调用这个函数，传入详尽的事件快照 `LogRecord`。
    
4.  **环境修饰符**：利用 `rnOnly` 和 `vmOnly` 严格隔离代码的执行环境。
    

Solidity

```
import '../../../lib/reactive-lib/src/interfaces/IReactive.sol';
import '../../../lib/reactive-lib/src/abstract-base/AbstractReactive.sol';

contract MyReactiveContract is IReactive, AbstractReactive {
    // 1. RN 变量区 (面向主网管理)
    // 2. RVM 变量区 (面向逻辑处理与状态标记)

    constructor(...) {
        if (!vm) {
            // 在 RN 主网执行：向系统注册订阅
        }
    }

    function react(LogRecord calldata log) external override vmOnly {
        // 在 RVM 沙盒执行：事件处理逻辑与跨链回调
    }
}
```

* * *

## 🔄 目标二：理解 Subscribe / Trigger / Callback 模型

这是 Reactive Network 运转的底层逻辑飞轮，也是跨链自动化的核心生命周期。

1.  **Subscribe (订阅)**
    
    -   **作用**：告诉网络“我关心哪些链上的哪些事件”。
        
    -   **实现**：通过系统合约的 `service.subscribe(...)`，支持多维度过滤（Chain ID, Contract Address, Topic 0-3）。
        
    -   **规则**：允许设置通配符（如 `address(0)` 或 `REACTIVE_IGNORE`），但越精准越省计算资源。
        
2.  **Trigger (触发)**
    
    -   **作用**：去中心化节点集群扫描各条源链。当发现匹配的 `Event` 日志时，生成 `LogRecord` 结构体。
        
    -   **机制**：协议层在内存中瞬间拉起一个 ReactVM 实例，并将 `LogRecord` 注入，强制触发 `react()` 函数。
        
3.  **Callback (回调)**
    
    -   **作用**：RVM 在沙盒中计算出结果后，必须通过 `emit Callback(...)` 向外部世界发送执行指令。
        
    -   **安全机制**：在发往目标链之前，Payload 的第一个参数会被**强制替换为合约部署者的 RVM ID**，确保指令不可伪造。
        

* * *

## 🧠 目标三：理解 ReactVM 执行逻辑 (双状态模型)

> \[!abstract\] **Dual-State Model (双状态模型)**
> 
> 为什么响应式网络不拥堵？因为它把“行政管理”和“具体干活”分开了。同一份代码，存在于两个平行时空。

| 维度 | Reactive Network (RN) 状态 | ReactVM (RVM) 状态 |
| 定位 | 全局主网账本 (公共环境) | 私有执行沙盒 (隔离环境) |
| 功能 | 记录订阅关系、扣除 Gas 费 | 并发执行 react() 业务逻辑 |
| 识别机制 | detectVm() 检测到系统合约存在 | detectVm() 检测到系统合约不存在 |
| 状态持久化 | 永久记录，多方共识 | 实例私有，阅后即焚（不回传主网） |
| 修饰符控制 | rnOnly | vmOnly |

**⚠️ 避坑点**：永远不要试图在 `react()` (RVM环境) 中直接调用 `subscribe`，因为沙盒里没有系统合约。动态修改订阅必须通过 Callback“自己调自己”回主网执行。

* * *

## 🌐 目标四：理解跨链、自动化与经济机制 (Economy)

RC 使得智能合约不仅能感知外部世界，还能跨越链与链的边界。

### 1\. 跨链自动化机制

-   **源链 (Origin Chain)**：发生原始业务（如 Ethereum 上的 Uniswap 交易），抛出日志。
    
-   **中继 (Reactive Network)**：捕获日志，拉起 RVM 计算判断，打包跨链指令（Payload）。
    
-   **目标链 (Destination Chain)**：由官方的 `Callback Proxy` 代理合约发起真正的交易，执行用户的目标逻辑（如在 Polygon 上空投）。
    

### 2\. 经济模型 (Economy): 谁来付 Gas？

既然跨链回调是后台全自动执行的，没有用户来签名付 Gas 怎么办？

> \[!info\] `IPayer` **与预充值机制**
> 
> -   开发者需要在 Reactive Network 上为自己的合约\*\*预充值（Top-up）\*\*原生代币。
>     
> -   当 RVM 发出 Callback，底层的 Relayers (中继者) 帮你在目标链垫付 Gas 费把交易打上链。
>     
> -   随后，系统会根据目标链消耗的实际成本，从你在 RN 的预充值余额中自动扣款。如果余额耗尽，回调将停止发送。
>     

* * *

## 🛠️ 实战解析：Uniswap V2 止损脚手架 (Stop Order Demo)

参照 [GitHub 官方示例](https://github.com/Reactive-Network/reactive-smart-contract-demos/tree/main/src/demos/uniswap-v2-stop-order)，我们可以将理论落地：

1.  **监控阶段**：构造函数在 RN 主网 (`!vm`) 订阅目标 Uniswap 池子的 `Sync` 事件和目标链执行合约的 `Stop` 成功事件。
    
2.  **运算阶段**：在 RVM 中，解析 `Sync` 事件带出的 `reserve0` 和 `reserve1`，利用交叉相乘法计算当前价格是否低于止损线 (`threshold`)。
    
3.  **防重机制 (Idempotency)**：使用 `triggered` 布尔变量。只要触发过一次 Callback，立刻将 `triggered` 设为 true，防止因价格连续波动导致向目标链狂发数百笔重复指令。
    
4.  **终结阶段**：收到目标链返回的 `Stop` 事件，将 `done` 设为 true，机器人完美下班。
    

* * *
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->







# Uniswap V2 核心机制与响应式止损单 (Stop-Order)

## 🌟 核心理念 (Overview)

要编写一个能在去中心化交易所（DEX）上自动执行交易的机器人，首先必须彻底弄懂 **Uniswap V2** 的底层数学模型和事件机制。结合 Reactive Contracts (RC)，我们可以将“被动”的 Uniswap 变成一个支持“条件触发（如止损/止盈）”的现代化自动交易引擎。

* * *

## 🦄 第一部分：Uniswap V2 运行机制 (Lesson 6)

Uniswap V2 抛弃了传统的“订单薄（Order Book）”模型，采用了 **自动化做市商 (AMM)** 机制。其核心是由两个代币组成的**流动性池 (Liquidity Pools)**。

### 1\. 核心算法：恒定乘积公式 (Constant Product Formula)

> \[!info\] **$x \* y = k$**
> 
> -   $x$ 和 $y$ 分别代表资金池中两种代币的储备量（Reserves）。
>     
> -   $k$ 是一个常数。
>     
> -   **核心法则**：无论发生什么交易，交易后的 $x$ 和 $y$ 的乘积必须等于（或大于，因为有手续费）交易前的 $k$。这使得代币价格会根据资金池中代币的稀缺程度自动浮动。
>     

### 2\. `swap()` 函数的工作流

当你在 Uniswap 上用 Token A 换 Token B 时，智能合约底层执行了以下严格的校验：

1.  **输入输出检查**：确保你要换出的数量 `amountOut` 大于 0，且不掏空池子。
    
2.  **计算真实输入**：通过池子余额的差值，计算出你实际打进来的 Token A 数量 `amountIn`。
    
3.  **扣除手续费并校验 $k$ 值**：扣除 0.3% 的千分之三手续费后，强制要求新的 $x \* y \\ge k$。
    
4.  **更新与转账**：调用 `_update` 刷新池子储备，并将 Token B 转给你。
    
5.  **发出事件**：`emit Swap(...)`。
    

### 3\. RC 开发者的“雷达”：Swap 与 Sync 事件

对于响应式合约来说，我们不关心代码怎么跑，我们只关心**链上留下了什么日志（Events）**。

-   `Swap` **事件**：记录了\*\*“谁”**用**“多少 A”**换了**“多少 B”\*\*。
    
-   `Sync` **事件**：记录了交易发生后，池子里\*\*“最新的储备量 (reserve0, reserve1)”\*\*。
    

> \[!tip\] **为什么 RC 更喜欢监听** `Sync` **事件？**
> 
> 因为 $x \* y = k$ 模型中，**价格 = reserve0 / reserve1**。监听 `Sync` 事件，RC 就能在每次交易发生后，瞬间计算出该交易对的**最新精确价格**，这正是触发“止损”的最佳信号！

* * *

## 🤖 第二部分：响应式止损合约实战 (Lesson 7)

我们现在来拆解 `UniswapDemoStopOrderReactive` 这个合约。它的目标是：**紧盯某个 Uniswap 池子的** `Sync` **事件，一旦价格跌破阈值，立即自动发起一笔卖出交易（止损）。**

### 1\. 合约状态变量 (State Variables)

回顾我们学过的**双状态 (Dual-State)**，这个合约的数据分为两类：

-   **常量 (Constants)**：`SEPOLIA_CHAIN_ID`，以及预先计算好的 `UNISWAP_V2_SYNC_TOPIC_0` 和 `STOP_ORDER_STOP_TOPIC_0`（事件签名的哈希）。
    
-   **RVM 私有状态 (RVM Variables)**：
    
    -   `triggered` (布尔值)：**极其重要！** 用于防止价格暴跌时，合约在几毫秒内发出一百次回调指令。
        
    -   `done` (布尔值)：标记整个止损流程是否彻底完结。
        
    -   业务参数：`pair` (池子地址), `client` (用户), `threshold` (止损阈值), `coefficient` (精度系数)。
        

### 2\. 初始化与订阅 (Constructor)

部署合约时，配置业务参数，并在 **RN 主网 (**`!vm`**)** 上向系统申请订阅两个事件：

1.  监听 Uniswap 池子的 `Sync` 事件（盯盘）。
    
2.  监听目标链止损合约的 `Stop` 事件（确认交易是否成功）。
    

### 3\. 核心大脑：`react()` 函数逻辑

这是运行在 RVM 沙盒里的核心调度中心：

Solidity

```
function react(LogRecord calldata log) external vmOnly {
    assert(!done); // 如果已经彻底完结，直接拦截

    // 🌟 分支 1：如果是目标链发来的 "已成功止损" 事件
    if (log._contract == stop_order) {
        // 校验是不是真的成功了
        if (triggered && log.topic_0 == STOP_ORDER_STOP_TOPIC_0 && ...) {
            done = true; // 彻底关闭流程
            emit Done();
        }
    } 
    // 🌟 分支 2：如果是 Uniswap 资金池发来的 "价格更新(Sync)" 事件
    else {
        // 解析出池子的最新储备量
        Reserves memory sync = abi.decode(log.data, (Reserves));
        
        // 如果价格低于阈值 (below_threshold) 且 尚未触发过 (!triggered)
        if (below_threshold(sync) && !triggered) {
            
            // 1. 打包目标链的执行指令 (调用 stop 函数)
            bytes memory payload = abi.encodeWithSignature(
                "stop(address,address,address,bool,uint256,uint256)",
                address(0), pair, client, token0, coefficient, threshold
            );
            
            // 2. 锁死触发器，保证幂等性 (Idempotency)
            triggered = true;
            
            // 3. 发出 Callback 跨链指令
            emit Callback(log.chain_id, stop_order, CALLBACK_GAS_LIMIT, payload);
        }
    }
}
```

### 4\. 数学判定：`below_threshold()`

智能合约不支持浮点数，所以在计算价格 $P = r\_1 / r\_0$ 是否小于阈值 $T$ 时，通常采用**交叉相乘**的方法避免小数：

Solidity

```
function below_threshold(Reserves memory sync) internal view returns (bool) {
    if (token0) {
        // 本质是：(reserve1 / reserve0) * coefficient <= threshold
        // 转换为乘法避免精度丢失：
        return (sync.reserve1 * coefficient) / sync.reserve0 <= threshold;
    } else {
        return (sync.reserve0 * coefficient) / sync.reserve1 <= threshold;
    }
}
```

* * *

## 🎯 总结：全自动化闭环工作流 (Execution Flow)

1.  **部署 (Initialization)**: 合约在 RN 注册，开始监听 Uniswap 资金池。
    
2.  **盯盘 (Event Monitoring)**: 有人在 Uniswap 砸盘，池子抛出 `Sync` 事件。
    
3.  **判断 (React & Math)**: RVM 瞬间苏醒，解码 `Sync` 数据，发现价格触发了 `threshold`。
    
4.  **行动 (Callback)**: RVM 发出 Callback 并将 `triggered` 设为 `true`。
    
5.  **执行 (Destination Chain)**: Reactive 代理在目标链执行真实的 `stop` 卖出代币操作。
    
6.  **归档 (Completion)**: 目标链卖出成功抛出 `Stop` 事件，RVM 再次苏醒，将 `done` 设为 `true`，机器人完美下班。
    

> \[!success\] **进阶思考**
> 
> 传统 Web2 的量化机器人需要一台中心化服务器 24 小时开机通过 API 轮询价格，一旦宕机就面临爆仓风险。而这个 Reactive Contract 完全运行在去中心化网络上，只要以太坊在出块，你的止损逻辑就绝对不会失效。这就是 Web3 自动化的终极魅力！
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->








## 🌟 核心理念 (Overview)

虽然响应式合约 (RC) 非常擅长监听**链上 (On-chain)** 事件，但区块链本身是一个封闭的、确定性的系统，它无法直接知道今天的以太坊价格或是明天的天气。 **预言机 (Oracles)** 就是解决这个问题的特殊类别：它们负责将**链下数据 (Off-chain data)** 搬运到区块链上。当 RC 结合了预言机抛出的事件时，智能合约就真正拥有了感知并响应“现实世界”的能力。

* * *

## 🛑 1. 预言机难题 (The Oracle Problem)

**什么是预言机难题？** 智能合约的运行必须是**确定性 (Deterministic)** 和可验证的。如果每个节点在执行合约时都自己去请求一次外部 API 获取价格，由于网络延迟或 API 变动，节点之间会得出不同的结果，从而导致**共识崩溃**。 因此，区块链**不能**主动向外请求数据，只能等待外部把数据“喂”进来。

### 预言机如何解决这个问题？

预言机作为“数据搬运工”，充当区块链与外部世界（金融市场 API、政府数据库、物联网设备等）的桥梁。

-   **信任机制**：为了防止单点故障或恶意篡改，主流预言机（如 Chainlink, Band Protocol）采用**去中心化预言机网络 (DON)** 和**多重签名 (Multisig)** 协议。
    
-   **运作方式**：多个节点在链下获取数据并达成共识后，使用预言机服务商的私钥对数据进行签名，然后将确定的结果打包成一笔交易，写进区块链的状态中（或抛出事件）。  
    

## 💼 2. 预言机的实际应用场景

预言机解锁了智能合约的无限可能：

1.  **DeFi (去中心化金融)**：读取代币价格源（Price Feeds）来执行清算、调整借贷利率或进行资产兑换。
    
2.  **保险 (Insurance)**：根据可验证的现实事件（如航班延误、自然灾害）自动触发理赔。
    
3.  **在线博彩 (Online Betting)**：将现实体育比赛的结果安全地输入到去中心化的博彩合约中以分配奖金。
    

## 💻 3. 传统合约的痛点：只能“被动读取”

在传统 EVM 开发中，你可以像下面这样通过 Chainlink 获取以太坊的价格：

**致命缺陷：缺乏实时反应能力** 在上面的代码中，数据确实在链上了。**但是，合约并不会自己去读它！** 只有当一个真实的外部账户 (EOA，即用户钱包) 发起一笔交易，主动调用 `getLatestPrice()` 时，合约才能拿到这个价格。如果价格在一秒钟内暴跌，而用户没有及时手动发送交易，合约就会毫无反应。

## ⚡ 4. 终极协同：预言机 + 响应式合约 (RC)

这就是为什么我们需要 **Reactive Contracts (RC)**，以及我们之前反复强调的**控制反转 (Inversion of Control)** 原则。

-   **传统模式 (预言机 + 普通合约)**：预言机更新了价格 -> 价格静静地躺在链上 -> 用户手动发交易 -> 合约执行（**慢且依赖人工**）。
    

Solidity

```
pragma solidity ^0.8.0;

import "@chainlink/contracts/src/v0.6/interfaces/AggregatorV3Interface.sol";

contract PriceConsumerV3 {

    AggregatorV3Interface internal priceFeed;

    /**
     * Network: Ethereum Mainnet
     * Aggregator: ETH/USD
     * Address: 0x... (Chainlink ETH/USD Price Feed Contract Address)
     */
    constructor() public {
        priceFeed = AggregatorV3Interface(0x...);
    }

    /**
     * Returns the latest price
     */
    function getLatestPrice() public view returns (int) {
        (
            /* uint80 roundID */,
            int price,
            /* uint startedAt */,
            /* uint timeStamp */,
            /* uint80 answeredInRound */
        ) = priceFeed.latestRoundData();
        return price;
    }
}

```

代码解释：

```
### 1. 编译版本与接口导入

- **`pragma`**：声明这段代码使用 0.8.0 或更高版本的 Solidity 编译器编译。
    
- **`import`**：这是关键。智能合约想要调用另一个合约（这里是 Chainlink 的官方喂价合约），必须知道对方长什么样（有哪些函数、返回值是什么）。`AggregatorV3Interface.sol` 就是 Chainlink 提供的一份“API 说明书”（接口）。
    

### 2. 状态变量声明

- 这里声明了一个名为 `priceFeed` 的状态变量，它的类型就是我们刚刚导入的 `AggregatorV3Interface`。
    
- `internal` 意味着这个变量只能在当前合约内部使用，外部用户无法直接读取它。你可以把它理解为**“一部专门用来给 Chainlink 打电话的专线座机”**。
    

### 3. 构造函数 (初始化)

- **`constructor`**：这是合约部署时**只执行一次**的初始化函数。（_注：在 0.8.0 以上的 Solidity 中，构造函数不需要写 `public`，这里算是老版本代码留下的一点小习惯。_）
    
- **绑定地址**：`0x...` 应该是 Chainlink 官方在以太坊主网上部署的 ETH/USD 喂价合约的具体地址（比如以太坊主网上是 `0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419`）。这行代码的作用是把你的“专线座机”插上特定的电话线，明确告诉它去找谁要数据。
    

### 4. 核心功能：获取最新价格

- **`public view`**：`public` 表示任何人都可以调用这个函数；`view` 表示这个函数**只读取数据，不修改区块链状态**。因此，如果你在链下（比如在前端页面）调用它，是**完全免费、不消耗 Gas 的**。
    
- **`priceFeed.latestRoundData()`**：你拿起座机按下了查询键。Chainlink 的合约会返回一组数据（共 5 个返回值）。
    
- **“逗号占位法”（元组解构）**：这是 Solidity 的特色语法。因为 `latestRoundData()` 返回 5 个值（轮次ID、价格、开始时间、时间戳、回答轮次），但你的业务逻辑**只需要 `price`（价格）**。为了节省变量声明带来的 Gas 消耗，代码中用 `/* 注释 */,` 的方式把不需要的返回值直接留空忽略掉了，只把第二个返回值赋给了 `price` 变量。
```

-   **响应模式 (预言机 + RC)**：预言机更新价格时，通常会 `emit` 一个类似 `PriceUpdated` 的事件 -> **Reactive Network 瞬间监听到该事件** -> 自动唤醒 ReactVM -> 执行你的 `react()` 逻辑 -> 抛出 Callback 自动执行止损/清算（**全自动化、毫秒级响应**）。  
    

**核心结论 (The Synergy)** 预言机负责**“将现实变成链上事件”**，RC 负责**“对链上事件做出实时行动”**。这两者的结合，彻底打破了智能合约必须由用户（EOA）手动触发的限制，开启了 Web3 全自动化的新纪元。

## 💡 总结与要点

1.  **预言机**是打破区块链信息孤岛的唯一安全途径。
    
2.  预言机通过**去中心化网络和多重签名**保障数据的真实与不可篡改。
    
3.  传统合约调用预言机依然受限于 EOA 手动触发的瓶颈。
    
4.  **预言机事件 + RC 订阅机制** 是实现诸如“全自动链上止损”、“自动化组合投资”的核心技术栈。
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->










## 1\. ReactVM：响应式合约的“私人大脑”

在响应式网络中，**ReactVM** 是专门为执行响应式合约（RC）设计的 EVM 环境。它与传统以太坊环境最大的区别在于其\*\*双状态（Dual-State）\*\*特性：

-   **隔离的私有空间**：当你部署一个 RC 时，它会被分配到一个专属的 ReactVM 中，该 VM 的地址与你的部署者钱包地址（EOA）完全一致。
    
-   **双状态环境**：
    
    -   **Reactive Network State (外部)**：处理管理功能，如暂停订阅（`pause()`）或更新合约配置。
        
    -   **ReactVM State (内部)**：存放业务逻辑，当订阅的事件发生时，在这里执行具体的响应代码。
        
-   **高并发处理**：ReactVM 支持跨线程并行处理不同用户的合约，大幅提升了网络吞吐量。
    

## 2\. 订阅机制 (Subscriptions)：精准捕捉信号

要让 RC 动起来，你必须告诉网络你对哪些“信号”感兴趣。

-   **如何设置**：通常在合约的 `constructor()` 中通过调用系统合约的 `subscribe()` 方法来完成初始化。
    
-   **过滤准则 (Criteria)**：
    
    -   **Chain ID**：来源链的标识。
        
    -   **Contract Address**：发出事件的合约地址（设为 `address(0)` 可监听全网同类事件）。
        
    -   **Topics (0-3)**：事件的特征哈希。你可以使用 `REACTIVE_IGNORE` 来忽略特定的 Topic。
        
-   **动态调整**：如果需要在运行时增减监听对象，必须通过 **Callback** 机制向系统合约发送指令，因为 ReactVM 内部无法直接修改外部的订阅表。
    

## 3\. 预言机 (Oracles)：打破区块链的“封闭性”

预言机是连接区块链与现实世界的桥梁。

-   **RC 的绝佳拍档**：预言机将价格、天气等数据以“事件”的形式抛出，而 RC 天生就是为了监听事件而生的。
    
-   **实战场景**：例如 Chainlink 抛出一个 `PriceUpdated` 事件，你的 RC 监听到该事件后，可以自动判断是否触发止损订单。这种结合让智能合约真正具备了“感知现实并自动决策”的能力。
    

* * *

## 4\. Module 2 展望：走进 DeFi 实战

进入第二模块，学习重点将转向更具挑战性的 **去中心化金融 (DeFi)** 应用。

-   **Uniswap V2 深度拆解**：学习流动性池的工作机制及其实际合约操作。
    
-   **复杂自动化逻辑**：
    
    -   **止损订单 (Stop Orders)**：在 Uniswap 资金池上实现自动化交易。
        
    -   **跨链资产同步**：当用户在 A 链操作时，RC 自动在 B 链做出相应调整。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->











## 🌟 一、 范式转移：什么是响应式智能合约 (RSC)?

传统的智能合约（如你写的 `HeroNFT`）是**被动**的。它们像一个锁着的箱子，只有当用户通过交易（Transaction）去“点”它时，它才会运行。

**响应式智能合约 (RSCs)** 代表了一种范式转移：

-   **自主性 (Autonomous)**：它们不依赖用户手动触发，而是主动监测 EVM 兼容链上的**事件 (Events)** 并做出反应。
    
-   **全链感知**：它们能监听多个区块链的日志，根据预设逻辑处理后，自主在其他链执行动作。
    
-   **去中心化自动化**：无需中心化的“定时任务”脚本或机器人，直接在 Reactive Network 的去中心化网络中完成。
    

* * *

## 🛠️ 二、 开发 RSC 的前置知识 (Prerequisites)

在开始 Reactive 开发之前，你需要掌握以下技能点：

1.  **Solidity 核心语法**：熟悉 `mapping`, `struct`, `event` 等基础（参考之前的 `HeroNFT` 练习）。
    
2.  **EVM 运行原理**：理解交易签名、函数调用以及 Gas 机制。
    
3.  **Git 与 命令行**：用于本地环境配置和代码版本管理。
    
4.  **钱包与测试币**：例如使用 MetaMask 在 Sepolia 测试网领水和交互。
    
5.  **DeFi 基础概念**：理解流动性池、AMM（如 Uniswap）的工作原理，以便理解 RSC 在金融场景下的应用。
    

* * *

## 🏗️ 三、 核心架构：The Reactive Loop

RSC 的工作流程形成一个完整的自动化闭环：

1.  **源链事件 (Source Event)**：源链（如 Ethereum）合约 `emit` 一个事件。
    
2.  **监听与过滤 (Subscription)**：Reactive Network 监测并匹配订阅 criteria。
    
3.  **响应逻辑 (Reactive Logic)**：网络触发 RSC 的 `react()` 函数。
    
4.  **回调执行 (Callback)**：RSC 发出 `Callback` 事件，网络自动在目标链（如 Base）发起交易。
    

* * *

## 💻 四、 深入代码：`IReactive` 接口详解

所有的 RSC 必须实现 `IReactive` 接口，它是响应式逻辑的“合同”。

### 1\. 事件快照：`LogRecord` 结构体

当 Reactive 节点捕捉到事件，它会将以下信息“喂”给你的合约：

-   `chain_id`: 事件在哪条链发生的。
    
-   `_contract`: 哪个合约喊的话。
    
-   `topic_0`: **核心指纹**（事件名的哈希，如 `Transfer`）。
    
-   `topic_1`~`topic_3`: 索引后的参数（如 `tokenId`, `from`, `to`）。
    
-   `data`: 非索引的详细数据。
    
-   `tx_hash`: 用于回溯原始交易的唯一哈希。
    

### 2\. 执行入口：`react()` 函数

Solidity

```
function react(LogRecord calldata log) external;
```

这是你的“大脑”。你在这里写 `if (log.topic_0 == ...)` 来判断捕捉到的是不是你要找的那个“信号”。

### 3\. 指令发出：`Callback` 事件

Solidity

```
event Callback(uint256 indexed chain_id, address indexed _contract, uint64 indexed gas_limit, bytes payload);
```

当你在 `react()` 里跑完逻辑，需要去别的链干活时，你就 `emit Callback`。Reactive Network 看到这个事件，就会自动帮你在目标链下单。

* * *

## 🛡️ 五、 安全铁律：回调验证与地址替换

### 1\. 环境隔离

**规则**：如果起源地是主网，目的地也必须是主网。

-   原因：防止黑客在零成本的测试网上伪造“大额转账”信号，去主网套取真实资产。
    

### 2\. 地址替换规则 (Authorization)

为了确保安全，Reactive Network 在执行回调时有一条硬性规定：

-   **自动替换**：网络会自动将回调 `payload`（指令包）的前 160 位（第一个 address 参数）替换为该 RSC 部署者的地址（RVM ID）。
    
-   **代码实现**：这意味着你的目标链函数第一个参数**必须**是 `address` 类型，且接收到的是调用者的合法身份。
    

* * *

## 📝 六、 实战案例：自动止损 (Stop-Loss)

将以上知识串联起来。假设你要实现“当 Uniswap 价格跌破阈值时自动卖币”：

Solidity

```
// 在 RSC 的 react 函数中构建 payload
bytes memory payload = abi.encodeWithSignature(
    "stop(address,address,address,bool,uint256,uint256)",
    address(0),  // 占位符：网络会自动替换成合法的 ReactVM 地址
    pair,        // 监控的交易对
    client,      // 用户地址
    token0,      // 代币地址
    coefficient, // 策略参数
    threshold    // 价格阈值
);

// 发出回调指令
emit Callback(chain_id, stop_order_contract, CALLBACK_GAS_LIMIT, payload);
```

### 总结

1.  **Event** 是信号发射源。
    
2.  **IReactive** 是信号接收器。
    
3.  **react()** 是逻辑处理器。
    
4.  **Callback** 是最终执行指令。
    

Q：为什么普通合约不能起到RC一样的效果

A：

**1\. 监测层（Reactive Network 的“眼睛”）**

-   **动作：** 响应式网络（Reactive Network）在底层协议级不断地扫描所有连接的 EVM 链（如以太坊、BSC 等）发出的事件日志。
    
-   **区别：** 普通合约没有这种“眼睛”，它看不见外面的世界。而响应式网络会根据你合约里定义的订阅条件，主动把匹配的事件抓取回来。
    

### 2\. 执行层（ReactVM 的“大脑”）

-   **动作：** 一旦“眼睛”看到了你感兴趣的事件，网络会自动触发你合约中的 `react()` 函数。
    
-   **运行环境：** RC 并不是运行在普通的以太坊节点上，而是运行在 **ReactVM**（响应式虚拟机）中。
    
-   **关键点：** 这笔调用 `react()` 的交易是由**响应式网络节点**自动发起的，不需要你手动点钱包。
    

### 3\. 反馈层（Callback 的“手”）

-   **动作：** 当你在 `react()` 逻辑里执行完判断，发出 `emit Callback` 事件时，响应式网络会立刻捕捉到这个指令。
    
-   **执行：** 网络会自动跨链，在目标链（Destination Chain）上发起一笔真实的交易来执行最终结果（比如卖币或发空投）。
<!-- DAILY_CHECKIN_2026-03-10_END -->
<!-- Content_END -->
