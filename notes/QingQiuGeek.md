---
timezone: UTC+8
---

# QingQiu

**GitHub ID:** QingQiuGeek

**Telegram:** @qingqiu

## Self-introduction

本科大四，软件工程专业，已有两段java实习，略懂全栈，有从0到1开发全栈项目经历，前不久参加过一次Permissionless模式的web3冬季实习计划并坚持到最后，目前已经学习完solidity、go基础语法，对web3、ai有深厚兴趣

## Notes

<!-- Content_START -->
# 2026-03-19
<!-- DAILY_CHECKIN_2026-03-19_START -->
````markdown

用metamask生成新钱包时，MetaMask 随机生成 12 个助记词，然后推导出EOA账户地址。

- 助记词 (Mnemonic)：是这棵树的“根”。它本质上是把一串随机的二进制数字转换成 12 或 24 个易记的单词。拥有助记词就拥有了所有权限。

- 私钥：它是具体的“钥匙”。每一把钥匙控制一个具体的账户。

- 公钥：它是你的“指纹”。私钥可以推导出公钥，但公钥无法反推私钥（单向加密）。

- EOA 账户 (Externally Owned Account)：银行账号，由公钥经过哈希运算（如以太坊的 Keccak-256）并截取后生成的。它由私钥直接控制，不包含任何代码。

## 理解派生路径

```
m / 44' / 60' / 0' / 0 / 0
```

![](image-1.png)

- m (Master)
  代表根节点。对应 12 或 24 个助记词生成的“主种子”。所有的分支都是从这个 m 延伸出来的。

- 44' (Purpose)
  代表用途。44' 是一个固定值，意味着这个路径遵循 BIP44 标准（多币种层次确定性钱包标准）。看到 44，钱包就知道后面会跟着币种、账户等信息。

  注：末尾的 ' 号代表“硬化派生”（Hardened），通俗点说就是增加了一道防火墙，即使分支私钥泄露，也不会倒推威胁到主种子的安全。

- 60' (Coin Type)
  代表币种编号，不同的链有不同的编号：

  60'：以太坊 (ETH) 以及兼容链（Base, Starknet, BNB Chain 等）。这就解释了为什么钱包里有些链的EOA地址是一样的！

  0'：比特币 (BTC)。

  501'：Solana (SOL)。

  784'：Sui (SUI)。

- 0' (Account)
  代表逻辑账户索引。可以把它理解为“主钱包”、“次钱包”。

  一般用户默认都是 0'。如果你是一个高级玩家，想把资产完全隔离，你可以手动指定 1' 或 2'。它们在链上看起来完全无关。

- 0 (Change)
  代表找零路径。

  0：外部链（接收地址），用于生成你收钱的地址。

  1：内部链（找零地址），这在比特币（UTXO 模型）中常用，但在以太坊（账户模型）中几乎永远是 0。

- 0 (Address Index)
  代表具体的地址序号。这就是你在 MetaMask 里点“创建新账户”时改变的数字：

  第一个账户：m/44'/60'/0'/0/0

  第二个账户：m/44'/60'/0'/0/1

  第三个账户：m/44'/60'/0'/0/2
````
<!-- DAILY_CHECKIN_2026-03-19_END -->

# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->

## The Graph概念

区块链有一个巨大的、只能追加记录的账本（比如以太坊），这导致查询数据极其困难。

The Graph 就是为了解决这个问题而生的。它通过索引（Indexing）区块链数据，让开发者可以使用标准的 GraphQL 语言高效地查询链上信息。

## The Graph 的核心工作原理

The Graph 并不是直接修改区块链，而是在区块链之上加了一个“搜索层”：

-   事件监听： 它会监控区块链上的特定智能合约事件（Events）。
    
-   数据加工、存储： 当事件触发时，它根据开发者定义的逻辑，把原始的十六进制数据转换成易读的格式，并将处理后的数据存入数据库。
    

开发者通过 GraphQL 接口，瞬间就能查到复杂的数据关联。

## GraphQL

GraphQL 是一种用于 API 的查询语言，以及一个用于履行这些查询的运行时（Runtime）。

它最初由 Facebook 开发，旨在解决 REST API 的痛点。它的核心理念是：让客户端（前端）自己定义“我需要什么数据”，然后服务器（Backend/Graph）精确地返回这些数据。

而 REST API 是服务器定义好 JSON 结构。
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->



## Base

Base 是以太坊 Layer2 网络。由 Coinbase 基于 Optimism 的 OP Stack 技术栈构建。它是“乐观汇总”（Optimistic Rollup），**把成千上万笔交易打包在一起，然后传回以太坊主网进行结算**。它共享了以太坊的安全级别，但手续费比主网便宜 10 到 100 倍。

状态存储：**基于账户的模型（Account-based Model）**，以太坊维护着一个巨大的账本，包含所有账户余额、智能合约代码和合约数据的巨大树状结构（Merkle Patricia Tree）。当你转账时，以太坊会找到你的账户，减去金额，找到对方账户，加上金额。

由于所有交易都在**修改同一个全局账本**，为了保证账本不出错，**以太坊通常只能串行处理（一个接一个排队）**。这就是以太坊慢且贵的根本原因。

## Starknet

Starknet 也是以太坊 Layer2 网络。如果说 Base 是用 Optimism 技术，那 Starknet 使用的是更先进、技术难度更高的 ZK-Rollup（零知识证明），该技术可以防量子计算机（目前主流的加密算法如以太坊、比特币使用的 ECDSA 椭圆曲线加密，这在量子计算机面前非常脆弱，因为 Shor 算法可以轻易破解它们。）。

核心原理：Starknet 把几万笔复杂的交易拿到以太坊外面去执行，然后利用一种叫 STARKs 的数学证明技术，生成一个极小的“数学证明”。它把这个证明发回以太坊主网。以太坊只需要花极小的代价验证这个证明是否正确，就可以确认那几万笔交易的合法性。

Starknet使用一种模仿 Rust 的语言叫 Cairo。

原生账户抽象：在以太坊或 Solana 上，你的钱包通常只是个私钥。在 Starknet 上，每个钱包本身就是一个智能合约。

Starknet是先执行交易后生成证明，执行过程中如果有坏交易则会报错，所以到生成证明那一步坏交易已经不存在了，然后就会两两聚合、四四聚合……形成一个“证明树”，如果最终的根证明（Root Proof）失败，计算节点可以通过检查树的子节点，快速定位到是哪个子集出现了计算逻辑不一致。

如果主网验证失败，可能是证明生成软件本身有 Bug 或 L2 节点之间出现了状态分叉。2025 年 9 月 Starknet 发生过一次事故，就是因为两个序列器对以太坊的状态看法不一致（分叉），导致生成的证明与主网不匹配。当时的解决办法是人工干预并触发 L2 层面的重组（Reorg）。

## Solana

使用Rust开发，采用 PoH (Proof of History，历史证明)。它给每个交易打上时间戳，让节点不需要互相通信就能达成时间共识。

状态存储：账户模式(数据与逻辑分离)，合约代码逻辑不存储变量，账户数据存在于不同账户里。修改数据时不用排队，个玩个的，这意味着支持多核并行处理。

Solana 要求得先用代码指挥系统为每个用户创建一个“数据账户”。账户里必须留一点 SOL 作为“租金”（通常只要保持一定余额就免租），否则数据会被系统清理掉。

主要赛道：Meme、AI Agent、DePin

## Sui

Sui 是由Move语言开发，Move 语言被设计为“资产导向”，在处理代币增发、转账时，比 Rust 更安全、更不容易写出导致 Rug Pull 的漏洞。

以对象为中心，在其他链上，数据存在账户里；在 Sui 上，一切都是“对象”。如果你要给别人转一个 NFT，Sui 只需要处理这个 NFT 对象的所有权变更，完全不需要去动别人的账户数据。

支持异步处理。

状态存储：对象模式

主赛道：DeFi 创新、Web3 游戏、NFT
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->





[参考](https://aave.com/docs/aave-101)

Uniswap 是“去中心化交易所”，用户可以在此兑换货币，而 Aave 是“去中心化银行”，也是一种协议，部署在各种区块链上，用户可通过aave协议抵押、借贷。aave抵押的货币需要是aave支持的。

Aave 使用一条利用率曲线（借出的资金比例），该曲线在目标水平处有一个“拐点”：低于目标水平时，借款利率缓慢上升；高于目标水平时，利率上升幅度更大，以保障流动性。供应商利率由借款利息提供资金，并随着利用率的提高而上升。

举例，如果池子里有 100 块钱，现在已经被借走了 99 块。这时如果有存款人想取出他存的 10 块钱，池子就会因为没钱而“宕机”，导致取款失败。这是去中心化银行最大的灾难。为了防止这种情况，当利用率（借出的比例）太高时，Aave 就会提高借款利率，让借款成本更大，进而使得借款变少，同时提高借款利率，存款的人会变多，因为产生的利息更高。

## GHO

GHO（发音为“go”）是一种基于以太坊的 ERC-20、去中心化、超额抵押的稳定币，由Aave协议全额担保，透明且原生于该协议。GHO旨在与美元挂钩，用户可按需铸造，但需遵守Aave治理机制设定的铸造上限。其稳定性得益于Aave协议固有的市场效率和超额抵押机制。

GHO 采用代理模式运行。代理是经 Aave Governance 批准的合约，拥有 GHO 的铸造和销毁权限，每个代理的铸造量均受制于治理机制设定的上限。这种模式既能灵活扩展 GHO 的功能，又能保持对代币供应的去中心化控制。
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->







````markdown
## basic

### 源链合约

```
forge create --broadcast --rpc-url $ORIGIN_RPC --private-key src/demos/basic/BasicDemoL1Contract.sol:BasicDemoL1Contract
```

合约部署地址：0x21F0472653212B3eb6804377d084E55bA113b84b

### 目的链合约

```
forge create --broadcast   --rpc-url $DESTINATION_RPC   --private-key    src/demos/basic/BasicDemoL1Callback.sol:BasicDemoL1Callback   --value 0.00001ether   --constructor-args $DESTINATION_CALLBACK_PROXY_ADDR
```

合约部署地址：0x6764CE8ddFE82C92e248960e16238480009B1098

DESTINATION_CALLBACK_PROXY_ADDR 是目的链上的回调代理合约地址，它是 Reactive Network 官方部署的服务合约，负责安全地把跨链回调请求转发到自己的 BasicDemoL1Callback 合约。

--value 0.00001ether是存入余额供回调交易费用

### 响应式合约

```
 forge create --broadcast   --rpc-url $REACTIVE_RPC   --private-key  src/demos/basic/BasicDemoReactiveContract.sol:BasicDemoReactiveContract   --value 0.001ether   --constructor-args $SYSTEM_CONTRACT_ADDR $ORIGIN_CHAIN_ID $DESTINATION_CHAIN_ID 0x21F0472653212B3eb6804377d084E55bA113b84b //源链合约地址0x8cabf31d2b1b11ba52dbb302817a3c9c83e4b2a5194d35121ab1354d69f6a4cb  //源链要监听的事件签名0x6764CE8ddFE82C92e248960e16238480009B1098  //目的链地址
```

--value 0.001ether是余额，供reactive network支付执行费用

0x8cabf31d2b1b11ba52dbb302817a3c9c83e4b2a5194d35121ab1354d69f6a4cb是源链的Receive哈希事件签名，计算方法：keccak256("Received(address,address,uint256)")

### 测试

向源链合约转钱测试

```
 cast send 0x21F0472653212B3eb6804377d084E55bA113b84b --rpc-url $ORIGIN_RPC --private-key  --value 0.001ether
```

## Aave

要在Aave V3 Sepolia测试网上做实验，必须先有Aave支持的测试代币（比如USDC、DAI、WETH等），以下是Aave V3 Sepolia测试网支持的资产

![](image.png)

要“存入”或“借出”某种代币，合约必须知道是哪种资产，所以要用合约地址来区分。在Aave存USDC，Aave合约就和USDC的合约地址交互；存DAI就和DAI的合约地址交互。

此处查看持有token的合约地址，每种Token（比如USDC、DAI、MyFirstNFT）都是一个独立的智能合约，有自己独立的地址，部署在以太坊网络上。这个合约地址是唯一的，就像每个网站都有自己的URL一样。

![](image-1.png)

[aave 看supply、borrow](https://app.aave.com/)

![](image-2.png)

[aave的协议部署合约信息](https://aave.com/docs/resources/addresses)

![](image-3.png)

[aave v3错误码](https://github.com/aave/aave-v3-core/blob/master/contracts/protocol/libraries/helpers/Errors.sol)

![](image-4.png)
````
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->









要“存入”或“借出”某种代币，合约必须知道是哪种资产，所以要用合约地址来区分。在Aave存USDC，Aave合约就和USDC的合约地址交互；存DAI就和DAI的合约地址交互。

此处查看持有token的合约地址，每种Token（比如USDC、DAI、MyFirstNFT）都是一个独立的智能合约，有自己独立的地址，部署在以太坊网络上。这个合约地址是唯一的，就像每个网站都有自己的URL一样。

![](image-1.png)
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->










````markdown
## basic

### 源链合约

```
forge create --broadcast --rpc-url $ORIGIN_RPC --private-key src/demos/basic/BasicDemoL1Contract.sol:BasicDemoL1Contract
```

合约部署地址：0x21F0472653212B3eb6804377d084E55bA113b84b

### 目的链合约

```
forge create --broadcast   --rpc-url $DESTINATION_RPC   --private-key    src/demos/basic/BasicDemoL1Callback.sol:BasicDemoL1Callback   --value 0.00001ether   --constructor-args $DESTINATION_CALLBACK_PROXY_ADDR
```

合约部署地址：0x6764CE8ddFE82C92e248960e16238480009B1098

DESTINATION_CALLBACK_PROXY_ADDR 是目的链上的回调代理合约地址，它是 Reactive Network 官方部署的服务合约，负责安全地把跨链回调请求转发到自己的 BasicDemoL1Callback 合约。

--value 0.00001ether是存入余额供回调交易费用

### 响应式合约

```
 forge create --broadcast   --rpc-url $REACTIVE_RPC   --private-key  src/demos/basic/BasicDemoReactiveContract.sol:BasicDemoReactiveContract   --value 0.001ether   --constructor-args $SYSTEM_CONTRACT_ADDR $ORIGIN_CHAIN_ID $DESTINATION_CHAIN_ID 0x21F0472653212B3eb6804377d084E55bA113b84b //源链合约地址0x8cabf31d2b1b11ba52dbb302817a3c9c83e4b2a5194d35121ab1354d69f6a4cb  //源链要监听的事件签名0x6764CE8ddFE82C92e248960e16238480009B1098  //目的链地址
```

--value 0.001ether是余额，供reactive network支付执行费用

0x8cabf31d2b1b11ba52dbb302817a3c9c83e4b2a5194d35121ab1354d69f6a4cb是源链的Receive哈希事件签名，计算方法：keccak256("Received(address,address,uint256)")

### 测试

向源链合约转钱测试

```
 cast send 0x21F0472653212B3eb6804377d084E55bA113b84b --rpc-url $ORIGIN_RPC --private-key  --value 0.001ether
```

## Aave

要在Aave V3 Sepolia测试网上做实验，必须先有Aave支持的测试代币（比如USDC、DAI、WETH等），以下是Aave V3 Sepolia测试网支持的资产

![](image.png)

要“存入”或“借出”某种代币，合约必须知道是哪种资产，所以要用合约地址来区分。在Aave存USDC，Aave合约就和USDC的合约地址交互；存DAI就和DAI的合约地址交互。
````
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->












````markdown

## Reactive 基础概念

- Reactive Network：本质是一条独立的区块链网络，专门用于：监听其他链事件、执行自动逻辑、触发跨链交易。有两种执行环境：

  Reactive Network ：EOA 与合约互动的公链，管理订阅。

  Reactive Network ：私有执行环境，用于事件处理的执行环境。该环境中Reactive Contracts无法直接访问外部系统。它们从反应式网络接收事件日志，并能向目的链发送回调事务，但无法与外部 RPC 端点或链外服务交互。

- Reactive Contract：部署在 Reactive Network 上的智能合约，Reactive Contracts可以在部署期间或部署后通过 Sourcify 端点进行验证。Sourcify 是一个去中心化的验证服务，能够将部署的字节码与源代码匹配，使合同可审计且透明。

- 起源 (Origin)：事件发生的源头链，Reactive Network“监听”源链特定的日志（Logs）。

- 目的地 (Destination)：最终执行动作的目标链。

- 回调 (Callback)：Reactive Network在监听到源链的事件后，向 Destination 发出的“执行指令”。

- 回调地址 (Callback Proxy Address)：部署在目的地链上的一个特殊合约（Proxy），用于接收并校验来自 Reactive Network 的指令。

- Hyperlane：跨链消息传输协议，Reactive Network 的智能合约无法自己直接操作另一条链，这就需要Hyperlane完成跨链
  。

  **Reactive Contracts收到源链消息后，发送回调指令，调用 Reactive Network 上的 Hyperlane Mailbox 合约的 dispatch() 函数，Mailbox 接收指令后给它贴上“目的地地址”、“目标链 ID”和“消息负载”的标签，并将其存入出站队列，发送给目的地链的Hyperlane Mailbox。目的地链的 Hyperlane Mailbox 会触发 handle() 函数，将指令最终送达回调代理地址。**

## Reactive Network的经济机制

**Reactive Network 通过 REACT 代币 + 余额 + 债务系统 来支付智能合约执行和跨链回调的费用。**

Reactive Contract 类似一个钱包账户，必须有REACT TOKEN余额，用于支付计算费用、支付 callback 费用、支付跨链交易，如果余额不足合约将无法运行。

### Reactive Network的合约资金分成三种状态

1. Balance（余额）：合约当前可用资金，可以直接用于执行任务。

2. Debt（债务）：系统记录的未结算费用。产生原因：任务已经执行但费用还没完全结算。

3. Reserves（储备）：系统预留的资金，用来保证callback 可以正常执行、资金安全、系统稳定，相当于押金。

### Reactive Contract运行时需要支付两类费用：

1. RVM 执行费用：执行时不需要用户手动提交 gas price，RVM 执行完策略逻辑再统一结算费用。从余额中扣除。

2. 跨链、回调费用：包含跨链执行费用、callback 交易费用。执行前从余额中预留一部分到 Reserves，从Reserves扣除，确保最后一步的回调不会失败！

```
假设做一个自动策略：当 Ethereum 上 ETH 价格低于 2000 USDC 时，自动在 Arbitrum 上买入 ETH。流程如下：

1 Ethereum发生价格更新
      │
      ▼
2 Reactive Network监听到事件
      │
      ▼
3 Reactive Contract执行代码逻辑，判断需要买ETH
      │
      ▼
4 Reactive Contract通过Hyperlane发送跨链消息
      │
      ▼ dispatch()
5 Hyperlane Mailbox (Reactive Network)
      │
      ▼
6 Hyperlane Mailbox (Destination Chain)
      │
      ▼ handle()
7 Callback Proxy   ← 回调代理
      │
      ▼
8 Target Contract  ← 真正的目标合约
```

## Subscription订阅

Subscription 让 Reactive Contract 监听某条链上某种事件，当事件发生时自动触发合约逻辑。

一个Subscription包含chain_id、contract（监听哪个合约）、topic_0（事件类型）、topic_1（事件参数1）...这些参数来自 EVM event log topics。

一个Reactive Contract 可以监听多个事件。

不能创建“全局订阅”，系统不允许：监听所有链、监听所有合约、监听某链所有事件，必须指定链+合约/事件

### 如何创建 Subscription

通过 Reactive Network system contract 调用：service.subscribe(...)

1. 在 constructor 中创建（静态订阅），适合永久监听某事件。

2. Reactive Contract 根据事件动态创建订阅。

### Wildcard 通配符监听

例如：

```
| 参数     | 通配方式         |
| -------- | --------------- |
| contract | address(0)      |
| chain_id | 0               |
| topics   | REACTIVE_IGNORE |

```

监听某合约 所有事件

```
subscribe(
 chainId,
 contractAddress,
 REACTIVE_IGNORE,
 REACTIVE_IGNORE,
 REACTIVE_IGNORE,
 REACTIVE_IGNORE
)
```

## 问题

![](image.png)
````
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->

















## Reactive Basics

1.  源链和目标链、回调代理地址
    

起源 (Origin)：事件发生的源头链。Reactive 网络在这里“监听”特定的日志（Logs）。

目的地 (Destination)：最终执行动作的目标链。

回调 (Callback)：Reactive 网络在监听到 Origin 的事件后，向 Destination 发出的“执行指令”，是连接“监听”与“执行”的桥梁。

回调地址 (Callback Proxy Address)：部署在目的地链上的一个特殊合约（Proxy），用于接收并校验来自 Reactive 网络的指令。是安全守门员，确保只有合法的 Reactive 合约能触发目标动作。

有些网络还不能作为目的地链，因为回到代理合约未部署。在这种情况下，使用 Hyperlane 作为跨链回调的传输工具。

2.  Hyperlane跨链消息传输协议
    
3.  Reactive Contracts
    

事件驱动的智能合约，订阅链上事件并自动触发逻辑.

4.  ReactVM
    

Reactive Network 的执行环境

5.  Economy（经济机制）
    

-   Callback 的费用机制
    
-   Reactive 的支付模型
    

## Reactive Essentials

1.  Reactive Contracts 机制
    
    -   reactive execution
        
    -   自动执行逻辑
        
2.  Events & Callbacks
    
    -   EVM events
        
    -   callback 触发机制
        
3.  ReactVM 与 Reactive Network 环境
    
    -   双状态执行环境
        
    -   状态管理
        
4.  Subscriptions
    
    -   合约订阅链上事件
        
5.  Oracles
    
    -   链下数据接入
        
6.  实践案例
    
    -   部署 Reactive Contract
        
    -   demo：监听日志并触发 callback
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->



















用remix跑了basic的三个合约
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->





















打卡，今天看来co-learning直播，看了reactive官方文档
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
