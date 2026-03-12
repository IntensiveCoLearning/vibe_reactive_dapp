---
timezone: UTC+8
---

# beautifulremi

**GitHub ID:** beautifulrem

**Telegram:** 

## Self-introduction

Let's vibe Reactive dApp！

## Notes

<!-- Content_START -->
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
# **L2.1 - Uniswap V2**

# 流动性池

在传统的交易所（如纳斯达克或币安），交易是靠“订单簿”完成的：有人想买，有人想卖，价格匹配才能成交。

但在 Uniswap V2 中，交易不需要等待对手方。**流动性池就像是一个“智能自动售货机”**：

-   它始终包含**两种代币**（例如 ETH 和 USDT）。
    
-   池子里储备了大量的这两种代币，由“流动性提供者”（LP）存入。
    
-   当你来交易时，你不是在和另一个人交易，而是在**和这个智能合约里的储备金交易**。
    

这种流动性池时去中心化的，因为其包含两个特点：

-   **无需许可 (Permissionless)**：不需要传统的做市商（机构或银行）来提供流动性。任何人都可以把自己的代币存入池中成为流动性提供者，并赚取交易手续费。
    
-   **透明性 (Transparency)**：所有的操作都发生在以太坊区块链上。这意味着每一笔兑换、每一次存钱或取钱，在 **Etherscan** 这样的区块浏览器上都是公开透明、不可篡改的。
    

智能合约在这里：

-   管理着这些储备金，确保没有人能随便把钱提走。
    
-   它强制执行一套数学规则（即**恒定乘积模型**），决定了你用多少代币 A 能换回多少代币 B。
    
-   它保证了交易的原子性：要么交易成功，代币交换完成；要么交易失败，资金退回。
    

# 恒定乘积公式

Uniswap数学上遵循 x \\cdot y = k 这一恒定乘积公式。

-   **x 和 y**：池中两种代币的实时储备量。
    
-   **k**：乘积不变量。
    
-   **原理**：在不考虑手续费的情况下，交易前后的 x 和 y 的乘积必须保持不变。
    
    -   如果你想从池子里拿走一些代币 y（输出），你就必须放入足够多的代币 x（输入），使得新的储备量 x’ \\cdot y’ \\ge k。
        
    -   这导致了一个特性：当你买入某种代币时，它的价格会随着你买入数量的增加而呈指数级上升（滑动价差）。
        

一段 Uniswap V2 swap() 函数的简化片段：

```solidity
function swap(uint amount0Out, uint amount1Out, address to, bytes calldata data) external {  
require(amount0Out > 0 || amount1Out > 0, "UniswapV2: INSUFFICIENT_OUTPUT_AMOUNT");  
(uint112 reserve0, uint112 reserve1,) = getReserves(); // fetches reserves of the pool  
require(amount0Out < reserve0 && amount1Out < reserve1, "UniswapV2: INSUFFICIENT_LIQUIDITY");  
  
uint balance0;  
uint balance1;  
{  
uint amount0In = reserve0 - (balance0 = reserve0 - amount0Out);  
uint amount1In = reserve1 - (balance1 = reserve1 - amount1Out);  
require(amount0In > 0 || amount1In > 0, "UniswapV2: INSUFFICIENT_INPUT_AMOUNT");  
  
uint balanceAdjusted0 = balance0 * 1000 - amount0In * 3;  
uint balanceAdjusted1 = balance1 * 1000 - amount1In * 3;  
require(balanceAdjusted0 * balanceAdjusted1 >= uint(reserve0) * uint(reserve1) * (1000**2), "UniswapV2: K");  
  
// Emit the Swap event  
emit Swap(msg.sender, amount0In, amount1In, amount0Out, amount1Out, to);  
}  
  
_update(balance0, balance1, reserve0, reserve1);  
  
if (amount0Out > 0) _safeTransfer(token0, to, amount0Out);  
if (amount1Out > 0) _safeTransfer(token1, to, amount1Out);  
  
if (data.length > 0) {  
IUniswapV2Callee(to).uniswapV2Call(msg.sender, amount0Out, amount1Out, data);  
}  
}
```

我们可以将这段代码拆解为四个主要阶段：

**预检查阶段** (Guards)

```solidity
require(amount0Out > 0 || amount1Out > 0, "UniswapV2: INSUFFICIENT_OUTPUT_AMOUNT");
(uint112 reserve0, uint112 reserve1,) = getReserves();
require(amount0Out < reserve0 && amount1Out < reserve1, "UniswapV2: INSUFFICIENT_LIQUIDITY");
```

首先确保用户不是在做“零交易”，且请求提取的数量不能超过池子现有的储备金（Reserve）。这里使用的是 `amountOut`。在 Uniswap V2 中，交易是“乐观的”，你先告诉合约你想拿走多少，合约会在最后验证你是否存入了足够的钱。

**计算输入金额** (Input Calculation)

```solidity
uint amount0In = reserve0 - (balance0 = reserve0 - amount0Out);
uint amount1In = reserve1 - (balance1 = reserve1 - amount1Out);
require(amount0In > 0 || amount1In > 0, "UniswapV2: INSUFFICIENT_INPUT_AMOUNT");
```

这段代码通过对比“交易前储备”和“交易后余额”来算出用户到底转入了多少代币。在完整的 Uniswap 合约中，`balance0` 会通过 `token.balanceOf(address(this))` 获取。这里简化了逻辑，核心是确保你必须存入（In）了一些东西，才能拿走（Out）东西。

**手续费与恒定乘积验证**

在代码层面上实现了 x \\cdot y = k。

```solidity
uint balanceAdjusted0 = balance0 * 1000 - amount0In * 3;
uint balanceAdjusted1 = balance1 * 1000 - amount1In * 3;
require(balanceAdjusted0 * balanceAdjusted1 >= uint(reserve0) * uint(reserve1) * (1000**2), "UniswapV2: K");
```

-   **手续费处理**：Uniswap V2 收取 **0.3%** 的手续费。
    
    -   逻辑是：在检查乘积不变量之前，先从你的输入金额中扣除 0.3%。
        
    -   为了避免浮点数计算，它全员乘以 1000。
        
    -   `balance0 * 1000 - amount0In * 3` 实际上等于 1000 \\times (Balance - 0.003 \\times Input)。
        
-   **验证公式**：
    
    (x\_{new} \\cdot 1000 - x\_{in} \\cdot 3) \\times (y\_{new} \\cdot 1000 - y\_{in} \\cdot 3) \\ge (x\_{old} \\cdot y\_{old}) \\times 1000^2
    
    只要这个等式成立，说明交易后池子的流动性（考虑手续费后）没有减少。
    

**乐观转账与回调** (Flash Swaps)

```solidity
if (amount0Out > 0) _safeTransfer(token0, to, amount0Out);
if (amount1Out > 0) _safeTransfer(token1, to, amount1Out);

if (data.length > 0) {
    IUniswapV2Callee(to).uniswapV2Call(msg.sender, amount0Out, amount1Out, data);
}
```

-   **先给钱，后验证**：注意到 `_safeTransfer`（给用户钱）发生在 K 值验证（第3步）的逻辑包裹中或紧随其后。
    
-   **闪电贷接口**：如果 `data` 长度大于 0，合约会触发接收地址的 `uniswapV2Call`。
    
    -   **这允许用户：** 先把代币拿走，利用这些代币在其他地方套利，只要在同一个交易结束前把钱还回来并支付 0.3% 的手续费，交易就能成功。这就是“闪电贷”。
        

整个代码需要注意以下特点：

-   **输入与输出的确定**：
    
    在 V2 中，用户先通过代币合约把代币“转入”池子，然后调用 `swap()` 告知合约你想“拿走”多少。代码中的 `amount0In` 和 `amount1In` 是通过当前余额减去旧储备量计算出来的。
    
-   **0.3% 手续费的处理**：
    
    代码中出现了 `* 1000 - amount0In * 3`。这实际上是在验证 k 值。
    
    -   逻辑是：在验证 x \\cdot y \\ge k 之前，先从输入金额中扣除 0.3% 的手续费。
        
    -   乘以 1000 是为了处理小数点（Solidity 不支持浮点数），即 997/1000 = 99.7\\%。
        
-   **悲观检查 (Optimistic Checks)**：
    
    Uniswap V2 支持**闪电兑换 (Flash Swaps)**。它允许你先拿走代币（`_safeTransfer`），只要在交易结束前你把钱（加上手续费）还回来，让最后那个 `require`（验证 k 值）通过即可。如果没还钱，整个交易会回滚。
    

# Uniswap V2 中的事件

## Swap

对于 Uniswap V2 这种高频交易协议，`Swap` 事件是外界感知交易发生的最主要途径。

在区块链上，改变合约状态（比如资金池里的代币变少了）是不会主动通知外部的。如果你想知道刚刚谁买入了 ETH，你不能一直去查询合约的余额（那太慢且贵），而是应该**监听** `Swap` **事件**。

-   **实时性**：交易一旦打包，事件就会立即出现在区块日志中。
    
-   **低成本**：存储事件日志比存储合约变量要便宜得多。
    
-   **可追溯性**：你可以通过事件历史轻松查出过去一年内所有的交易记录。
    

具体参数如下：

```solidity
event Swap(
    address indexed sender, // 谁调用的 swap 函数（通常是路由合约）
    uint amount0In,         // 存入池子的 Token0 数量
    uint amount1In,         // 存入池子的 Token1 数量
    uint amount0Out,        // 从池子拿走的 Token0 数量
    uint amount1Out,        // 从池子拿走的 Token1 数量
    address indexed to      // 最终谁收到了这些代币
);
```

-   `indexed` **关键字**：标记为 `indexed` 的参数（如 `sender` 和 `to`）可以被高效过滤。比如，你可以搜索“所有发送给某特定地址的交易”。
    
-   **In vs Out**：在一个典型的交易中，通常是一个 `In` 为正，另一个 `Out` 为正。
    
    -   _场景 A（用 Token0 买 Token1）_：`amount0In > 0`, `amount1In = 0`, `amount0Out = 0`, `amount1Out > 0`。
        
    -   _场景 B（用 Token1 买 Token0）_：与之相反。
        

## Sync

如果说 `Swap` 事件记录的是过程（谁换了多少钱），那么 `Sync` **事件记录的就是结果（池子里现在还剩多少钱）**。

在 Uniswap V2 中，`Sync` 事件是流动性池状态的“最终真相”。以下是这一部分的深度解析：

`Sync` 事件非常简单，只包含两个数据：

```solidity
event Sync(uint112 reserve0, uint112 reserve1);
```

-   **reserve0 和 reserve1**：代表交易完成后，池子中 Token0 和 Token1 的**最新总余额**。
    
-   **uint112**：这是一个专门优化的数据类型，为了节省 Gas 费，Uniswap 将两个 112 位的整数打包存放在一个 256 位的插槽中。
    

`Sync` 处理了三种 `Swap` 事件覆盖不到的情况：

1.  **添加/移除流动性 (Mint/Burn)**：
    
    当流动性提供者（LP）存入或取走代币时，不会触发 `Swap` 事件，但池子的储备金变了，此时必须发出 `Sync` 事件来更新价格参考。
    
2.  **直接转账 (Donations)**：
    
    如果有人直接把代币转入池子的合约地址（而不是通过交易），池子的余额也会增加。`Sync` 能够捕捉到这些变化，并将其强制同步到合约的状态变量中。
    
3.  **计算价格的“基准线”**：
    
    在 Uniswap 中，**价格是计算出来的**。  
    Price\_{Token0} = \\frac{reserve1}{reserve0}
    
    如果没有最新的 `reserve` 数据，你就无法算出下一笔交易的精确报价。
    

## Reactive Network的意义

睿应式网络可以监听 `swap` 和 `sync` 事件，从而达到想要的效果：

-   利用 `Swap` 事件进行“自动化交易与套利”
    

`Swap` 事件提供了交易的动态信息（进出金额、交易者地址）。Reactive Network 利用它来做**意图（Intent）执行**。

-   **Reactive 逻辑**：RSC 监控 `Swap` 事件中的 `amountIn` 和 `amountOut`。
    
-   **自动化操作**：
    
    -   **跟单交易（Copy Trading）**：如果监测到某个“高胜率地址”（Whale）在 `Swap` 事件中买入某代币，Reactive Contract 可以立即在另一个 DEX 执行相同的买入指令。
        
    -   **自动止盈止损**：当 `Swap` 事件导致的价格变动触及用户的止损线时，Reactive Network 自动发起交易卖出代币。
        
-   **优势**：相比传统的链下监控脚本，Reactive Network 在共识层处理事件，延迟更低且更安全。
    

* * *

-   利用 `Sync` 事件构建“实时价格预言机”
    

由于 `Sync` 事件包含了池子最新的储备量 (reserve0 和 reserve1)，它是计算**瞬时价格**最准的数据源。

-   **Reactive 逻辑**：RSC 订阅特定流动性池的 `Sync` 事件。
    
-   **计算公式**：每当 `Sync` 触发，RSC 自动计算 Price = \\frac{reserve1}{reserve0}。
    
-   **应用场景**：**跨链价格同步**。
    
    -   例如：当以太坊上 Uniswap 的价格发生波动时，Reactive Network 可以瞬间感知，并自动更新其它链（如 Polygon 或 Arbitrage）上的借贷协议利率或清算价格，无需等待中心化预言机（如 Chainlink）的延迟更新。
        

* * *

-   利用 `Sync` 监控“流动性风险”
    

`Sync` 事件在添加或移除流动性时也会触发。

-   **安全监控**：如果监测到 `Sync` 事件显示的 reserve 突然大幅度下降（可能发生了 Rug Pull 或大额撤资），Reactive Contract 可以触发**紧急避险**。
    
-   **操作**：自动将用户的资金从相关的矿池中提出，或者在协议被掏空前执行对冲。
    

* * *

在 Reactive Network 的开发中，利用这两个事件通常分为三步：

| 步骤 | 操作内容 | 对应 Uniswap 事件 |
| --- | --- | --- |
| 1. 订阅 (Subscribe) | RSC 告知 Reactive 节点监控特定池子的合约地址。 | 监控 Swap / Sync 的 Topic ID |
| 2. 过滤 (Filter) | 设置触发条件（例如：amountIn > 100 ETH）。 | Swap 事件中的参数 |
| 3. 执行 (Callback) | 当事件匹配时，在目标链发起一个 Transaction。 | 调用另一链的 transfer 或 buy |
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->


# **L1.5 - 预言机**

**blog:**[**https://beautifulremi.dpdns.org/**](https://beautifulremi.dpdns.org/)

## 预言机问题

区块链和智能合约是一个完全封闭且自洽的系统。为了保证网络中每个节点运行代码的结果都完全一致（可验证、可重复），智能合约**无法主动连接互联网**去获取外部信息。

智能合约如果只在“小黑屋”里自娱自乐，实际用途会非常有限。要释放它的全部商业潜力，它必须知道“屋外的世界”发生了什么，比如：ETH现在的美元价格是多少？某个地方发生飓风了吗？这些存在于区块链外部的信息被称为**链下数据（Off-chain data）**。

既然智能合约出不去，外部数据就必须被“人为”送进去。但是，区块链的核心价值在于**去中心化**和**免信任**（不依赖单一的中心化权威）。如果我们只依赖一个中心化的服务器或个人来提供数据，一旦这个源头作恶、被黑客攻击或宕机，整个智能合约的执行就会出错。**如何既能把现实世界的数据送上链，又不会引入单点故障、不破坏区块链的去中心化和免信任特性？**

## 传统解决方案

首先需要注意的是：预言机本身并不是数据源，它是一个**中间件**。它负责从外部世界（如金融市场的 API 接口、政府公开数据库、甚至现实中的物联网/IoT设备）抓取数据，然后将这些数据“喂”给区块链上的智能合约。

它的核心价值在于：能以“信任最小化”的方式来验证和传递这些数据。也就是说，智能合约不需要盲目信任某个单一的数据提供者。

而对于信任性的问题，区块链上的任何状态改变（包括写入外部数据）都需要通过发送交易来实现。预言机服务商会使用自己的私钥对包含外部数据的交易进行签名，然后发送到区块链上。

这里的风险是：如果只由一个中心化的预言机节点来签名和提交数据，一旦这个节点的私钥泄露，或者它本身被恶意操控，它就可以向区块链输入虚假数据（比如把 ETH 的价格报成 1 美元），从而导致智能合约执行错误。

为了防止上述的单点故障和恶意操纵，行业内引入了**去中心化预言机网络（Decentralized Oracle Networks, DONs）**。

-   **多重签名机制：** 系统会规定一个阈值（比如 7 个人中至少需要 5 个人同意）。当预言机网络抓取到外部数据时，必须有足够数量的独立预言机节点（参与者）用各自的私钥对该数据进行签名授权，这笔包含数据的交易才能最终被提交到区块链上。  
    
-   **意义：** 没有任何单一实体可以擅自将数据送上链。这种方法将去中心化和安全性的理念延伸到了数据获取的环节，完美契合了区块链“免信任”的本质。
    

现代目前去中心化预言机提供商，如**Chainlink** 和 **Band Protocol**的工作原理大多都是聚合（Aggregate）来自多个不同数据源的数据（比如同时参考币安、Coinbase、Kraken 的价格），并由多个独立节点进行验证和签名，从而最大限度地保证了数据的真实性，杜绝了被单方面操纵的风险。

预言机通过提供链下数据，目前广泛的用于以下几个方面：

-   **DeFi 平台（去中心化金融）：** 这是目前预言机最广泛的应用。DeFi 协议需要依靠预言机提供的实时价格数据，来决定借贷利率、进行资产兑换，以及在抵押物价值下跌时**触发清算**。  
    
-   **去中心化保险：** 智能合约可以通过预言机获取官方的天气预报或航班信息。一旦发生台风或航班延误，合约会自动核实并向投保人发放理赔金，全程无需人工审核。  
    
-   **在线博彩：** 将现实中体育比赛的比分通过预言机准确、防篡改地输入到链上，智能合约会根据结果自动且透明地分配奖金，实现了真正的“免信任”博彩。
    

## 应用例子

```solidity
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

代码首先引入了 Chainlink 提供的标准接口 `AggregatorV3Interface.sol`。在构造函数中，合约实例化了这个接口，并传入了一个具体的地址（`0x...`）。这个地址就是 Chainlink 在以太坊主网上专门负责持续更新 ETH/USD 价格的那个“官方”预言机合约。

`getLatestPrice`是核心功能函数。当它被调用时，它会去读取 Chainlink 预言机合约里的 `latestRoundData()`。注意这里的语法 `( /*...*/, int price, /*...*/ )`，它使用了**解构赋值**，忽略了轮次ID、时间戳等不需要的信息，精准地只把 `price`（价格）提取出来并返回。

## EOA限制

上面的代码虽然可以使用，但是它是一个“被动查询”的模型。对于`getLatestPrice()` 这个函数。智能合约本身是死的，它**绝对不可能**自己定时（比如每秒钟）去运行这个函数更新价格。

另外，在以太坊的底层架构中，所有的动作（Transaction）都必须由一个 **EOA（Externally Owned Account，即由私钥控制的普通人类用户钱包）** 来发起和签名。智能合约虽然可以调用另一个智能合约，但这整个“多米诺骨牌”的第一推力，必须来自 EOA。

所以如果你想在价格跌破某个阈值时立刻执行清算，单靠这段代码是做不到的。你必须在外部写一个脚本（比如用 Python），让这个脚本 24 小时不断地用你的 EOA 钱包去发送交易调用 `getLatestPrice()`。这不仅极其消耗 Gas 费，而且存在延迟，根本算不上真正的“智能”和“实时”。

## 睿应式合约

传统智能合约是“被动”的，必须由外部账户（用户钱包）发起交易才能运行。而响应式合约颠覆了这一点，实现了“控制反转”。它们不需要用户直接去“戳”它，而是像雷达一样，实时监听各个 EVM 兼容链上发生的“事件”（Events）。一旦监测到符合条件的事件，它们就会**自动触发**并执行预设的链上操作，甚至可以跨链执行。

所以我们可以将这两者结合起来：

-   **第一步（预言机的工作）：** 预言机敏锐地捕捉到现实世界的数据变化（例如：ETH 价格跌破 2000 美元），并将这个数据打包上链。数据上链的这个动作，在区块链上会**抛出一个“事件”**。  
    
-   **第二步（响应式合约的工作）：** 响应式合约立刻捕捉到了预言机抛出的这个“事件”。它不需要等待任何人来发送交易确认，而是**瞬间、自动地**执行事先写好的代码逻辑（例如：立刻清算某个抵押不足的 DeFi 仓位）。
    

因此我们使用睿应式合约，配合预言机就可以做到 **实时响应链下事件并自动执行链上操作**。

## 例子

设定一个场景，监听Chainlink的ETH/USD价格，当价格低于2000美元时出售仓位：

首先，Chainlink 的价格聚合器合约在更新价格时，会抛出一个名为 `AnswerUpdated` 的事件。它的签名长这样： `AnswerUpdated(int256 current, uint256 roundId, uint256 updatedAt)`

在 EVM 体系中，为了监听这个事件，我们需要计算它的**哈希值（Topic 0）**。 `keccak256("AnswerUpdated(int256,uint256,uint256)")`

```solidity
pragma solidity ^0.8.0;

// 引入 Reactive Network 提供的标准接口
import "reactive-network/interfaces/IReactive.sol";
import "reactive-network/interfaces/ISubscriptionService.sol";

contract ChainlinkPriceReactor is IReactive {

    // ---------------- 配置监听参数 ----------------
    // 1. 源链的 Chain ID (例如：以太坊主网是 1)
    uint256 constant ETH_MAINNET_ID = 1;
    
    // 2. 现成的 Chainlink ETH/USD 预言机合约地址
    address constant CHAINLINK_ETH_USD_ADDRESS = 0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419;
    
    // 3. 事件的 Topic 0 (AnswerUpdated 事件的哈希值)
    uint256 constant ANSWER_UPDATED_TOPIC_0 = 0x0559884fd3a46039e24ee36f2ca1bda315b94f5fb2b3b05f6311de120eb0790b;

    // ---------------- 第一步：订阅 (Subscribe) ----------------
    constructor(address _subscriptionService) {
        // 调用底层的订阅服务，告诉 ReactVM 的节点：
        // "请帮我死死盯住以太坊(Chain ID 1)上，这个地址抛出的这个 Topic 0 事件！"
        ISubscriptionService(_subscriptionService).subscribe(
            ETH_MAINNET_ID, 
            CHAINLINK_ETH_USD_ADDRESS, 
            ANSWER_UPDATED_TOPIC_0, 
            0, 0, 0 // 其他 topic 留空，表示我们只根据 Topic 0 过滤
        );
    }

    // ---------------- 第二步：响应 (React) ----------------
    // 这是 Reactive Network 节点在监听到事件后，会自动、瞬间触发的“回调函数”
    // 注意：这个函数不需要任何普通用户 (EOA) 去花 Gas 费调用！
    function react(
        uint256 chain_id,
        address _contract,
        uint256 topic_0,
        uint256 topic_1,
        uint256 topic_2,
        uint256 topic_3,
        bytes calldata data,         // 预言机传上来的具体数据（比如价格）都在这里
        uint256 block_number,
        uint256 op_code
    ) external override {
    
        // 1. 二次确认：这是不是我们要的那个价格更新事件？
        if (topic_0 == ANSWER_UPDATED_TOPIC_0) {
            
            // 2. 解析数据：从 data 中解构出 Chainlink 传过来的最新价格 (current)
            int256 latestPrice = abi.decode(data, (int256));
            
            // 3. 执行自动化业务逻辑！
            // 假设我们设定了一个跌破 2000 美元就清算的红线 (注意 Chainlink 默认有 8 位小数)
            if (latestPrice < 2000 * 10**8) {
                
                // 触发后续的链上操作（比如：向目标链发送指令，清算某个用户的借贷仓位）
                // executeCrossChainLiquidation(...);
                
            }
        }
    }
}
```

这就实现了真正的自动化和无需EOA触发。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->



# **L1.3 - ReactVM和睿应式网络**

# 双重实例

一个响应式合约虽然只有一套代码，但它会同时运行在两个不同的环境中：**Reactive Network (RN)** 和 **ReactVM**。即每个响应式合约在部署后，实际上存在两个物理上的实例：

-   **Reactive Network (RN) 实例**：表现得像传统的 EVM 区块链，负责与系统合约交互，管理事件的订阅（Subscribe）和取消订阅（Unsubscribe）。  
    
-   **ReactVM 实例**：这是一个受限的隔离环境，专门用于**处理事件逻辑**。它不直接与外部交互，而是通过 RN 接收事件并发送回调请求。
    

Reactive Network的本质是普通的 EVM 区块链 + 额外的"系统合约"，其监听以太坊、BNB、Polygon、Optimism 等链上的事件，管理订阅关系（订阅/取消订阅），同时接收用户直接发起的交易。

而ReactVM本质是一个"隔离的小虚拟机"，每个部署者地址专属一个，它是专门用来**处理事件**的，同一地址部署的合约可以互相交互，但**不能**与其他地址的 Reactive Network 合约交互，用户也**不能**直接调用它。

Pasted image 20260310174807.png

ReactVM 虽然是隔离的，但可以通过**两种方式**与外界沟通：

① 源链发生事件 → Reactive Network 转发 → ReactVM 接收并处理

② ReactVM 处理完 → 发请求给 Reactive Network → 目标链执行回调

| 特性 | Reactive Network (前台/大厅) | ReactVM (实验室/引擎) |
| --- | --- | --- |
| 本质 | 标准的 EVM 区块链（类似以太坊）。 | 受限的、隔离的执行环境。 |
| 主要任务 | 管理订阅：负责记录你要听哪些链的事件。 | 处理逻辑：负责计算接收到的事件并决定做什么。 |
| 交互对象 | 任何人（用户可以调用它来修改设置）。 | 只有事件：不直接接触外界，只吃“事件数据”。 |
| 可见性 | 公开，所有节点都能看到状态。 | 隔离，同一部署者的合约才能互通。 |
| 输出结果 | 记录状态、变更订阅列表。 | 产生回调（Callback），让前台去执行。 |

我们可以把整个流程看作一个自动化工厂的运作：

1.  **订阅（在前台中）**：你在 Reactive Network 上调用合约，告诉它：“帮我盯着以太坊上的 Uniswap 交易事件。”
    
2.  **捕获（从源链到前台）**：Reactive Network 捕获到了那个交易事件。
    
3.  **计算（在实验室里）**：Reactive Network 把事件数据塞进 **ReactVM**。在这里，合约的 `react()` 函数开始运行，计算：“现在的价格达到我的止损线了吗？”
    
4.  **执行（从实验室回到前台）**：如果满足条件，ReactVM 发出一个指令给 Reactive Network，说：“去帮我给目标链发一个回调，执行卖出操作。”
    

所以在代码里，我们会看到：

```solidity
if (!vm) {
    // 这部分代码只会在“前台”跑，比如去注册订阅
} else {
    // 这部分逻辑只会在“实验室”里跑，处理具体的业务
}
```

双重实例的意思就是，代码里声明的变量（比如 `uint256 public price`），在 RN 里有一个值，在 ReactVM 里有另一个完全独立的值。它们就像是在平行宇宙里的同一个人，有着相同的外貌（代码），但经历（状态）完全不同。

使用双重实例是为了解决“高性能”**和**“安全性”的问题：

-   **并行处理（高性能）**：ReactVM 是按部署者地址隔离的。如果你部署了 100 个合约，它们可以在不同的 ReactVM 里同时跑，互不干扰，也不会堵塞 Reactive Network 主链。  
    
-   **状态隔离**：
    
    -   **RN 状态**：保存的是“行政数据”（比如：我是否暂停了服务？我订阅了哪个地址？）。  
        
    -   **ReactVM 状态**：保存的是“逻辑数据”（比如：上次捕获的价格是多少？我已经触发过回调了吗？）。
        

# 识别执行上下文

前面我们知道，同一套代码会在**两个环境**中运行，但有些函数**只能在特定环境中执行**，比如：

-   订阅事件 → 只能在 Reactive Network
    
-   处理事件逻辑 → 只能在 ReactVM
    

所以合约需要知道其当前处于的环境是哪个。

在 Reactive Network 的设计中，有一个特殊的系统合约地址：`0x0000000000000000000000000000000000fffFfF`。

-   **在 Reactive Network (RN) 中：** 这个地址是真实存在的，上面部署了系统逻辑（用来处理订阅）。  
    
-   **在 ReactVM 中：** 这是一个完全隔离的沙盒环境。为了安全和性能，这个系统合约地址在 ReactVM 里是**不存在的**，也就是“真空地带”。
    

合约通过一段简单的汇编代码来做检测：

```Solidity
function detectVm() internal {
    uint256 size;
    // 使用内联汇编获取目标地址的代码大小
    assembly { size := extcodesize(0x0000000000000000000000000000000000fffFfF) }
    // 如果大小为 0，说明这个地址没东西 -> 我在 ReactVM 里
    vm = size == 0;
}
```

用汇编的原因是 Solidity 本身没有直接检查"某个地址代码大小"的内置函数，必须用底层的 EVM 汇编指令来实现。这里不直接调用系统合约来判断原因是在 ReactVM 里，合约不存在，所以直接调用是会报错崩溃的。

# 强制执行上下文

我们通过 `detectVm()` 识别不同的环境，而下面我们需要给合约的不同功能装上**门禁**，让其在不同的环境下工作。

我们来设想一个场景，由于同一份代码在两个环境里跑，如果 `react()` 函数（专门处理繁重逻辑的）不小心在 Reactive Network（主网）上跑了，会发生什么？

-   **浪费 Gas**：主网上的计算非常昂贵。  
    
-   **逻辑冲突**：主网的状态和 VM 的状态不一样，混在一起会导致逻辑崩盘。
    

因此我们导入两个函数：

## `rnOnly()`

```solidity
modifier rnOnly() {
    require(!vm, 'Reactive Network only');
    _;
}
```

-   **逻辑**：它检查 `vm` 是不是 `false`。  
    
-   **直白解释**：如果你在 ReactVM（实验室）里尝试调用带这个修饰符的函数，它会直接报错弹出。  
    
-   **例子**：比如 `pause()` 函数。你只想在主网上通过手动操作来暂停合约，而不希望它在处理某个事件时被自动触发。
    

## `vmOnly()`

```solidity
modifier vmOnly() {
    require(vm, 'VM only');
    _;
}
```

-   **逻辑**：它检查 `vm` 是不是 `true`。  
    
-   **直白解释**：这个函数只能在 ReactVM 那个“黑盒子”里运行。  
    
-   **例子**：核心函数 `react(LogRecord calldata log)`。这个函数是专门给 ReactVM 的引擎调用的，它接收外界的事件并决定要不要做跨链操作。
    

* * *

# 双重变量

我们已经知道同一套代码在**两个环境**中运行，且各自有**独立的状态**。但是但这就带来一个问题：“合约里的变量，到底是给哪个环境用的？”

答案就是给两个环境各准备一套专属变量。需要注意的是，变量在两个环境中是独立的，也就是在概念上是两个变量。

## RN变量

RN变量继承自 `AbstractReactive` 合约，当你写 `contract MyContract is AbstractReactive` 时，不需要自己定义，系统会自动塞给你两个重要的工具：

-   `vm` **(布尔值)**：这就是判断环境的函数。合约运行的一瞬间，它就知道自己是在 **ReactVM (true)** 还是 **Reactive Network (false)**。  
    
-   `service` **(接口)**：这是一个“电话机”。只有通过它，你才能告诉系统：“我要监听哪条链上的哪个动作。”
    

## RM变量

RM变量是更加关键的部分，其在你自己的响应式合约里声明，职责就是**记录业务逻辑所需的数据**和在`react()` **函数中被读取和修改**

## 实际运行

以Uniswap 止损订单响应式合约的构造函数为例子，如下：

```solidity
// State specific to ReactVM instance of the contract.  
  
bool private triggered;  
bool private done;  
address private pair;  
address private stop_order;  
address private client;  
bool private token0;  
uint256 private coefficient;  
uint256 private threshold;  
  
constructor(  
address _pair,  
address _stop_order,  
address _client,  
bool _token0,  
uint256 _coefficient,  
uint256 _threshold  
) payable {  
triggered = false;  
done = false;  
pair = _pair;  
stop_order = _stop_order;  
client = _client;  
token0 = _token0;  
coefficient = _coefficient;  
threshold = _threshold;  
  
if (!vm) {  
service.subscribe(  
SEPOLIA_CHAIN_ID,  
pair,  
UNISWAP_V2_SYNC_TOPIC_0,  
REACTIVE_IGNORE,  
REACTIVE_IGNORE,  
REACTIVE_IGNORE  
);  
service.subscribe(  
SEPOLIA_CHAIN_ID,  
stop_order,  
STOP_ORDER_STOP_TOPIC_0,  
REACTIVE_IGNORE,  
REACTIVE_IGNORE,  
REACTIVE_IGNORE  
);  
}  
}
```

在代码中`triggered = false` 等初始化变量在两个环境中都初始化，但是`if (!vm)`里的关于监听的代码只在VN中进行。这些变量都是为了`react()`中的逻辑判断。以下是例子：

```solidity
// Methods specific to ReactVM instance of the contract.  
function react(LogRecord calldata log) external vmOnly {  
assert(!done);  
  
if (log._contract == stop_order) {  
if (  
triggered &&  
log.topic_0 == STOP_ORDER_STOP_TOPIC_0 &&  
log.topic_1 == uint256(uint160(pair)) &&  
log.topic_2 == uint256(uint160(client))  
) {  
done = true;  
emit Done();  
}  
} else {  
Reserves memory sync = abi.decode(log.data, ( Reserves ));  
if (below_threshold(sync) && !triggered) {  
emit CallbackSent();  
bytes memory payload = abi.encodeWithSignature(  
"stop(address,address,address,bool,uint256,uint256)",  
address(0),  
pair,  
client,  
token0,  
coefficient,  
threshold  
);  
triggered = true;  
emit Callback(log.chain_id, stop_order, CALLBACK_GAS_LIMIT, payload);  
}  
}  
}
```

`react()`函数被标记为`vmOnly`，它处理两种情报：

**来自** `stop_order` **合约的反馈**

```solidity
if (log._contract == stop_order) {
    if (triggered && ... ) {
        done = true; // 任务圆满完成，贴上封条
        emit Done();
    }
}
```

-   **逻辑**：它在等一个信号。如果它发现已经触发了卖出（`triggered` 为真），并且收到了目标链传回来的“确认卖出成功”的日志，它就会把 `done` 设为 `true`。代表这笔止损交易彻底结束了。
    

**来自 Uniswap** `pair` **的价格波动**

```solidity
else {
    Reserves memory sync = abi.decode(log.data, ( Reserves )); // 解码价格情报
    if (below_threshold(sync) && !triggered) { // 跌破线了且还没触发过
        triggered = true; // 立刻锁定开关，防止二次触发
        emit Callback(...); // 向 Reactive Network 发送“攻击指令”
    }
}
```

-   **逻辑**：它不断接收 Uniswap 的储备量（Reserves）数据。一旦计算发现价格跌破了 `threshold`，它会执行两件事：
    
    1.  把 `triggered` 设为 `true`（从此之后，这个 `if` 就再也进不来了，防止重复触发）。
        
    2.  发出 `Callback` 事件。**注意：在 ReactVM 里发出** `Callback` **事件，就相当于向 Reactive Network 发出了一个“跨链代操作”的请求。**
        

# 交易执行

使用响应式合约（RC）时，交易主要发生在两个环境中：睿应式网络（Reactive Network）和 ReactVM。每个环境在发起和处理交易方面均遵循不同的规则。

## RN交易

在 Reactive Network (RN) 这一侧，交易就像你在以太坊上操作合约一样，是**显式**的。这里的交易发起者是合约管理员，而操作RN交易的原因一般是执行管理任务，比如“现在市场太波动了，我要暂时关闭这个止损程序”。

比如`pause()` 和 `resume()`

-   这两个函数带有 `rnOnly`。  
    
-   它们会调用 `service.unsubscribe`。这就像是你打电话给接线员说：“别再把那个交易对的消息转接给我了。”  
    
-   **结果：** 这一动作发生在 RN 的存储状态里，它直接切断了传往 ReactVM 的“情报流”。
    

同时，RN也可以发起一些事件分发交易，这里就是说订阅事件的时候，当源链（如 Ethereum）产生一个事件时，RN 会启动一个内部交易，把这个事件“快递”给对应的 ReactVM 实例。

## RM交易

在 ReactVM 内部，唯一的触发源就是来自 RN 的事件分发，收到RN的分发后去执行`react()`函数，如果符合就去执行callback。

| 维度 | Reactive Network 交易 | ReactVM 交易 |
| --- | --- | --- |
| 发起者 | 用户（你）或系统节点 | 仅限系统转发的事件 |
| 可达性 | 公开，可以通过钱包/脚本调用 | 私密，外界无法直接触达 |
| 主要修饰符 | rnOnly | vmOnly |
| 目的 | 修改合约配置、订阅列表 | 运行算法逻辑、产生跨链回调 |
| Gas 消耗 | 消耗 RN 链上的 Gas | 运行逻辑本身不耗 RN Gas（沙盒执行） |

# L1.4 - **订阅机制**

# 核心接口

## ISubscriptionService 接口

**订阅服务接口**提供订阅和取消订阅的能力：

```solidity
interface ISubscriptionService is IPayable {
    function subscribe(
        uint256 chain_id,    // 监听哪条链
        address _contract,   // 监听哪个合约
        uint256 topic_0,     // 事件签名（keccak256哈希）
        uint256 topic_1,     // 索引参数1
        uint256 topic_2,     // 索引参数2
        uint256 topic_3      // 索引参数3
    ) external;
    
    function unsubscribe(...) external;  // 参数相同
}
```

## IReactive 接口

这是**响应式合约接口**定义了合约如何接收和处理事件：

```solidity
interface IReactive is IPayer {
    
    // 事件日志的完整数据结构
    struct LogRecord {
        uint256 chain_id;      // 事件来自哪条链
        address _contract;     // 事件来自哪个合约
        uint256 topic_0;       // 事件类型
        uint256 topic_1;       // 索引参数1
        uint256 topic_2;       // 索引参数2
        uint256 topic_3;       // 索引参数3
        bytes data;            // 非索引的额外数据
        uint256 block_number;  // 事件所在区块号
        uint256 op_code;       // 操作码（事件分类）
        uint256 block_hash;    // 所在区块哈希
        uint256 tx_hash;       // 触发事件的交易哈希
        uint256 log_index;     // 日志在交易中的索引
    }

    // 向目标链发送回调的事件
    event Callback(
        uint256 indexed chain_id,   // 目标链ID
        address indexed _contract,  // 目标合约地址
        uint64 indexed gas_limit,   // 最大gas限制
        bytes payload               // 调用数据
    );
    
    // 处理事件的核心函数
    function react(LogRecord calldata log) external;
}
```

# 静态订阅

## 用法

静态订阅的基本写法如下：

```solidity
address private _callback;  // RN 专用变量
uint256 public counter;     // ReactVM 专用变量

constructor(
    address _service,
    address _contract,
    uint256 topic_0,
    address callback
) payable {
    service = ISystemContract(payable(_service));
    
    if (!vm) {  // 只在 Reactive Network 上订阅
        service.subscribe(
            CHAIN_ID,
            _contract,
            topic_0,
            REACTIVE_IGNORE,  // 不过滤 topic_1
            REACTIVE_IGNORE,  // 不过滤 topic_2
            REACTIVE_IGNORE   // 不过滤 topic_3
        );
    }
    
    _callback = callback;
}
```

对于订阅条件的配置，存在三种通配符：

```
address(0) → 匹配任意合约地址
uint256(0) → 匹配任意链ID
REACTIVE_IGNORE → 匹配任意 topic 值
```

例如，监听特定合约的所有事件：

```solidity
service.subscribe(
    CHAIN_ID,
    0x7E0987E5b3a30e3f2828572Bb659A548460a3003,  // 指定合约
    REACTIVE_IGNORE,  // 任意事件类型
    REACTIVE_IGNORE,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE
);
```

这个订阅的效果是该合约发出的任何事件都会触发 `react()`。

而监听特定事件类型（跨所有合约）的订阅如下：

```solidity
service.subscribe(
    CHAIN_ID,
    address(0),   // 任意合约
    0x1c411e9a96e071241c2f21f7726b17ae89e3cab4c78be50e062b03a9fffbbad1,  // Uniswap V2 Sync
    REACTIVE_IGNORE,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE
);
```

效果就是所有合约发出的 Sync 事件都会触发 `react()`  
（可监控全网所有 Uniswap V2 Pair 的价格变动）

同时也可以监听特定合约+特定事件的组合：

```solidity
service.subscribe(
    CHAIN_ID,
    0x7E0987E5b3a30e3f2828572Bb659A548460a3003,  // 指定合约
    0x1c411e9a96e071241c2f21f7726b17ae89e3cab4c78be50e062b03a9fffbbad1,  // 指定事件
    REACTIVE_IGNORE,
    REACTIVE_IGNORE,
    REACTIVE_IGNORE
);
```

最后，多个订阅也支持同时监听多个来源：

```solidity
constructor(...) payable {
    if (!vm) {
        // 订阅1：监听合约1的所有事件
        service.subscribe(CHAIN_ID, _contract1, REACTIVE_IGNORE, ...);
        
        // 订阅2：监听全网的某类事件
        service.subscribe(CHAIN_ID, address(0), topic_0, ...);
        
        // 可以继续添加更多订阅...
    }
}
```

## 禁止事项

-   不能用 `>` `<` 范围过滤参数
    
    假设有一个代币合约，每次有人转账就会发出一个事件： `Transfer(address from, address to, uint256 amount)`。
    
    如果你想监听“金额大于 1000 的大额转账”，在响应式网络**不能在** `subscribe` **订阅时写条件：** 你不能要求系统合约（`ISystemContract`）帮你拦截小于 1000 的转账。它没有做数学运算（>、<、>=）的能力。
    
    在订阅时，必须对金额参数使用 `REACTIVE_IGNORE`（无视金额，全部监听）。系统会把**每一笔**转账（哪怕只有 1 块钱）都推送给你的合约。再在自己的代码中进行筛选。
    
-   不能在单次订阅中用 `OR`（或）逻辑
    
    在写代码时，你不能在一次 `subscribe()` 调用里监听 A 合约 **或者** B 合约。如果既想监听 A，又想监听 B，必须在代码里写**两行**独立的 `subscribe()` 函数调用。
    
-   不能同时订阅所有链的所有事件
    
    假设你把链 ID 设置为 `0`（任意链），把合约地址设置为 `address(0)`（任意合约），把事件也全设为 `REACTIVE_IGNORE`（任意事件）。这意味着你要求系统把全世界所有区块链上的**每一次转账、每一次交互统统发给你**。这不仅毫无意义，而且会瞬间让网络瘫痪，所以系统直接在源头禁止了这种“贪心”操作。你必须至少提供**一个**具体的目标（比如指定一个具体的合约，或者一个具体的事件）。
    
-   不能订阅某条链上的所有事件
    
    和上一条类似。就算你指定了“我只听以太坊（Sepolia）上的”，但不指定具体合约和事件，这也是被禁止的。以太坊上每秒钟发生无数笔交易，把这些全推送给你，你的合约根本处理不过来，你的手续费（Gas）也会在一秒钟内被扣光。
    
-   重复订阅：技术上允许但按次收费，需避免
    
    该事件发生时， `react` 函数会被**触发两次**，同时你要**付两次的手续费**。因为在区块链底层去查重是非常昂贵的（耗费存储空间），为了省整个网络的钱，系统把“防止重复订阅”的责任交给了开发者，需要在写代码时自己确保逻辑严密。
    

## 取消订阅

取消订阅是很贵的，是因为以太坊虚拟机（EVM）的**数据存储结构**决定的。

当你的合约调用 `subscribe` 时，系统合约在底层通常是把你这套条件（链 ID、合约地址、Topic 等）打包成一条记录，**直接塞到一个列表（数组）的最后面**。这是很快捷的操作。

当你的合约调用 `unsubscribe` 时。系统合约为了找到你当初设定的那条规则，必须经历以下步骤：

-   **第一步：极其昂贵的“搜索”（Storage Read）** 区块链上的存储（Storage）不像我们平时的数据库那样有高级的索引功能。系统合约可能需要**从头到尾遍历（Loop）整个订阅列表**，一条一条地去核对。在 EVM 里，每一次读取底层存储（也就是 `SLOAD` 操作码）都是要收费的。列表越长，它找得越久，消耗的 Gas 费就越高。  
    
-   **第二步：麻烦的“移除与填坑”（Storage Write）** 好不容易找到了你那条记录，把它删掉之后，列表里就出现了一个“空洞”。为了节省空间和保持列表的整洁，智能合约通常会把列表里的**最后一条记录搬过来，填补这个空洞**，然后再把列表的长度减一。 这个过程涉及到修改存储数据（`SSTORE` 操作码），而在区块链上，**“修改和写入数据”是所有操作里最贵、最耗费 Gas 的**，因为全网的节点都要跟着同步修改硬盘。
    

# 动态订阅

动态订阅就是运行时根据事件内容，动态增加/删除订阅。

首先，直接实现动态订阅在VN/VM中是不可能的，假设你想在收到某个事件时，立刻新增一个订阅。但是，负责处理事件的 `react` 函数是运行在 ReactVM（虚拟机）里的，而能办理订阅业务的 `ISystemContract`（系统合约）只存在于 RN 主网上。虚拟机里的代码，摸不到主网的系统合约。

# 步骤

动态订阅依然得在构造函数里做一次静态订阅。但这次你订阅的不是具体的业务事件，而是“控制指令”。

```solidity
constructor(ApprovalService service_) payable {
    owner = msg.sender;
    approval_service = service_;
    
    if (!vm) {
        // 静态订阅①：监听"有人想订阅"这个事件
        service.subscribe(
            SEPOLIA_CHAIN_ID,
            address(approval_service),  // 监听 ApprovalService 合约
            SUBSCRIBE_TOPIC_0,          // 订阅请求事件
            REACTIVE_IGNORE,
            REACTIVE_IGNORE,
            REACTIVE_IGNORE
        );
        
        // 静态订阅②：监听"有人想取消订阅"这个事件
        service.subscribe(
            SEPOLIA_CHAIN_ID,
            address(approval_service),  // 监听 ApprovalService 合约
            UNSUBSCRIBE_TOPIC_0,        // 取消订阅请求事件
            REACTIVE_IGNORE,
            REACTIVE_IGNORE,
            REACTIVE_IGNORE
        );
    }
}
```

接着，用户发起订阅请求：

```
用户A 在 Sepolia 链上调用：
ApprovalService.requestSubscribe(userA_address)

ApprovalService 合约内部发出事件：
emit Subscribe(userA_address)  
→ topic_0 = SUBSCRIBE_TOPIC_0
→ topic_1 = userA_address
```

当VN监听到了这个请求后，将其传递到VM执行`react()`函数。

```solidity
function react(LogRecord calldata log) external vmOnly {
    
    //  收到了"有人想订阅"的事件
    if (log.topic_0 == SUBSCRIBE_TOPIC_0) {
        
        // 第①步：从事件中取出想要订阅的用户地址
        address subscriber = address(uint160(log.topic_1));
        // log.topic_1 就是 userA_address！
        
        // 第②步：编码要在 RN 上调用的函数
        bytes memory payload = abi.encodeWithSignature(
            "subscribe(address,address)",
            address(0),    // rvm_id 占位（系统自动填入）
            subscriber     // 要监听的用户地址
        );
        
        // 第③步：发出 Callback，请求 RN 执行
        emit Callback(
            REACTIVE_CHAIN_ID,  // 目标：Reactive Network
            address(this),      // 目标合约：本合约的 RN 实例
            CALLBACK_GAS_LIMIT, // gas 限制
            payload             // 调用数据
        );
    }
    ...
}
```

ReactVM 自己无法调用 `service.subscribe()`，但它可以通过 `emit Callback` 给 RN 发消息，调用其订阅。这里RN收到了callback，然后执行订阅：

```solidity
function subscribe(address rvm_id, address subscriber) 
    external 
    rnOnly           // 只能在 RN 环境执行
    callbackOnly(rvm_id)  // 只能由 service 以 owner 身份触发
{
    service.subscribe(
        SEPOLIA_CHAIN_ID,
        address(0),                      // 任意合约
        APPROVAL_TOPIC_0,                // ERC20 Approval 事件
        REACTIVE_IGNORE,                 // topic_1 不过滤
        uint256(uint160(subscriber)),    // topic_2 = 指定用户地址！
        REACTIVE_IGNORE                  // topic_3 不过滤
    );
}
```

新增订阅后，假设现在 userA 在某个 ERC20 合约上调用 approve()：

```
USDC.approve(spender, 1000)

发出事件：

Approval(owner=userA, spender=xxx, value=1000)

→ topic_0 = APPROVAL_TOPIC_0

→ topic_1 = userA (owner)

→ topic_2 = spender

→ data = 1000 (amount，非索引参数)
```

VN监听到这个调用并传递给VM，这时候VM执行真正的逻辑：

```solidity
function react(LogRecord calldata log) external vmOnly {
    ...
    // 情况3：收到真正的 Approval 事件
    else {
        // 从 data 字段解码出授权金额
        (uint256 amount) = abi.decode(log.data, (uint256));
        
        // 编码要在 Sepolia 上调用的函数
        bytes memory payload = abi.encodeWithSignature(
            "onApproval(address,address,address,address,uint256)",
            address(0),                      // 占位
            address(uint160(log.topic_2)),   // spender 地址
            address(uint160(log.topic_1)),   // owner（userA）地址
            log._contract,                   // 哪个 token 合约
            amount                           // 授权金额
        );
        
        // 回调 Sepolia 上的 ApprovalService
        emit Callback(
            SEPOLIA_CHAIN_ID,               // 目标：Sepolia 链
            address(approval_service),       // 目标：ApprovalService 合约
            CALLBACK_GAS_LIMIT,
            payload
        );
    }
}
```

而如果要取消订阅：

```solidity
// react() 中的取消订阅处理
else if (log.topic_0 == UNSUBSCRIBE_TOPIC_0) {
    bytes memory payload = abi.encodeWithSignature(
        "unsubscribe(address,address)",
        address(0),
        address(uint160(log.topic_1))  // 要取消的用户地址
    );
    emit Callback(REACTIVE_CHAIN_ID, address(this), CALLBACK_GAS_LIMIT, payload);
}

// RN 上的取消订阅函数
function unsubscribe(address rvm_id, address subscriber)
    external
    rnOnly
    callbackOnly(rvm_id)
{
    service.unsubscribe(
        SEPOLIA_CHAIN_ID,
        address(0),
        APPROVAL_TOPIC_0,
        REACTIVE_IGNORE,
        uint256(uint160(subscriber)),  // 移除对该用户的监听
        REACTIVE_IGNORE
    );
}
```

| 步骤 | 发生了什么 | 在哪里 |
| --- | --- | --- |
| 1 | 用户发出订阅请求事件 | Sepolia 链 |
| 2 | react() 检测到请求 | ReactVM |
| 3 | react() emit Callback | ReactVM |
| 4 | 系统转发 Callback | Reactive Network |
| 5 | subscribe() 被调用 | Reactive Network |
| 6 | service.subscribe() 新增监听 | Reactive Network |
| 7 | 业务事件触发新的 react() | ReactVM |
| 8 | 回调目标链执行业务逻辑 | Sepolia 链 |

## 双重验证

在新增监听的时候，需要进行双重验证：

```solidity
require(msg.sender == address(service), 'Callback only');
```

首先需要验证直接调用这个函数的人是否是系统合约，也就是callback，防止其他人恶意调用新增订阅。

```solidity
require(evm_id == owner, 'Wrong EVM ID');
```

然后还需要检查这张跨链回调单子的最初发起人（`evm_id`，即在 ReactVM 里触发事件的那个地址），是不是这个合约的真正主人（`owner`）。因为其他的ReactVM也可以调用callback。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->




# 睿应式合约

**睿应式智能合约（RCs）** 是一种范式转变。

-   **传统合约（EVM）**：是“被动”的。它像一个上锁的柜子，除非有人拿着钥匙（发交易）去开它，否则它永远不会动。  
    
-   **睿应式合约（RCs）**：是“主动”的。它像一个带传感器的机器人，它会盯着其他区块链上的“风吹草动”（事件日志），一旦发现符合条件的情况，它就自己根据逻辑去执行动作。
    

通过依据预设逻辑处理事件，并自主在区块链上执行后续操作。这是一种更去中心化的自动化机制，可在无需人工干预的情况下，自动响应链上事件。

当前以太坊生态的一个**痛点：对中心化脚本/机器人的依赖**。_如果你想做一个“自动止损”功能，传统合约自己做不到。你必须写一个链外的 Python 或 JS 脚本（机器人），给它私钥，让它不断扫描链上价格，然后由这个机器人发交易。_ 那么这样的一个模式存在风险：

-   **中心化风险**：如果你的机器人服务器宕机了，自动化就失效了。  
    
-   **安全风险**：你必须把私钥存在服务器上，容易被黑。
    

Reactive将这种“监听+触发”的逻辑直接写进区块链底层（睿应层），由去中心化的网络来保证执行。

Pasted image 20260309190009.png

# 优点

## 1\. 去中心化 (Decentralization)

传统的自动化方案通常依赖于运行在中心化服务器（如 AWS 或 Google Cloud）上的脚本。

-   **消除单点故障**：RCs 独立运行在区块链上，消除了对中心化控制点的依赖。  
    
-   **提高安全性**：由于逻辑在去中心化网络中执行，降低了被恶意操纵或因单点服务器崩溃而导致失败的风险。  
    
-   **信任最小化**：你不需要信任某个开发者运行的机器人，只需要信任睿应层（Reactive Network）的代码执行逻辑。
    

## 2\. 全自动化 (Automation)

这是对传统 EVM “被动触发”模式的根本性改变。

-   **自主执行**：RCs 能够根据链上事件自动执行逻辑，无需任何外部手动干预。  
    
-   **高效实时**：它允许系统对实时发生的链上数据做出秒级响应，这在瞬息万变的 DeFi 市场中至关重要。  
    
-   **减少人工成本**：不需要开发者持续监控链上状态并手动发送交易。
    

## 3\. 跨链互操作性 (Cross-Chain Interoperability)

在多链并行的时代，这是 RCs 的“杀手锏”。

-   **打破孤岛**：RCs 可以同时与多个区块链进行交互，充当不同网络之间的桥梁。  
    
-   **复杂交互**：它支持跨链触发逻辑，例如在以太坊上的操作可以自动触发 Polygon 或其他 EVM 链上的后续动作。  
    
-   **原生支持**：这种能力是架构原生自带的，而不需要引入额外的、可能存在安全隐患的第三方跨链协议。
    

## 4\. 增强的效率与功能性 (Enhanced Efficiency & Functionality)

RCs 为开发者提供了更强大的工具箱，去实现以前“不可能完成的任务”。

-   **基于实时数据的反应**：通过直接对实时数据做出反应，RCs 极大地提升了智能合约的运行效率。  
    
-   **支持高级应用**：它使得构建复杂的金融衍生品、动态 NFT（根据链上行为自动进化）以及创新的 DeFi 应用成为可能。  
    
-   **生态互联**：它创造了一个响应更迅速、连接更紧密的区块链生态系统，让原本零散的功能能够串联成自动化流。
    

# 控制反转

考虑一个场景，在没有控制反转的情况下，如果你想实现自动化（例如：当币价跌到 100 时自动卖出），你必须依赖**外部实体**：

-   **传统方案的局限**：你需要设置一个中心化的“机器人 (Bot)”来监控链上数据。这个机器人持有你的私钥，并在满足条件时替你签名发送交易。  
    
-   **IoC 的优势**：
    
    -   **无需机器人**：不再需要托管额外的实体来模拟人类签名。  
        
    -   **去中心化自动化**：只要输入和输出都在链上，逻辑就可以在去中心化的网络中完全自主运行。  
        
    -   **消除单点故障**：避免了因机器人服务器宕机或私钥泄露导致的风险。
        
-   **传统模式**：执行逻辑由**外部力量**（普通用户 EOA 或中心化机器人）启动。合约是“被动”的，没有外部指令它就是死的。  
    
-   **睿应模式**：执行逻辑由**合约自身**根据预设事件决定何时运行。控制权从外部转移到了合约内部。  
    
-   **为什么要“反转”？** 传统的自动化需要你运行一个中心化机器人，给它私钥，让它不断扫描链上状态并签名发交易。这不仅存在单点故障风险，还引入了私钥管理的安全性挑战。RCs 允许你在**完全去中心化**的环境下运行这种逻辑。
    

Pasted image 20260309191204.png

# 运作机制

一个睿应合约的生命周期包含三个关键环节：

-   **监听定义**：在创建 RC 时，你首先需要指定关心的**链、合约地址以及事件（Topic 0）**。这包括转账、DEX 交易、贷款、投票等任何智能合约活动。  
    
-   **有状态处理 (Stateful)**：当监听到目标事件时，Reactive Network 会自动执行你写的逻辑。RCs 是**有状态的**，这意味着它可以存储历史数据。例如，它不只是看当前这一笔交易，还可以结合过去一小时的数据进行计算。  
    
-   **自动触发**：根据计算结果，RC 会更新状态并自主在目标链上发起交易。
    

## 1\. 订阅与配置 (Input / Monitoring)

在创建一个 RCs 时，开发者首先需要定义“感兴趣的事件”。

-   **指定目标**：开发者需明确要监控的**区块链（Chains）**、**合约地址（Contracts）**以及具体的**事件主题（Topic 0）**。  
    
-   **监控范围**：监控的活动可以包括代币转账、DEX 兑换、借贷记录、投票、大户（Whale）变动或任何智能合约的活动。  
    
-   **实时捕获**：睿应网络（Reactive Network）会持续监测这些地址，一旦检测到匹配的事件，便会启动执行流程。
    

## 2\. 逻辑处理与状态管理 (Processing / Stateful Logic)

一旦检测到事件，睿应网络会自动运行开发者编写的逻辑。

-   **有状态执行 (Stateful)**：RCs 具有“状态”属性，这意味着它们不仅能处理当前数据，还能存储并更新数值。  
    
-   **数据聚合**：合约可以在状态中累积历史数据。当“历史数据”与“新链上事件”的组合满足特定标准时，才会触发动作。  
    
-   **逻辑计算**：RCs 可以执行复杂的计算，例如计算 Uniswap 池中的流动性和实时汇率，或者聚合来自多个预言机的数据并取平均值。
    

## 3\. 自主执行交易 (Output / Action)

这是 RCs 区别于传统合约的关键点：

-   **自动触发交易**：基于逻辑计算的结果，RCs 会自动更新其状态，并可以在目标 EVM 区块链上发起交易。  
    
-   **无需外部签名**：整个过程在睿应网络内通过去中心化的方式运行，不需要人类用户或中心化机器人（Bot）持私钥签名发起交易。  
    
-   **信任最小化**：执行过程是去中心化且不可篡改的，确保了响应的自动化、快速和可靠。
    

# 应用场景

## 多预言机数据聚合

我们设定一个航班延误险自动理赔的场景，目标是如果某航班延误超过 2 小时，自动给投保人赔付 1 ETH。

**数据源（预言机）**：为了防止单一数据源被操纵，我们需要聚合三个来源：

1.  **Chainlink（在以太坊上）**：提供官方民航数据。
    
2.  **Pyth Network（在 Polygon 上）**：提供实时机场雷达数据。
    
3.  **第三方商业预言机（在 Arbitrum 上）**：提供航空公司官方 API 数据。
    

在传统 EVM 中，由于合约无法主动获取跨链数据，你必须：

-   **依赖机器人**：雇佣一个运行在中心化服务器（如 AWS）上的 Python 脚本。  
    
-   **手动中转**：机器人要盯着三条链，等三个预言机都更新后，由机器人拿着你的**私钥**，算好平均值，再发一笔交易到保险合约触发理赔。  
    
-   **风险**：如果机器人服务器断电了，或者私钥被黑了，理赔就永远不会发生。
    

而使用 **Reactive Contracts (RCs)**，整个流程变成了全自动的：

-   **主动监听**：一个 RC 合约同时订阅以太坊、Polygon 和 Arbitrum 上这三个预言机的“数据更新”事件。  
    
-   **自主决策**：当 RC 发现三个数据都到齐了，它在睿应层内部自动计算：“3 个中有 2 个显示延误 > 2 小时吗？”。  
    
-   **自动理赔**：一旦结论为“是”，RC 直接向目标链发起理赔交易。**全程不需要任何人工或外部机器人干预**。
    

在多预言机场景下，最难的不是“获取数据”，而是“谁来决定什么时候该聚合数据”。在睿应层，这个决定权反转到了合约自己手里。

## Uniswap 止损单

在传统的去中心化交易所（DEX）如 Uniswap 中，“止损单”是很复杂的。如果你想在 $2500 卖出 ETH 止损，由于以太坊合约是**被动**的，它无法感知时间或价格的变化。你必须编写一个运行在中心化服务器上的 Python/JS 机器人（Bot）。这同样有上述说的私钥和中心化的风险。

而使用Reactive，步骤就变为十分简单的三步：

-   **监听**：RC 订阅 Uniswap 池中的 `Swap` 事件。  
    
-   **计算**：每当有人在 Uniswap 交易，RC 会在睿应层内实时计算当前兑换率和流动性。  
    
-   **触发**：一旦计算结果显示价格跌破 $2500，RC 自动向目标链发起卖出交易。
    

## DEX套利

我们先假设一个具体的套利场景：ETH 的价格在 **Ethereum 的 Uniswap** 还是 $2500，但在 **Polygon 的 QuickSwap** 已经涨到了 $2510。这里存在 $10 的利差。

传统方案中，你需要在服务器上运行一个脚本，不断通过 API 轮询（Polling）两个链的价格。发现差价后，脚本指挥你的钱包分别在两条链发起交易。这中间比较严重的问题是，轮询 API 有延迟，等你发现机会并发送交易时，机会可能已被抢走。

而部署一个 **Reactive Contract (RC)**，它像神经末梢一样直接“长”在区块链的事件流上。RC 在睿应层内部是秒级感知的，并且自动判断获利空间。如果满足，它通过**控制反转 (IoC)** 机制，自主向目标链发起套利交易。

## 自动资金池再平衡

在一个多链生态中，同一个项目的流动性往往分布在多个不同的区块链上（例如以太坊、Polygon 和 Arbitrum）。

-   **痛点**：由于各链的交易量不均，某些链的资金可能会被买空（流动性枯竭），而另一些链的资金却处于闲置状态。  
    
-   **目标**：根据实时需求，自动将资金从“溢出”的链搬运到“短缺”的链，以维持最优的流动性效率。
    

传统方案中，通常需要项目方运行一个复杂的后端脚本。该脚本不断调取各链节点的 API，对比余额，然后由项目方控制的**多签钱包或管理员私钥**发起跨链转账。

在Reactive中，思路如下

-   **全局监控**：RC 同时订阅所有相关链上的流动性变动事件（如 `Mint`、`Burn`、`Swap`）。  
    
-   **有状态分析**：RC 是**有状态的（Stateful）**，它会实时记录并更新各链的资金比例。  
    
-   **自主决策**：一旦某个链的流动性低于阈值，RC 内部逻辑自动触发。  
    
-   **跨链回调**：RC 自动向目标链发出指令，执行资金拨付或重组。
    

# EVM中的事件

事件允许智能合约在满足特定条件时，通过记录特定信息来与外部世界进行通信。有了事件，去中心化应用（dApps）就可以直接响应发生的动作，而不需要不停地主动查询（轮询）区块链状态。

EVM 会对事件进行索引（Index），这使得监控区块链活动（如转账、价格变化）变得非常高效和容易。当合约触发事件时，数据被存储在交易的日志（Logs）中。这些日志虽然附着在区块链的区块上，但它们**不会直接影响区块链的状态**（例如，它们不会改变账户余额或合约变量的值）。

开发者通过两个核心关键字来使用事件：

1.  **定义（Define）**：使用 `event` 关键字，后接事件名称和要记录的数据类型。
    
    > 例如：`event PriceUpdated(string symbol, uint256 newPrice);`
    
2.  **触发（Emit）**：使用 `emit` 关键字来正式记录数据。
    
    > 例如：`emit PriceUpdated("ETH", newEthPrice);`
    

设想一个需要实时价格信息来执行其逻辑的智能合约，例如一个根据最新市场价格动态调整抵押品要求的去中心化金融（DeFi）借贷平台。该合约可能定义如下事件：

```solidity
event PriceUpdated(string symbol, uint256 newPrice);
```

当智能合约从 Chainlink 的预言机接收到新的价格更新时，会触发 `PriceUpdated` 事件：

```solidity
emit PriceUpdated("ETH", newEthPrice);
```

# 响应式合约中的事件处理

## 接口

为了实现自动化反应，响应式合约必须遵循一套标准的交互协议，即实现 `IReactive` 接口。

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

整个接口的核心数据包是 **LogRecord 结构**

当源链（如以太坊）发生事件时，睿应网络会将该事件打包成一个 `LogRecord` 结构体发送给合约。你可以把它想象成一封包含所有关键信息的“挂号信”：

具体来说：

-   `chain_id` ：事件来源的区块链 ID。
    
-   `_contract` ：发出该事件的合约地址。
    
-   `topic_0` 至 `topic_3` ：日志的已索引主题（indexed topics）。
    
-   `data` ：事件日志中的未索引数据。
    
-   `block_number` ：事件发生的区块编号。
    
-   `op_code` ：可能表示操作码。
    
-   `block_hash` 、 `tx_hash` 和 `log_index` ：用于追踪事件来源与上下文的其他标识符。
    

`react()`是睿应合约的核心入口。

-   **触发机制**：响应式网络会持续监控日志，一旦发现符合你设置的订阅条件的事件，就会**自动调用**这个 `react()` 方法，并将上述 `LogRecord` 丢进去。  
    
-   **动态处理**：通过解析输入的数据，合约可以动态决定接下来要做什么（比如：如果价格低于 X，就准备卖出）。  
    
-   **权限限制**：它被标记为 `external`，意味着它只接受来自网络层的主动触发，而不能被合约内部随意调用。
    

## Callback回调

响应式合约本身运行在睿应网络中，它不能直接在以太坊上写数据。因此，它通过发出一个特定格式的日志（即 `Callback` 事件），告诉睿应网络：“请帮我在那条链上执行这个操作”。

因此当 `react()` 函数决定要行动时，它不会直接去操作目标链，而是通过抛出一个 `Callback` 事件来发出指令。

-   这个事件包含了目标链的 ID、目标合约地址、Gas 限制以及最重要的 **payload（执行载荷）**。  
    
-   睿应网络捕捉到这个 `Callback` 后，会负责在目标链上把这笔交易“跑”出来。
    

具体来说：

-   `chain_id` ：该事件的区块链 ID。
    
-   `_contract` ：触发该事件的合约地址。
    
-   `gas_limit` ：为回调函数分配的最大 Gas 量。
    
-   `payload` ：随回调函数一同传递的编码数据。
    

整个过程是完全自动化且去中心化的：

1.  **触发（Emit）**：当响应式合约的逻辑判断需要采取行动时，它会 `emit Callback(...)`。
    
2.  **检测（Detect）**：睿应网络（Reactive Network）作为一个底层基础设施，会实时监控这些回调事件。
    
3.  **处理与提交（Process & Submit）**：一旦检测到事件，网络会解析 `payload` 中的交易详情，并使用你提供的 `chain_id` 和 `gas_limit`，在目标链上创建并提交一笔真实的交易。
    

需要注意的是，代码并不是直接跑在主网上，而是在一个名为 **ReactVM** 的私有沙箱中运行，运行后的callback才有睿应网执行跨链操作。

-   **隔离性**：响应式合约只能与**同一个部署者**部署的合约进行交互。  
    
-   **安全性**：这种隔离机制确保了即使在处理复杂的跨链事件时，合约环境也是受控且安全的，防止了恶意的跨合约攻击。
    

## 身份替换机制

在传统的跨链操作中，证明“谁发起了请求”通常非常复杂。睿应层通过协议层的强制操作简化了这一过程：

-   **强制覆盖**：当你构建 `payload`（执行载荷）时，无论你为第一个参数填入了什么值，响应式网络在将交易提交到目标链之前，都会**自动且强制性地**将前 160 位（即第一个参数的位置）替换为调用方合约的 **RVM ID**。  
    
-   **RVM ID 的本质**：这个 ID 等同于该响应式合约在 ReactVM 中的地址，且与该合约**部署者的地址**完全一致。  
    
-   **不可伪造性**：由于替换发生在网络底层，开发者无法通过代码篡改这个身份标识。这为目标链合约提供了一个不可信环境下的“信任原点”。
    

这一机制直接影响了你编写 Solidity 代码的方式：

-   **参数类型限制**：你目标链上的被调用函数，其**第一个参数必须是** `address` **类型**。  
    
-   **变量名无关性**：无论你在目标链合约中给第一个参数起什么名字（比如 `caller` 或 `origin`），它接收到的永远是发起请求的 RC 合约地址。
    

一个Uniswap 止损单例子：

```solidity
bytes memory payload = abi.encodeWithSignature(
   "stop(address,address,address,bool,uint256,uint256)",
   address(0),  // The ReactVM address
   pair,        // The Uniswap pair address involved in the transaction
   client,      // The address of the client initiating the stop order
   token0,      // The address of the first token in the pair
   coefficient, // A coefficient determining the sale price
   threshold    // The price threshold at which the sale should occur
);
emit Callback(chain_id, stop_order, CALLBACK_GAS_LIMIT, payload);
```

这里需要注意的是第一个参数填 `address(0)`，起到占位符作用。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
