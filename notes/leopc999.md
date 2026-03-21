---
timezone: UTC+8
---

# Leot (leopc999)

**GitHub ID:** leopc999

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-21
<!-- DAILY_CHECKIN_2026-03-21_START -->
尝试实现 Cron Demo，还没完全走通。另外准备黑客松的组队。
<!-- DAILY_CHECKIN_2026-03-21_END -->

# 2026-03-20
<!-- DAILY_CHECKIN_2026-03-20_START -->

1.  **关于睿应式智能合约的问题**
    
    -   **可行性与实用性**：用睿应式智能合约构建一个完全链上的安全守护系统比较复杂。第一部分“监测威胁”在安全性角度上较难实现，而第二部分“执行防护动作”则是可行的。
        
    -   **攻击面**：引入睿应式合约可能会增加攻击面，因为攻击者可能利用预先定义好的响应机制进行攻击。
        
2.  **关于睿应层的问题**
    
    -   **实现阶段**：睿应层还没有完全成熟，但也并非停留在理想化的规划阶段。它已经具备一定的去中心化程度，但仍希望进一步增强去中心化。
        
    -   **代码修改**：在正常运行情况下，像止损合约这类睿应式合约通常不需要修改代码。不过，如果底层智能合约更新了，睿应式合约也可能需要同步更新。
        
3.  **成本最小化**
    
    -   **默认情况**：默认情况下，睿应式合约没有运行成本，因此不需要专门去最小化成本。
        
    -   **更新场景**：如果源合约或目标合约发生更新，睿应式合约也可能需要更新，这取决于是否需要与新的合约配合工作。
<!-- DAILY_CHECKIN_2026-03-20_END -->

# 2026-03-19
<!-- DAILY_CHECKIN_2026-03-19_START -->


继续学习 Reactive Network 的 Cron Demo
<!-- DAILY_CHECKIN_2026-03-19_END -->

# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->



今天继续学习其他 Demo。
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-17
<!-- DAILY_CHECKIN_2026-03-17_START -->




## **Reactive Network 原理**

**核心思想**：让合约感知世界并自动执行逻辑，通过event触发、捕捉事件，经reactive control，由RC合约执行逻辑，条件满足后发起链上操作。

**订阅机制**：开发者可指定监听链、合约及事件，订阅只负责监听，逻辑判断在RC合约执行。

**架构模型**：采用双环境模型，一个负责监听和调度，一个负责执行逻辑，彼此隔离，提高安全性。

## **应用案例及局限**

**案例**：以Uniswap自动止损为例，市场流动性变化触发事件，系统重新计算价格，低于阈值自动卖出，引入状态变量控制流程防止重复触发。

**局限**：存在5 - 15秒的必然延迟，不适合高频交易。

## **总结及意义**

**总结**：智能合约定义规则，区块链提供账本，Reactive Network提供执行能力，让系统从记录走向行动。

**意义**：使Web3应用更加自动化和去中心化，未来更多逻辑将不再依赖链下系统。

## **RC合约相关问题**

**更新迭代**：RC合约本质不可升级，功能改变需重新部署，涉及签约定义、订阅及状态管理，因订阅规则、地址和回调逻辑已绑定。

**注销监听**：无法真正注销监听，若要注销需重新写合约，也可部署新版本合约使旧版本监听无效。

## **应用场景探讨**

**自动止盈止损**：用户主动操作，自定义价格，用于锁定利润和限制损失。

**替代预言机**：可直接用链上时间，监听多个合约事件，聚合数据触发执行，做更轻量级链上语义机。

**自动清算**：在质押合同、质押池子中，用户抵押不足时自动清算。

**清算保护**：可在快到清算线时自动补仓，继续下跌则自动止损。
<!-- DAILY_CHECKIN_2026-03-17_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->





# 部署睿应式合约讲解

1.  **部署准备**：
    
    -   **变量设置**：检查环境变量，包括回调合约地址和源地址。此前部署的基础示例合约的源地址被加入到环境变量中。
        
    -   **资金检查**：有足够资金用于部署睿应式合约（reactive contract）。
        
2.  **合约部署步骤**：
    
    -   **在以太坊网络上部署合约**：讲者运行命令将基础示例合约部署到 Ethereum Sepolia，得到源合约地址。
        
    -   **回调合约部署**：使用类似命令部署回调合约，并将其部署地址保存到环境变量。
        
    -   **部署睿应式合约**：使用 Foundry 工具，执行一条包含 RPC 地址、私钥、要部署的合约、初始以太金额和构造函数参数的命令来部署。
        
3.  **部署结果与验证**：
    
    -   **部署成功**：睿应式合约部署成功，但尚未通过验证。提供了查看合约的链接。
        
    -   **交易查看**：讲者向源地址发送了测试交易。在 Etherscan 上，可以查看到相关交易和事件（源合约的 received 事件、睿应式合约的 reaction 交易）。目标链的交易正在等待被打包，可在内部交易（internal transactions）中找到。
        
4.  **功能说明**：
    
    -   **反应机制**：源合约收到以太并触发 `received` 事件。睿应式合约检查交易中的以太数量，并调用回调合约的回调函数，后者再发出 `callback received` 事件。
        
5.  **总结与问答**：
    
    -   **总结**：讲者认为已解答如何部署睿应式合约的问题；在更复杂的配置中，构造函数参数可能会更多。
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->






## **🦄 Uniswap V2 Stop Order Demo：去中心化自动止损**

这个 Demo 更具实战意义。它监听 Uniswap 价格，当价格跌破阈值时，自动卖出代币止损。

**部署流程速览**

1.  **部署代币**: 发行 Token A 和 Token B。
    
2.  **组建 LP**: 创建交易对并添加流动性（例如 10:10）。
    
3.  **Destination**: 部署回调合约（负责执行卖出操作）。
    
4.  **Reactive**: 部署监听合约（设定价格阈值，例如 0.8）。
    
5.  **授权**: approve 回调合约支配代币。
    
6.  **砸盘触发**: 最关键的测试步骤。
    

**具体执行命令**

**Step 1**：部署代币 (Token A & B) 这里遇到了第一个坑：**Foundry 的 Dry Run**。

> **错误**: Warning: Dry run enabled, not broadcasting transaction **解决**: 必须加上 --broadcast 参数。

```
# 部署 Token A
forge create --broadcast --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY src/demos/uniswap-v2-stop-order/UniswapDemoToken.sol:UniswapDemoToken --constructor-args "Token A" "TA"
# Deployed: 0x27a8Be4f66BB3248bDB9087c459C41c0FE2491fe (Token0)

# 部署 Token B
forge create --broadcast --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY src/demos/uniswap-v2-stop-order/UniswapDemoToken.sol:UniswapDemoToken --constructor-args "Token B" "TB"
# Deployed: 0x7BF7C09C9933Fe6C36ad7d1E1fDC8Fd7A276A23b (Token1)
```

**注意**: Uniswap 根据地址数值排序。0x27a8... < 0x7bf7...，所以 TA 是 Token0，TB 是 Token1。

**Step 2**：创建流动性池 (Pair)

```bash
cast send $UNISWAP_V2_FACTORY 'createPair(address,address)' --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY $TOKEN0_ADDR $TOKEN1_ADDR
```

**Pair Address:** **0x6504800f88AEf82f76D73247f76Bd2F9619c679A**

**Step 3**：添加流动性 按 10:10 比例添加，初始价格 = 1。

```bash
# Transfer 10 TA & 10 TB, then mint
cast send $TOKEN0_ADDR 'transfer(address,uint256)' --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY $PAIR_ADDR 10000000000000000000

cast send $TOKEN1_ADDR 'transfer(address,uint256)' --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY $PAIR_ADDR 10000000000000000000

cast send $PAIR_ADDR 'mint(address)' --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY $CLIENT_WALLET
```

**Step 4**：部署回调与监听合约

```bash
forge create --broadcast --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY src/demos/uniswap-v2-stop-order/UniswapDemoStopOrderCallback.sol:UniswapDemoStopOrderCallback --value 0.002ether --constructor-args $DESTINATION_CALLBACK_PROXY_ADDR $UNISWAP_V2_ROUTER
```

**Callback Contract:** **0xdadFC7F820394a90acD49C36008cec7f646a417C**

设定阈值 800 (即 0.8)。

```bash
forge create --broadcast --rpc-url $REACTIVE_RPC --private-key $REACTIVE_PRIVATE_KEY src/demos/uniswap-v2-stop-order/UniswapDemoStopOrderReactive.sol:UniswapDemoStopOrderReactive --value 0.1ether --constructor-args $PAIR_ADDR $CALLBACK_ADDR $CLIENT_WALLET true 1000 800
```

**Reactive Contract:** **0xaf68074454Ba705fa1dc7622f79467A6E09a640f**

**Approve:** 授权 Callback 合约支配 Token A。

```bash
cast send $TOKEN0_ADDR 'approve(address,uint256)' --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY $CALLBACK_ADDR 1000000000000000000
```

**Step 5**：砸盘触发 (The Dump) 这是最有趣也最容易报错的一步。为了触发止损，需要大幅压低 Token A 的价格（转入大量 Token A，换出少量 Token B）。 **转入**: 5 个 Token A。

```bash
cast send $TOKEN0_ADDR 'transfer(address,uint256)' --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY $PAIR_ADDR 5000000000000000000
```

**Swap**: 取出 3 个 Token B。

```bash
cast send $PAIR_ADDR 'swap(uint,uint,address,bytes calldata)' --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY 0 3000000000000000000 $CLIENT_WALLET "0x"
```

**结果分析**:

-   池子变动: 10 TA : 10 TB -> **15 TA : 7 TB**。
    
-   新价格: 7 / 15 ≈ 0.46。
    
-   **0.46 < 0.8**，成功触发止损！
    
-   **Trigger Tx Hash:** _0x19ed3b618ae2d2bdade052488de30b6190f9c80f37a4a56fbebd80cc2e81a660_
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->







今天尝试进行第二关的 Uniswap 止损单的练习，在学习文档内容。
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->








# 用 AI 构建睿应式合约

1.  **AI 在开发中的角色**
    
    -   **增强开发者能力**：AI 并非替代开发者，而是在三方面增强其能力：快速获取知识、提高开发速度、提升产出质量。
        
    -   **开发者的核心职责**：开发者仍需理解问题、熟悉所用技术，并对 AI 的输出进行验证。
        
2.  **使用 AI 的建议**
    
    -   **Claude AI 工具**：建议使用 CLI 工具或 Vscode 扩展，因为它可以直接在代码仓库中生成代码（已在上文以实体形式提及）。
        
    -   **其他 AI 选项**：也可使用 Cursor、GitHub Copilot、ChatGPT 和 DeepSeek 等替代方案。
        
3.  **与 AI 合作的工作流**
    
    -   **规划阶段**：让 AI 制定实现计划，验证该计划，反复提问直到满意为止。
        
    -   **实现阶段**：在计划确定后，让 AI 实施计划，仔细阅读 AI 生成的代码，并向 AI 提问以充分理解。
        
    -   **部署与总结**：将合约部署到测试网，运行预期工作流，然后让 AI 输出总结，但需人工验证该总结。
        
4.  **与 AI 交互的技巧**
    
    -   **提供最新信息**：向 AI 提供最新的链接与代码示例，确保其使用的是最新资料。
        
    -   **验证 AI 输出**：要求 AI 自我验证其所做的工作，同时开发者也要亲自核验。
        
    -   **沟通要清晰且需理解**：确保 AI 明确你的需求，同时你也要理解 AI 所构建的内容。
        
5.  **睿应式合约的应用场景**
    
    -   **常见用例**：止损/止盈单、桥接（bridges）、清算保护、自动收费、自动循环器（auto-looper）、跨链预言机（cross-chain Oracles）等。
        
6.  **问答要点**
    
    -   **Claude AI 的安全性**：存在安全性上的顾虑，但现代操作系统提供权限控制；在浏览器中工作可作为替代方案。
        
    -   **无法使用 Claude 时的替代 AI**：若因地区或其它原因无法使用 Claude，可选择文中提到的其他 AI。
        
    -   **向 AI 提供 token 问题**：睿应层无法直接赠送 AI 代币，但可以尝试联系 AI 公司申请资助或 grant。
        
    -   **向 AI 询问解释**：可以像问人一样向 AI 提问，AI 在回答上通常更有耐心。
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->









### **实时监控问题解答**

**监控原理**：市场波动与Reactive实时监控无关，其监控每条链的每个区块，并对合约订阅的事件做出反应。

**特殊情况说明**：在极端波动如黑客攻击或调试时，一个区块内的反应可能不足，但总体仍有时间监控数据，需确保交易及时并支付足够Gas费用。

### **应用场景讨论**

**跨链操作痛点**：Reactive可解决跨链操作繁琐、用户体验差及安全性隐患问题，目前跨链操作低效，合约间缺乏通讯机制，跨链桥存在中心化问题。

**潜在解决方案**：Reactive可弥补通讯机制空缺，避免因不同链合约缺乏沟通导致的清算不及时问题。

**全链上方案补充**：全链上跨链方案在链下较脆弱且无法自动化，跨链桥和预言机存在中心化问题。

### **经济生态问题探讨**

**币价影响**：有同学提出Reactive部署节点需质押代币，币价下跌会降低作恶成本，可能导致应用层出现问题。

**POS机制疑问**：大家讨论了POS机制能否保障安全，认为以太坊因市值高作恶成本高，但Reactive代币市值低时存在作恶可能。

**与预言机对比**：该同学认为Reactive与Chainlink机制类似，都需质押代币成为节点，且都面临共识安全问题。

**中心化解决方案**：提出可通过中心化控制部分节点解决问题，但会导致中心化程度过高，影响使用意愿。
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->










1\. **Transaction Rollback and Reorg Handling**

**Network Failure** ：When a node encounters a network failure, transactions will not be rolled back. The state of non - failed nodes is considered correct.

**Chain Reorg** ：If a reorg occurs on the origin chain after a callback is sent, it does not affect the execution of the reactive contract. Handling such scenarios should be done at the application (smart contract) level. For example, waiting for several blocks on the origin chain to ensure no reorgs before sending a callback.

2\. **Low - Latency Achievement**

**Latency Factors** ：The main latency from event trigger to reactive response comes from the block time of the React network (average 7 seconds) and the destination chain. For example, for Ethereum, the average block time is 10 seconds, and the reaction time is about half of the block time on average.

**Application Scenarios and Risks**

**Not Recommended for High-Frequency Use Cases** ：Reactive contracts are not recommended for time - sensitive, high - frequency use cases due to the added delay from the separate chain (React network) for decentralization. They are more suitable for use cases where decentralization is more important than high speed.

4\. **Demo Introduction**

**Starting Points** ：You can go to the official website, then either read the docs for more information on working with reactive contracts or go to the GitHub repository (reactive smart contract demos). Start with the basic demo in the src demos folder and follow the steps in the readme to deploy contracts.

5\. **Documentation Information**

**Availability** ：Translation versions of relevant information can be found in the notion document in the learning resource library, but currently only the first chapter is available.

* * *

### 中文解释：

1.  网络回退与链重组处理
    

网络故障：当某个节点遇到网络故障时，交易不会被回滚。未发生故障的节点状态被视为正确。

链重组：如果在发送回调之后，源链发生链重组，这不会影响睿应式合约的执行。此类情形的处理应在应用（智能合约）层面完成。例如，可在源链上等待若干个区块以确保没有重组再发送回调。

2.  低延迟实现
    

延迟因素：从事件触发到睿应响应的主要延迟来自睿应层的出块时间（平均约 7 秒）以及目标链的出块时间。例如，对于以太坊，平均出块时间约为 10 秒，平均反应时间约为区块时间的一半。

3.  应用场景与风险
    

不建议用于高频场景：由于为实现去中心化而引入了独立链（睿应层），会增加延迟，因此睿应式合约不建议用于对时间高度敏感的高频用例。它们更适合那些去中心化比极高速度更重要的场景。

4.  演示说明
    

入门：可访问官方网站，阅读文档以获取关于使用睿应式合约的更多信息，或访问 GitHub 仓库（睿应式合约示例）。从 `src` 下的 `demos` 文件夹中的基础示例开始，按照 README 中的步骤部署合约。

5.  文档信息
    

可用性：相关信息的中文翻译可在学习资源库的 Notion 文档中找到，但目前仅开放第一章。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->











# **Basic Reactive Demo：理解“监听-反应”闭环**

这个 Demo 是 Reactive 的 "Hello World"。流程很简单：在 Sepolia 上转账 -> Reactive 监听到 -> 自动通知 Sepolia 上的回调合约。

**部署流程速览**

1.  **Origin (Sepolia)**: 部署 BasicDemoL1Contract。这是被监听的对象。
    
2.  **Destination (Sepolia)**: 部署 BasicDemoL1Callback。这是接收通知的对象。
    
3.  **Reactive (Reactive Lasna)**: 部署监听合约，连接 Origin 和 Destination。
    
4.  **触发测试**: 向 Origin 转账 > 0.001 ETH。
    

**具体执行命令**

**Step 1**：部署源链合约 (Origin) 负责接收转账并发出事件。

```bash
forge create --broadcast --rpc-url $ORIGIN_RPC --private-key $ORIGIN_PRIVATE_KEY src/demos/basic/BasicDemoL1Contract.sol:BasicDemoL1Contract
```

**Deployed to:** **0x1fe40FbA975c1B0f8069737dB32B4b74C6dD01F8**

**Step 2**：部署目标链合约 (Destination) 负责接收回调。这里预充值 0.001 ETH 是为了模拟支付 Relayer 的 Gas 费。

```bash
forge create --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY src/demos/basic/BasicDemoL1Callback.sol:BasicDemoL1Callback --value 0.001ether --constructor-args $DESTINATION_CALLBACK_PROXY_ADDR
```

**Deployed to:** **0xf2Dd031f9a732a3f6e3097bB626eAA9627cDE4cc**

**Step 3**：部署反应式合约 (Reactive) 连接 Origin 和 Destination。注意中间的长 Hash 是 Received 事件的 Topic 0。

```bash
forge create --rpc-url $REACTIVE_RPC --private-key $PRIVATE_KEY src/demos/basic/BasicDemoReactiveContract.sol:BasicDemoReactiveContract --value 0.001ether --constructor-args $SYSTEM_CONTRACT_ADDR $ORIGIN_CHAIN_ID $DESTINATION_CHAIN_ID $ORIGIN_ADDR 0x8cabf31d2b1b11ba52dbb302817a3c9c83e4b2a5194d35121ab1354d69f6a4cb $CALLBACK_ADDR
```

**Step 4**：触发与验证 向 Origin 合约转账 **0.0015 ETH**（大于代码要求的 0.001 ETH）。

```bash
cast send $ORIGIN_ADDR --rpc-url $ORIGIN_RPC --private-key $ORIGIN_PRIVATE_KEY --value 0.0015ether
```

**Trigger Tx Hash:** _0xe002ce016018c9387aa5444634eaded33801d30f0175b994cd97d0ec40b14567_ **结果**: 几分钟后，在 Sepolia Etherscan 查看 Callback 合约，成功收到 CallbackReceived 事件。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->












# **我所理解的睿应层（Reactive Network）**

对于刚入门的初级技术开发来说，可以将睿应层理解为区块链世界的**“自动化触发器”或“智能中枢”**。

![图像](https://pbs.twimg.com/media/HBDarTpakAENKqM?format=jpg&name=medium)

## **核心概念：从“被动等待”到“主动响应”**

要理解睿应层，首先要明白它解决了什么问题。

-   **传统智能合约（旧模式）：** 就像一台**自动售货机**。它很被动，必须等待用户（你）去投币、按按钮，它才会动一下。如果没有人操作，它就永远静止。
    
-   **睿应合约（Reactive Smart Contracts，新模式）：** 就像一套**智能家居系统**。它不需要你每次都去按开关。它会自己“观察”环境——比如监测到“天黑了”（事件），它就会自动“开灯”（执行操作）。
    

在文档中，这被称为**“控制反转”（Inversion of Control）。**意思就是：**不再由**用户**手动驱动每一步，而是由链上的**数据流（事件）来驱动合约自动运行。

## **睿应层的运行逻辑（三步）**

整个睿应层的工作流程可以简化为“听”、“想”、“做”三个环节：

**第一步：听（订阅与源链）**

-   **源链 (Origins)：** 事情发生的地点（比如以太坊、Polygon 等区块链）。
    
-   **订阅 (Subscriptions)：** 睿应式合约会提前在这些链上通过“订阅”功能布置“耳目”。它会告诉网络：如果是关于“Uniswap 价格变动”的消息，记得第一时间通知我。
    

**第二步：想（ReactVM 与处理）**

-   **ReactVM：** 这是睿应层的大脑和处理中心。
    
-   **低成本处理：** 当源链上发生了订阅的事件（比如有人买入了代币），这个信息会被传送到睿应层。睿应层会用一种非常快且便宜的方式（并行化 EVM）来计算该怎么做。这比直接在昂贵的主链上计算要划算得多。
    

**第三步：做（回调与目标链）**

-   **目标链 (Destinations)：** 需要产生结果的地方。
    
-   **回调与执行：** 计算完成后，睿应式合约会向目标链发送指令（Callback），执行相应的操作。
    

**例子：** 比如在源链监测到币价跌到了某个位置，睿应层计算后，自动向目标链发送指令：“卖出这个币”（比如官方文档提到的 Uniswap V2 止损单的用例）。

## **为什么需要它？（核心价值）**

对于开发者来说，睿应层最大的意义在于**“跨链自动化”**：

1.  **省心（自动化）：** 开发者可以写出能自己根据情况做决定的程序，不需要用户时刻盯着屏幕操作或者额外部署监控机器人程序。
    
2.  **省钱（经济模型）：** 它把复杂的计算搬到了专门优化的睿应层上做，减少了在昂贵主链上的花费。
    
3.  **打通孤岛：** 它可以监听一条链的事，去影响另一条链，把不同的区块链连接了起来。
    

## **总结**

**睿应层（Reactive Network）既能听懂各条链上的“风吹草动”（事件），又能通过低成本计算迅速做出反应，最后指挥其他链进行操作的“自动化指挥官”。**
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
