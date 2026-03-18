---
timezone: UTC+8
---

# shell

**GitHub ID:** emptyshell424

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->
### 智能合约（Smart Contracts）

1\. Origin 链上的简单 Emit 合约（只负责 emit Event，确保 topic0 稳定 + indexed Origin）

部署在 **Origin 链**（Ethereum Sepolia 测试网，Chain ID 11155111）。 事件签名固定，topic0 永远不变，参数含 indexed origin。

solidity

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract OriginEmitter {
    event OriginEvent(
        address indexed origin,     // indexed（必含 Origin）
        uint256 amount,
        string message
    );

    function triggerEvent(uint256 amount, string calldata message) external {
        emit OriginEvent(msg.sender, amount, message);
    }
}
```

-   **topic0**：keccak256("OriginEvent(address,uint256,string)")（稳定不变）。
    
-   部署后记下合约地址（后面订阅用）。
    

2\. Reactive 合约（核心：双状态理解 + subscribe + react + callback）

-   **双状态**：合约在 **Reactive Network (RNK)** 公开部署一份（EOA 可交互、暂停订阅），同时在 **私有 ReactVM** 有一份隔离实例（仅执行 react()，状态不共享）。vmOnly 修饰符 + if (!vm) 保证订阅只在 RNK 执行。
    
-   使用官方 reactive-lib（AbstractReactive / IReactive / ISystemContract）。
    
-   constructor 调用 subscribe（过滤维度：chainId + contract + topics 等值匹配）。
    
-   react(LogRecord)：读取 topics/data，做条件判断（阈值、白名单、去重），emit 可观测事件。
    
-   Callback：emit Callback 事件触发系统代理到 Destination，**强制第一个参数为 address**。
    

solidity

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "reactive-lib/src/interfaces/ISystemContract.sol";
import "reactive-lib/src/abstract-base/AbstractReactive.sol";  // 推荐基类（含 vmOnly、service 等）

contract ReactiveBridge is AbstractReactive {
    uint64 constant GAS_LIMIT = 1_000_000;
    uint256 constant REACTIVE_IGNORE = 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff;

    address public immutable SYSTEM_CONTRACT;
    uint256 public originChainId;
    address public originContract;
    uint256 public topic0;
    address public destinationContract;  // Destination 回调地址

    event ReactiveTriggered(  // 可观测事件（供 UI/后端确认）
        address indexed origin,
        uint256 amount,
        uint256 blockNumber
    );

    event Callback(
        uint256 indexed chainId,
        address indexed target,
        uint64 gasLimit,
        bytes payload
    );

    constructor(
        address _systemContract,      // 固定 0x000...fffFfF
        uint256 _originChainId,       // e.g. Sepolia 11155111
        address _originContract,
        address _destinationContract
    ) payable AbstractReactive(_systemContract) {
        SYSTEM_CONTRACT = _systemContract;
        originChainId = _originChainId;
        originContract = _originContract;
        destinationContract = _destinationContract;

        // 计算 topic0（OriginEvent）
        topic0 = keccak256("OriginEvent(address,uint256,string)");

        if (!vm) {  // 只在 RNK 实例订阅
            ISystemContract(payable(_systemContract)).subscribe(
                _originChainId,
                _originContract,
                topic0,
                REACTIVE_IGNORE,  // topic1 不滤
                REACTIVE_IGNORE,
                REACTIVE_IGNORE
            );
        }
    }

    // 回调入口（Destination 侧会收到此签名，强制第一个参数 address）
    function callback(address origin) external {
        // 可在此加 Destination 逻辑（示例：简单 emit）
        emit ReactiveTriggered(origin, 0, block.number);
    }

    function react(LogRecord calldata log) external vmOnly {
        // 1. 读取 log（topics + data）
        address originAddr = address(uint160(log.topic_1));  // indexed origin
        uint256 amount = log.topic_2;                       // uint256 amount
        // bytes memory message = log.data;                 // 可 decode

        // 2. 条件判断（阈值 + 白名单 + 去重示例）
        require(amount >= 0.001 ether, "Amount threshold not met");
        // 白名单示例：if (originAddr != allowedAddress) return;
        // 去重：可维护 mapping(lastProcessed) 或用 block.number

        // 3. emit 可观测事件（便于 UI/后端确认）
        emit ReactiveTriggered(originAddr, amount, block.number);

        // 4. 触发 Destination 回调（强制第一个参数 address）
        bytes memory payload = abi.encodeWithSignature(
            "callback(address)",
            originAddr
        );
        emit Callback(originChainId, destinationContract, GAS_LIMIT, payload);
    }
}
```

-   **部署命令（Lasna Testnet）**（用 Foundry）：
    
    Bash
    
    ```
    forge create --rpc-url https://lasna-rpc.rnk.dev/ \
      --private-key <YOUR_KEY> \
      --chain-id 5318007 \
      src/ReactiveBridge.sol:ReactiveBridge \
      --constructor-args 0x0000000000000000000000000000000000fffFfF 11155111 <OriginContract> <DestinationContract>
    ```
    
-   部署后立即验证：
    
    Bash
    
    ```
    forge verify-contract --verifier sourcify --chain-id 5318007 <DEPLOYED_ADDR> ReactiveBridge
    ```
    
-   **RVM 地址 / 排错**：部署地址在 Reactscan（[https://lasna.reactscan.net/）可见对应](https://lasna.reactscan.net/）可见对应) RVM ID（回调验证用）。后端可通过 RNK RPC 查询（见后端部分）。日志/返回中带上 RVM ID: 0x... 并提示用户去 Reactscan 对照 tx。
    

3\. Destination 链上的回调入口合约（示例）

部署在 Destination（可复用 Sepolia）：

solidity

```
contract DestinationReceiver {
    event DestinationExecuted(address origin, string status);

    function callback(address origin) external {
        // 校验 sender == Callback Proxy（官方机制自动）
        emit DestinationExecuted(origin, "Executed via Reactive");
    }
}
```

### 前端（Frontend）

**技术栈**：Next.js + wagmi + viem + RainbowKit（钱包连接）+ React Timeline 组件。

-   **连接钱包 + 切换链**：支持 Origin（Sepolia）和 Lasna。
    
-   **最小交互**：点击按钮触发 Origin 事件。
    
-   **展示三段链路时间线**（Origin → Reactive → Destination）。
    

关键代码（WalletConnect.tsx）：

tsx

```
import { useAccount, useSwitchChain, useWriteContract } from 'wagmi';
import { sepolia, lasnaChain } from './chains';  // 自定义 Lasna chain 定义

const chains = [sepolia, lasnaChain];  // Lasna: chainId 5318007, rpc https://lasna-rpc.rnk.dev/

function TriggerOrigin() {
  const { chain } = useAccount();
  const { switchChain } = useSwitchChain();
  const { writeContract } = useWriteContract();

  const trigger = async () => {
    if (chain?.id !== 11155111) switchChain({ chainId: 11155111 });
    // 调用 OriginEmitter.triggerEvent
    writeContract({
      address: ORIGIN_ADDR,
      abi: ORIGIN_ABI,
      functionName: 'triggerEvent',
      args: [1000000000000000n, 'Test from frontend']
    });
  };

  return <button onClick={trigger}>触发 Origin 事件 → Reactive</button>;
}

// 时间线（用 react-timeline 或 shadcn）
const Timeline = ({ status }: { status: { origin: boolean; reactive: boolean; dest: boolean } }) => (
  <div className="timeline">
    <div>Origin ✅</div>
    <div>Reactive {status.reactive ? '✅' : '⏳'}</div>
    <div>Destination {status.dest ? '✅' : '⏳'}</div>
  </div>
);
```

-   用 WebSocket 接收后端推送，实时更新时间线状态。
    

### 后端（Backend）

**技术栈**：Node.js + Express + ethers.js v6 + ws（WebSocket）。

-   **同时监听至少两条链**（Origin + Lasna），统一事件结构。
    
-   **推送通道**：选择 **WebSocket**（实时性更好）。
    
-   **调用 RNK 专用 RPC**：用 ethers Provider 调用 view 方法验证（或 cast 等价）。
    
-   **返回排错信息**：部署地址 → RVM 地址 + Reactive tx 状态 + Reactscan 链接。
    

TypeScript

```
// server.ts
import { ethers } from 'ethers';
import { WebSocketServer } from 'ws';

const originProvider = new ethers.JsonRpcProvider('https://sepolia.infura.io/...');
const lasnaProvider = new ethers.JsonRpcProvider('https://lasna-rpc.rnk.dev/');  // RNK RPC

const wss = new WebSocketServer({ port: 8080 });

// 统一事件结构
interface UnifiedEvent {
  stage: 'Origin' | 'Reactive' | 'Destination';
  txHash: string;
  originAddr: string;
  timestamp: number;
  rvmId?: string;  // 排错
}

// 监听 Origin
originProvider.on(ORIGIN_FILTER, (log) => {
  const unified: UnifiedEvent = { stage: 'Origin', ... };
  broadcast(unified);
});

// 监听 Lasna Reactive（react 触发的 ReactiveTriggered 事件）
lasnaProvider.on(REACTIVE_FILTER, async (log) => {
  // 调用 RNK 专用 RPC 验证（示例：查询合约状态）
  const rvmId = await lasnaProvider.call({  // 或自定义 RNK 方法
    to: REACTIVE_ADDR,
    data: ethers.id('getRvmId()')  // 实际可根据系统合约查询
  });

  const unified: UnifiedEvent = {
    stage: 'Reactive',
    txHash: log.transactionHash,
    rvmId: ethers.toBeHex(rvmId),  // 返回 RVM 地址
    // ... 
  };
  broadcast(unified);

  // 日志 + 返回给前端
  console.log(`RVM 地址: ${rvmId} | 去 https://lasna.reactscan.net/tx/${log.transactionHash} 对照`);
});

// WebSocket 推送
function broadcast(event: UnifiedEvent) {
  wss.clients.forEach(client => client.send(JSON.stringify(event)));
}
```

-   **SSE 备选**：如果喜欢简单，用 res.write 流式返回。
    
-   排错信息直接返回给前端：{ rvmAddress: "0x...", reactiveStatus: "confirmed", reactscanUrl: "[https://lasna.reactscan.net/](https://lasna.reactscan.net/)..." }。
    

### 部署 & 测试流程（Dev Guide 核心）

1.  部署 Origin（Sepolia）→ 记地址。
    
2.  部署 Reactive（Lasna）→ 用上面命令，传入系统合约 + Origin 地址。
    
3.  部署 Destination（Sepolia）→ 记地址。
    
4.  前端连接钱包 → 切换 → 触发 → 后端推送 → 时间线实时更新。
    
5.  去 [**https://lasna.reactscan.net/**](https://lasna.reactscan.net/) 查看 Reactive tx + RVM 对应实例。
    

**Ecosystem Cases 灵感**：Rogues Studio（游戏事件 Origin → NFT Destination）、Reactive Bridge（跨链自动）、Based BRETT（实时 leaderboard 时间线）
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->

今天有事，明天补
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->


## 整体架构：三合约模型

Reactive Network 的跨链系统需要三个合约，分工明确：

**链 A（Origin Chain，比如 Sepolia）**

-   `OriginContract`：普通合约，用户在这里触发操作，emit 事件
    

**Reactive Network（中间层）**

-   `ReactiveContract`：核心 RSC，监听链 A 的事件，决定要做什么，emit `Callback`
    

**链 B（Destination Chain，比如 Sepolia 或其他链）**

-   `DestinationContract`：普通合约，接收来自 RSC 的回调，执行最终逻辑RSC 必须实现的接口只有两个关键点：
    
    **1\.** `react()` **函数** — 当订阅的事件发生时，Reactive Network 会调用这个函数
    
    `react()` 接收一个 `LogRecord` 结构体，里面包含事件来自哪条链（`chain_id`）、哪个合约（`_contract`）、以及事件的 topics 和 data。 [Reactive](https://dev.reactive.network/education/module-1/how-events-work)
    
    **2\.** `Callback` **事件** — RSC 通过 emit 这个事件，触发对目标链的调用
    
    Reactive Network 会自动将 payload 中的前 160 位替换成 RVM ID（部署者地址），所以回调函数的第一个参数永远是 `address` 类型。 [Reactive](https://dev.reactive.network/events-&-callbacks)
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->



### 1\. 核心概念：控制反转 (Inversion of Control, IoC)

这是 Reactive 合约与传统合约（如 Ethereum 上的合约）最本质的区别：

-   **传统合约（被动型）：** 必须由外部账户（EOA，如用户钱包或机器人）发起交易并支付 Gas，合约才会执行。
    
-   **Reactive 合约（主动型）：** 采用 **控制反转** 模式。合约不再等待用户调用，而是“订阅”特定区块链上的事件。一旦监测到符合条件的事件，合约会自动触发执行逻辑。
    

### 2\. 响应式工作流程

Reactive 合约的运行遵循“订阅 - 监测 - 反应”的循环：

1.  **配置订阅：** 开发者在合约中指定要监测的**链 ID**、**目标合约地址**以及特定的**事件签名 (Topic 0)**。
    
2.  **事件捕捉：** Reactive Network 持续扫描所选链的日志。
    
3.  **自动执行：** 当匹配的事件发生时，网络会自动在 Reactive 层触发合约逻辑。
    
4.  **跨链反馈：** 合约执行后，可以产生状态变更，或者通过网络的基础设施向其他目标链发送回调消息或触发新交易。
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->




## 1\. Reactive Contract 的组成结构

传统的智能合约是**被动**的（只有外部账户调用它，它才会执行）；反应式合约是**主动**的。其核心结构通常包含三个部分：

-   **监测逻辑 (Detection Logic):** 定义合约关心哪些链上或链下事件（如：当某个池子的价格低于 $X$）。
    
-   **触发条件 (Predicate/Condition):** 一组逻辑判断。只有当监测到的数据满足特定条件时，合约才会激活。
    
-   **动作逻辑 (Action Logic):** 一旦满足条件，合约需要执行的具体事务（如：执行清算、跨链转账）。
    

* * *

## 2\. Subscribe / Trigger / Callback 模型

这是 Reactive Contract 实现异步交互的标准工作流。

-   **Subscribe (订阅):** 合约向底层索引器或中间件注册它感兴趣的事件源（Source）。这就像你在 B 站关注了一个 UP 主并开启了小铃铛。
    
-   **Trigger (触发):** 当源链上发生了匹配的事件（Event）时，底层框架（如 Reactive Network）会捕获这个信号。
    
-   **Callback (回调):** 框架将触发信号和相关数据推送到你的 Reactive Contract 中预定义的函数。合约根据这些数据执行后续逻辑。
    

**本质逻辑：** 它将“状态改变的感知”与“逻辑的执行”解耦了。

* * *

## 3\. ReactVM 执行逻辑

ReactVM（Reactive Virtual Machine）与 EVM 的最大区别在于**并行处理**和**事件敏感性**。

-   **事件驱动流水线:** 不同于 EVM 顺序处理每个区块内的交易，ReactVM 维护着一个事件响应队列。
    
-   **状态隔离:** ReactVM 运行在独立的响应层。它不直接修改源链的状态，而是产生一个“意图（Intent）”，通过发送新的交易来改变目标链状态。
    
-   **确定性响应:** ReactVM 必须保证：对于给定的事件输入，产生的回调动作是确定性的，从而确保分布式节点能够达成共识。
    

* * *

## 4\. 跨链与自动化机制

这是 Reactive Contract 最具杀手锏的应用场景。

### 自动化 (Automation)

传统的自动化需要靠中心化的 Keepers（如 Chainlink Keepers）。

-   **Reactive 做法:** 合约自启动。它监控时间或状态，达到阈值后自动在 ReactVM 中运行逻辑并发出交易。**去中心化程度更高。**
    

### 跨链 (Cross-chain)

目前的跨链多是桥接（Bridge），而 Reactive Contract 实现的是**跨链互操作性**。

-   **监听 A 链:** 订阅 A 链的合约日志。
    
-   **ReactVM 决策:** 在中间层处理逻辑（例如计算汇率或检查库存）。
    
-   **驱动 B 链:** 自动触发 B 链上的合约调用。
    
-   **闭环:** 整个过程不需要前端介入，实现了“A 链发生 A'，B 链自动执行 B'”的无缝衔接。
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->





## 1\. 根本区别：被动调用 vs 主动反应

在传统的 **EVM** 架构中，智能合约是“死的”。除非用户（或外部脚本）发送一笔交易来调用它，否则它永远不会主动执行任务。

-   **传统 EVM (Passive)：** 像一个自动售货机。你不投币（发交易）并按下按钮，它永远不会动。
    
-   **Reactive Network (Active)：** 像一个带传感器的恒温器。它不断“观察”外部环境，一旦满足条件（如气温低于 20°C），它无需人为干预，立即自动调节。
    

* * *

## 2\. 核心架构：ReactVM + 睿应层环境

睿应层并不是要取代现有的区块链（如 Ethereum 或 BSC），它是作为\*\*元层（Meta-layer）\*\*存在。

### ReactVM (Reactive Virtual Machine)

ReactVM 是一种专门为**处理事件流**而优化的环境。

-   **非独占执行：** 它不仅处理自己的状态，还能“跨链”监听。
    
-   **逻辑分离：** 你可以将业务逻辑留在 EVM 链上，而将“监控与触发逻辑”部署在 ReactVM 中。
    

### 运行环境

睿应层通过 **Relayers（中继器）** 实时获取上游链（源链）的日志和状态。当检测到特定事件时，ReactVM 会根据预设逻辑产生一个“睿应”，并将其推送到下游链（目标链）。

* * *

## 3\. 什么是“睿应式智能合约”（Reactive Smart Contracts）？

如果说普通合约是 `Function call`，那么睿应式合约就是 `Event listener + Callback`。

**睿应式智能合约（RSC）** 的三个核心特征：

1.  **订阅性 (Subscription)：** 它定义了要监听哪些链、哪些合约、哪些 Topic（日志）。
    
2.  **异步性 (Asynchrony)：** 事件发生到合约执行是解耦的，不阻塞源链。
    
3.  **跨链原子性逻辑：** 虽然物理上不在同一条链，但在逻辑上它能把 A 链的输出作为 B 链的输入。
    

> **逻辑挑战：** 很多人会把这和“预言机（Oracle）”或“链下脚本（Keepers）”搞混。
> 
> -   **区别在于：** 睿应层是在**共识层**处理这些事件，而不是靠一个中心化的机器人去跑脚本。它实现了“链上原生的自动化”。
>     

* * *

## 4\. 睿应层（Reactive Network）适合解决什么问题？

如果你还在靠后端服务器去轮询（Polling）区块链状态，那这就是睿应层发挥作用的地方。

| 场景 | 传统方案的痛点 | 睿应层的解决方式 | | 全链收益聚合 | 需要用户手动在 A 链提取，再跨桥到 B 链。 | 自动监控 A 链收益，达标后自动触发跨链复投。 | | 动态 NFT (dNFT) | 需要中心化服务器监控链下数据并修改 metadata。 | 直接监听链上成就/事件，自动触发 NFT 状态升级。 | | 复杂的止损逻辑 | 依赖 Keeper 节点，存在延迟或失效风险。 | 直接在睿应层部署“价格触发器”，一旦满足条件立即执行清算。 | | 跨链治理 | 多链提案统计极其繁琐且不透明。 | 自动汇聚各链投票事件，汇总结果并自动在主链执行提案。 |
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->







## 合约设计逻辑

**Origin (**`BasicDemoL1Contract.sol`**)** — 部署在 Sepolia

-   `receive()` 接收 ETH，emit `Received(from, sender, value, counter)` 然后退款
    
-   `value` 是 indexed 的 topic\_3，RSC 用它来做条件过滤
    

**Destination (**`BasicDemoL1Callback.sol`**)** — 部署在 Sepolia

-   构造时传入 callback proxy 地址，做 `msg.sender` 校验，防止任意人调用
    
-   部署时要附带 0.01 ETH 作为 gas 储备
    

**Reactive (**`BasicDemoReactiveContract.sol`**)** — 部署在 Reactive Lasna

-   构造函数调用 `service.subscribe()`；如果 revert（在 ReactVM 中），则 `vm = true`
    
-   `react()` 只在 ReactVM 环境执行，检查 `topic_3 >= 0.01 ETH` 后 emit `Callback`
    
-   `address(0)` 是占位符，callback proxy 执行时会替换为实际 RSC 地址
<!-- DAILY_CHECKIN_2026-03-10_END -->
<!-- Content_END -->
