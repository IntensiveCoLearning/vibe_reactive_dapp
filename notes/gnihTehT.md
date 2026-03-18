---
timezone: UTC+8
---

# ShadowDogee

**GitHub ID:** gnihTehT

**Telegram:**

## Self-introduction

shadowdoge，web3新人

## Notes

<!-- Content_START -->
# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->
## 对于 Cron 事件，`react()` 收到的是什么

Cron 不来自任何用户合约——它来自 Reactive 的 **SystemContract**，按固定区块间隔自动 emit。所以 `react()` 收到的 `LogRecord` 里：

-   `_contract` = SystemContract 地址（固定值）
    
-   `topic_0` = cron 事件的 topic（订阅时指定的那个）
    
-   `block_number` = 当前区块号（可以用来做"每 X 块执行一次"的二次过滤）
    
-   `data` 里可能带有时间戳或间隔信息
    

因此在 Cron 场景里，`react()` 的典型结构是这样的：

solidity

```solidity
function react(LogRecord calldata log) external vmOnly {

    // 1. 可选：按区块号做频率过滤（每 100 块才真正执行）
    if (log.block_number % 100 != 0) return;

    // 2. 读取外部状态（只能读同一 deployer 的合约）
    uint256 healthFactor = myPositionTracker.getHealthFactor(user);

    // 3. 条件判断
    if (healthFactor < LIQUIDATION_THRESHOLD) {

        // 4. 构造 callback payload
        bytes memory payload = abi.encodeWithSignature(
            "protect(address,uint256)",
            address(0),   // ReactVM ID 自动填入
            healthFactor
        );

        // 5. 触发目标链上的保护操作
        emit Callback(DEST_CHAIN_ID, protectionContract, GAS_LIMIT, payload);
    }
}
```

* * *

## `react()` 里最常见的三种模式

**模式一：一次性触发**。用状态变量 `triggered` 做守卫，只触发一次——止损单就是这样，触发后不再重复执行。

**模式二：持续监控**。每次 cron 都检查，条件成立就触发，条件不成立就静默返回——Aave 清算保护就是这样，始终盯着健康因子。

**模式三：数据累积**。不触发 callback，只把事件数据存到 ReactVM 内部的状态变量里，等查询时再用——ERC-20 Turnovers Demo 就是这个模式，持续记录交易量，不主动干预。

* * *

## 一个隐藏细节：ReactVM 的 payload 第一个参数

Reactive Network 会自动把 callback payload 的前 160 bit 替换为 ReactVM ID（即 deployer 的地址）。 [Reactive](https://dev.reactive.network/events-&-callbacks)所以你在 `stop(address,...)` 里写的 `address(0)` 只是占位符，实际执行时那个位置会变成 ReactVM 地址——目标链合约可以用这个地址来验证调用方，防止伪造 callback。

这是 Reactive 的安全机制，写代码时要记得第一个参数永远留 `address(0)` 作占位。
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->

### 关键概念与实现

1.  **ERC-20** `Approval` **事件监听**：
    
    -   在 DeFi 中，用户通常需要授权（`approve`）智能合约一定数量的代币，以便合约能够代替用户执行交易。例如，在代币交换或质押时，用户需要手动进行 `approve` 操作，授予合约代币的使用权限。
        
    -   `ApprovalListener` 监听链上 ERC-20 的 `Approval` 事件，当某个账户发出授权时，系统会捕获到这一事件。
        
2.  **自动化代币交换**：
    
    -   在传统流程中，用户需要两到三次手动点击来授权、执行交换或者其他操作。但通过这个自动化系统，用户只需发起一次 `approve` 操作，系统会在检测到授权后，自动替用户发起代币的交换（swap）操作。
        
    -   这种流程的简化，使得整个操作变成了"授权即触发"，有效减少了用户交互的步骤。
        
3.  **无需修改 ERC-20 合约**：
    
    -   一个非常重要的技术点是，这个 Demo 完全没有修改原有的 ERC-20 合约。ERC-20 合约依然保持原封不动，仅仅是通过增加一个外部的监听层（`ApprovalListener`）来捕捉 `Approval` 事件并触发后续操作。
        
    -   这意味着即使在现有的 DeFi 合约中，开发者也可以通过加入监听机制来增强功能，而不需要对现有合约进行复杂的修改或部署新的合约。
        

### 应用场景

1.  **提升 DeFi 用户体验**：
    
    -   对于用户来说，DeFi 操作往往繁琐，尤其是在需要频繁授权和交换代币时。通过将多个步骤自动化，`Approval Magic` 能够显著降低用户的操作复杂度，提升整体体验。
        
    -   用户不需要再频繁进行“点击授权”以及“执行交易”的操作，而是通过一次授权，系统自动完成后续动作。
        
2.  **DeFi 流程的去中心化和灵活性**：
    
    -   通过这种监听层的方式，用户无需依赖平台方的改动，可以在任何支持 ERC-20 标准的 DeFi 协议中，自动化操作这些常见步骤，从而提高了系统的灵活性和扩展性。
        
    -   这种技术也可以应用到其他 DeFi 工作流中，例如自动套利、定时交易等，只要涉及到 ERC-20 授权与交易的场景，都可以进行类似的自动化。
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->


## Cron Demo — 给智能合约装上时钟

这个 Demo 解决了一个根本性问题：EVM 合约本身没有时钟，只能被动等人调用。Reactive Network 的 system contract 会以固定区块间隔 emit cron 事件，RC 订阅这些事件，就能实现定时执行——完全链上，不需要任何链下 keeper。

典型应用：每日费率更新、每周奖励分发、定期检查 DeFi 仓位健康度。

## Hyperlane Demo — 与外部消息协议集成

前面几个 Demo 的 callback 都走 Reactive 自己的 callback proxy。这个 Demo 展示了另一种路径：用 **Hyperlane** 做跨链消息传递，完全绕开 Reactive 默认的回调代理，实现双向消息流。`HyperlaneOrigin`（Base 主网）和 `HyperlaneReactive`（Reactive 主网）互相传消息，可以自动响应也可以手动触发。

这证明了 Reactive 的模块化能力——它不锁定你的消息层，可以插入任意消息协议。

## Uniswap V2 Stop Order — 止损单（单向）

RC 订阅 Uniswap V2 Pair 的 `Sync` 事件（每次 swap 都会触发），从 `reserve0 / reserve1` 推算当前价格，一旦跌破用户设定的止损价，就 emit Callback 让目标链合约执行 swap。这是挑战作业里的主线 Demo。

## Uniswap V2 Stop-Loss & Take-Profit — 止损 + 止盈（双向，每人独立部署）

这是 Demo 4 的升级版，两个关键改进：

第一，**同时支持止损（price 跌破下界）和止盈（price 突破上界）**，用同一套 RC 逻辑处理两个方向。第二，**每个用户部署自己专属的 RC**，订单管理完全隔离，不会有不同用户之间的状态污染。适合实际产品化的场景。

## Aave 清算保护 — 基于 Cron 的仓位守护

这个 Demo 把 Cron 机制用在了 DeFi 最紧迫的场景上：**防止 Aave 头寸被清算**。

RC 定期订阅 cron 事件，每次触发时检查用户的健康因子（Health Factor）。当 HF 低于阈值，callback 合约会自动执行补救操作：补充抵押物、偿还部分债务，或两者兼顾。整个过程没有链下 bot，不怕宕机，不怕网络延迟。

## Approval Magic — 自动化多步 DeFi 工作流

这个 Demo 展示了一个更"产品化"的思路：用户向 `ApprovalService` 注册，`ApprovalListener`（RC）监听链上的 ERC-20 `Approval` 事件。一旦检测到授权，系统自动代替用户发起代币 swap——原本需要用户手动点两三次按钮的流程，变成了"授权即触发"的单步操作。

这是 **无需修改原合约** 来增加功能的最典型示范：ERC-20 标准完全没动，只是在旁边加了一个监听层。
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->



Uniswap 是以太坊上最重要的去中心化交易所（DEX）协议之一，彻底改变了加密资产的交换方式。下面我来分几个核心概念为你讲清楚。

## 核心思想：自动做市商（AMM）

传统交易所（如币安）使用**订单簿**——买家挂买单，卖家挂卖单，撮合成交。Uniswap 抛弃了这种模式，改用一个数学公式来自动定价：

> **x × y = k**

其中 x 和 y 是流动性池中两种代币的数量，k 是常数。每次交易都会改变 x 和 y 的比例，但乘积 k 保持不变，价格由此自动浮动。

## 流动性提供者（LP）

没有流动性，交易就无法进行。任何人都可以向池子里注入等值的两种代币，成为**流动性提供者**，获得：

-   该池子的 LP 代币（代表份额）
    
-   每笔交易 0.3% 的手续费收益
    

但这里有个重要风险——**无常损失（Impermanent Loss）**：当两种代币价格比例发生大幅变化时，LP 持有的资产价值可能低于直接持有原始代币。

## Uniswap 版本演进

**V3 的集中流动性**是一个重大突破：LP 可以指定只在某个价格区间（比如 ETH 在 1800~2200 USDC 之间）提供流动性，从而大幅提升资本效率——同样的资金可以产生更多的手续费收益，代价是需要主动管理仓位。
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->




终于通过第一

## **核心概念**

• Origin Chain（源链）：事件发生的链，本实验使用 Base Sepolia

• Reactive Network：中间层，负责监听源链事件并触发回调

• Destination Chain（目标链）：接收回调的链，本实验同样使用 Base Sepolia

• topic\_0：事件的 Keccak256 签名哈希，用于精确订阅特定事件

• Callback Proxy：目标链上的代理合约，用于验证并转发回调

## **三个核心合约**

整个系统由三个合约协同工作：

| 合约名称 | 部署网络 | 功能说明 |
| BasicDemoL1Contract | Base Sepolia | 接收 ETH 并退回，同时触发 Received 事件 |
| BasicDemoReactiveContract | Reactive Lasna | 订阅源链事件，满足条件时发出 Callback 事件 |
| BasicDemoL1Callback | Base Sepolia | 接收跨链回调，发出 CallbackReceived 事件 |

## **部署流程**

## **Step 1 — 部署 Origin Contract**

在 Base Sepolia 上部署事件源合约：

forge create src/demos/basic/BasicDemoL1Contract.sol:BasicDemoL1Contract \\

  --rpc-url [https://sepolia.base.org](https://sepolia.base.org) \\

  --private-key <YOUR\_PRIVATE\_KEY> \\

  --broadcast

**⚠️  注意事项：**

• 必须在项目根目录 reactive-smart-contract-demos 下运行

• --broadcast 必须放在 --constructor-args 之前

## **Step 2 — 部署 Destination Contract**

部署回调合约时，构造函数需要传入 Callback Proxy 地址（不是 0xfffFfF！）：

forge create src/demos/basic/BasicDemoL1Callback.sol:BasicDemoL1Callback \\

  --rpc-url [https://sepolia.base.org](https://sepolia.base.org) \\

  --private-key <YOUR\_PRIVATE\_KEY> \\

  --broadcast \\

  --value 0.02ether \\

  --constructor-args 0xa6eA49Ed671B8a4dfCDd34E36b7a75Ac79B8A5a6

**Base Sepolia Callback Proxy 地址：**

| Base Sepolia | 0xa6eA49Ed671B8a4dfCDd34E36b7a75Ac79B8A5a6 |

## **Step 3 — 部署 Reactive Contract**

在 Reactive Lasna 网络上部署监听合约，订阅源链事件：

forge create src/demos/basic/BasicDemoReactiveContract.sol:BasicDemoReactiveContract \\

  --rpc-url [https://lasna-rpc.rnk.dev/](https://lasna-rpc.rnk.dev/) \\

  --private-key <YOUR\_PRIVATE\_KEY> \\

  --value 0.001ether \\

  --broadcast --legacy \\

  --constructor-args \\

  0x0000000000000000000000000000000000fffFfF \\   # service address

  84532 \\                                        # originChainId

  84532 \\                                        # destinationChainId

  <ORIGIN\_CONTRACT\_ADDR> \\                       # 源链合约地址

  0x8cabf31d2b1b11ba52dbb302817a3c9c83e4b2a5194d35121ab1354d69f6a4cb \\ # topic\_0

  <CALLBACK\_CONTRACT\_ADDR>                        # 回调合约地址

**topic\_0 计算方式：**

cast keccak "Received(address,address,uint256)"

## **Step 4 — 触发跨链事件**

向源链合约发送 ETH，金额必须 >= 0.001 ether 才能触发回调：

cast send <ORIGIN\_CONTRACT\_ADDR> \\

  --value 0.001ether \\

  --rpc-url [https://sepolia.base.org](https://sepolia.base.org) \\

  --private-key <YOUR\_PRIVATE\_KEY>

**⚠️  关键阈值：合约代码中 topic\_3 >= 0.001 ether 才会触发回调，发送金额不足会导致静默失败！**

## **踩过的坑（重要经验）**

| # | 问题 | 解决方法 |
| 1 | --broadcast 位置错误 | 必须放在 --constructor-args 之前，否则被解析为构造参数 |
| 2 | gas required exceeds allowance (0) | 钱包在目标链没有测试币，去水龙头领取 ETH |
| 3 | logs: [] 事件为空 | 发送的是普通 ETH 转账而非调用合约函数，需要发到合约地址 |
| 4 | Callback Proxy 地址错误 | 错用了 0xfffFfF，应使用 Base Sepolia 专用地址 0xa6eA49... |
| 5 | 发送金额不足 | 合约要求 topic_3 >= 0.001 ether，发送 0.0001 ether 不会触发回调 |
| 6 | 路径错误 | forge create 必须在项目根目录运行，不能在父目录 |

## **本次部署结果**

| 合约 | 网络 | 地址 | 交易哈希 |
| Origin Contract | Base Sepolia | 0x7cEF140853338D18aDb5661e3C3407C33cA91990 | 0x2046af258731f53d991266985ea06db15eded5365163cb6351b8fe0971d3e4e7 |
| Reactive Contract | Lasna Testnet | 0xE7eeDfC54367f8D641D591B6DfA7F8742cb68c71 | 0x740899928b5c1f24734ddcecc95e10f1a9bcf655e90ee19b3f898b03aa6076d2 |
| Destination Contract | Base Sepolia | 0x1d585253e67BD8329eEfBe36D4Dea875Ce47A12B | 0x110d60f7edd5a0885229a844f48acf57ee9008735947dca243bb8334ccd0d165 |

## **触发成功的交易**

| 触发交易 | 0xd3618207fef5102edbe40662573e9569cf6023679f79d8b7aa995f88810215a3 |

| 回调交易 | 0x1b474b8bc7... (BaseScan Events 截图可查) |

回调验证：在 BaseScan 上确认 CallbackReceived 事件已触发 ✅

• sender: 0xa6eA49Ed671B8a4dfCDd34E36b7a75Ac79B8A5a6（Callback Proxy）

• reactive\_sender: 0xB417B0f43E4bFeDA3A4564153768cC26E536ec43（自己的地址）

## **关键参数速查表**

| 参数 | 值 |
| Reactive Lasna RPC | https://lasna-rpc.rnk.dev/ |
| Base Sepolia RPC | https://sepolia.base.org |
| Base Sepolia Chain ID | 84532 |
| Ethereum Sepolia Chain ID | 11155111 |
| Reactive Service Address | 0x0000000000000000000000000000000000fffFfF |
| Base Sepolia Callback Proxy | 0xa6eA49Ed671B8a4dfCDd34E36b7a75Ac79B8A5a6 |
| ETH Sepolia Callback Proxy | 0xc9f36411C9897e7F959D99ffca2a0Ba7ee0D7bDA |
| Received 事件 topic_0 | 0x8cabf31d2b1b11ba52dbb302817a3c9c83e4b2a5194d35121ab1354d69f6a4cb |
| REACTIVE_IGNORE 常量 | 0xa65f96fc951c35ead38878e0f0b7a3c744a6f5ccc1476b313353ce31712313ad |
| 最低触发金额 | 0.001 ether |

## **区块浏览器链接**

• BaseScan (Base Sepolia): [https://sepolia.basescan.org](https://sepolia.basescan.org)

• Reactscan (Lasna): [https://lasna.reactscan.net](https://lasna.reactscan.net)

• 官方文档: [https://dev.reactive.network](https://dev.reactive.network)

• 示例仓库: [https://github.com/Reactive-Network/reactive-smart-contract-demos](https://github.com/Reactive-Network/reactive-smart-contract-demos)
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->





遇到了一个坑

# 坑 1：`--broadcast` 被当成 constructor 参数

**现象**

明明加了：

```
--broadcast
```

但 `forge` 仍然提示：

```
Warning: Dry run enabled, not broadcasting transaction
```

**原因**

在 Foundry 的 `forge create` 中：

```
--constructor-args
```

后面的内容都会被解析为 **构造函数参数**。

如果 `--broadcast` 写在 `--constructor-args` 后面，例如：

```
--constructor-args arg1 arg2 arg3 --broadcast
```

Forge 会把：

```
--broadcast
```

当成 **constructor 参数字符串**，而不是命令 flag。

所以命令变成：

```
forge create (dry run)
```

**解决方法**

把 `--broadcast` 放在 `--constructor-args` 之前：

```
--broadcast \
--constructor-args ...
```
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->






# 为什么你 deploy 会 revert

关键在这个逻辑：

```
if (!vm) {
    service.subscribe(...)
}
```

`vm` 是 Reactive 用来区分：

```
ReactVM
vs
普通 EVM
```

Reactive lib 会通过：

```
extcodesize(system contract)
```

检测运行环境。

如果不是 ReactVM：

```
vm = false
```

于是 constructor 会调用：

```
service.subscribe()
```

但你当前 RPC：

```
https://lasna-rpc.rnk.dev
```

**并没有 system contract**

所以：

```
subscribe() call → revert
```

这就是你遇到的：

```
execution reverted
```
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->







**Reactive Network Demo 的核心逻辑是一个跨链的“监听-触发”机制。**

这个 Demo 流程展示了从用户在源链（Origin Chain）发起交易，到响应网络（Reactive Network）处理，最后在目标链（Destination Chain）执行回调的完整闭环。

### 过程分为四个关键动作：

1.  **资产触发 (Origin Chain)**
    
    -   **User** $\\xrightarrow{\\text{Send ETH}}$ **BasicDemoL1Contract**
        
    -   合约逻辑执行，抛出 `Received` 事件。
        
    -   _关键数据：_ `topic_0` (事件签名), `topic_3` (金额)。
        
2.  **异步监听 (Reactive Network)**
    
    -   **Reactive Node** 扫描源链区块。
        
    -   **BasicDemoReactiveContract** 捕获匹配 `topic_0` 的日志。
        
3.  **逻辑过滤与指令下发**
    
    -   **判定：** `if (topic_3 >= 0.01 ETH)`。
        
    -   **执行：** 构造 `Callback` 负载（Payload），发往目标链。
        
    -   _注意：_ 这一步在 Reactive Network 内部完成，具有低延迟特性。
        
4.  **目标确认 (Destination Chain)**
    
    -   **Callback Proxy** 接收指令 $\\rightarrow$ 调用 **BasicDemoL1Callback**。
        
    -   **验证：** 检查调用者是否为授权代理。
        
    -   **结果：** 抛出 `CallbackReceived` 事件，完成闭环。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
