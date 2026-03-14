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
