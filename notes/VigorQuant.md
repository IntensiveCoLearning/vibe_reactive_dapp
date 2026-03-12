---
timezone: UTC+8
---

# Wei

**GitHub ID:** VigorQuant

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-13
<!-- DAILY_CHECKIN_2026-03-13_START -->
第一周：3.9-3-11

-   理解睿应层（Reactive Network）与传统 EVM 的根本区别
    
    -   睿应层 是什么？
        
    
    > 睿应层（Reactive Network）是一个兼容EVM（以太坊虚拟机）的区块链执行层，专为构建响应式智能合约（Reactive Contracts）而设计；
    > 
    > 目的：旨在推动Web3从“用户手动操作”向“事件自动驱动”的转变，让去中心化应用（dApp）能够跨链感知外部事件、自动执行逻辑，并在全链上结算；这种基础设施的核心在于将智能合约从传统的被动模式（即等待用户或外部调用）转变为主动响应模式，例如当特定事件发生时，合约能自动触发动作，而无需人工干预。
    
    -   区别
        
    -   Data lives on one chain, execution happened on another;
        
    -   smart contract cannot watch and react across both, Reactive contracts can;
        
    
    > 传统EVM的智能合约是“被动的工具”——它只有在有人/有交易“叫醒”它时才工作；
    > 
    > 睿应层的Reactive Contracts是“活的主体”——它能自己持续监听世界（链上事件），一旦感兴趣的事情发生，就自动醒来做事，无需任何人叫它。
    
-   核心架构：ReactVM + 睿应层（Reactive Network）环境
    
    -   Reactive Network：监听事件 → 判断逻辑 → 自动触发交易
        
    -   ReactVM ：专门运行 reactive smart contracts 的虚拟机
        
-   理解“事件驱动智能合约”，什么是 reactive smart contracts（睿应式智能合约）
    
    -   传统智能合约，被动执行，用户调用；
        
    -   RC：自动对链上事件作出反应的合约
        
-   知道睿应层（Reactive Network）适合解决什么问题
    
    -   链上事件，自动执行；
        
    -   例如：自动化defi，预测市场，Cross-chain automation，等
<!-- DAILY_CHECKIN_2026-03-13_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->

# **3.9-11 - 第一阶段：理解睿应层（Reactive Network）思维**

Demo 展示了两个关键特性：

-   对original 链上的合约进行，低延时监控；
    
-   从 Reactive Network 向目标链，进行 合约调用（execute calls）
    

三个合约：

-   Original 合约：接受 Eth，并且还给发送者，触发包含交易细节 的 Received 事件；
    
-   Reactive 合约：订阅源链事件，检查事件中的值（topic\_3）是否≥0.01 ETH，如果是则发出Callback事件触发跨链回调。
    
-   Destination 合约：仅允许授权发送者（Reactive Network）调用，记录回调元数据，并发出CallbackReceived事件。
    

未来增强功能：

-   扩展事件订阅，动态订阅，state persistence，Versatile Callbacks
    

## **挑战：构建一个跨链事件与回调合约**

部署&测试

NaN. 配置环境变量

```
  > ```
  > export ORIGIN_RPC=https://sepolia.infura.io/v3/YOUR_INFURA_KEY  
  > export ORIGIN_CHAIN_ID=11155111
  > export ORIGIN_PRIVATE_KEY=0xYOUR_PRIVATE_KEY
  > # 类似设置其他变量：DESTINATION_RPC, DESTINATION_CHAIN_ID, DESTINATION_PRIVATE_KEY, REACTIVE_RPC, REACTIVE_PRIVATE_KEY, SYSTEM_CONTRACT_ADDR, DESTINATION_CALLBACK_PROXY_ADDR
  > ```
```

NaN. Faucet，SepoliaETH 换 REACT代币

NaN. 部署 源合约，BasicDemoL1Contract

```
  > ```
  > forge create --broadcast \
  > --rpc-url $ORIGIN_RPC \
  > --private-key $ORIGIN_PRIVATE_KEY \
  > src/demos/basic/BasicDemoL1Contract.sol:BasicDemoL1Contract
  > ```
  
  部署地址： `$ORIGIN_ADDR`：
  
  > ```
  > export ORIGIN_ADDR=0xaF869c8490F7b29CB42c183Dee56d2c82cD5D0af
  > ```
```

NaN. 部署 目标合约，BasicDemoL1Contract

```
  > ```
  > forge create --broadcast \
  > --rpc-url $DESTINATION_RPC \
  > --private-key $DESTINATION_PRIVATE_KEY \
  > src/demos/basic/BasicDemoL1Callback.sol:BasicDemoL1Callback \
  > --value 0.02ether \
  > --constructor-args $DESTINATION_CALLBACK_PROXY_ADDR
  > ```
  
  部署地址：`CALLBACK_ADDR`
  
  > ```
  > export CALLBACK_ADDR=0xcE2067f3EF983d44b08E5027EaA74EcCB981b95D
  > ```
```

NaN. 部署 响应式合约，BasicDemoReactiveContract

```
  > ```
  > forge create --broadcast \
  > --rpc-url $REACTIVE_RPC \
  > --private-key $REACTIVE_PRIVATE_KEY \
  > src/demos/basic/BasicDemoReactiveContract.sol:BasicDemoReactiveContract \
  > --value 0.1ether \
  > --constructor-args $SYSTEM_CONTRACT_ADDR $ORIGIN_CHAIN_ID $DESTINATION_CHAIN_ID $ORIGIN_ADDR \
  > 0x8cabf31d2b1b11ba52dbb302817a3c9c83e4b2a5194d35121ab1354d69f6a4cb $CALLBACK_ADDR
  > ```
```

测试

> ```
> cast send $ORIGIN_ADDR --rpc-url $ORIGIN_RPC --private-key $ORIGIN_PRIVATE_KEY --value 0.001ether
> # check log result,transactionHash
> cast logs --rpc-url $DESTINATION_RPC --address $CALLBACK_ADDR
> ```

> ```
> cast logs --rpc-url $DESTINATION_RPC --address $CALLBACK_ADDR
> - address: 0xcE2067f3EF983d44b08E5027EaA74EcCB981b95D
>   blockHash: 0x9340c5abeaa639060d4481c5763ff2d67b9306eb1571d51421f16ef8ff29aaef
>   blockNumber: 10428715
>   data: 0x
>   logIndex: 137
>   removed: false
>   topics: [
>         0xcd2e8a773d922062a59e0f5941947a80a55deabb40bc5e3eb8583a02c05145b3
>         0x000000000000000000000000d499725b8d4aad361060a5fc5d30022285159449
>         0x000000000000000000000000c9f36411c9897e7f959d99ffca2a0ba7ee0d7bda
>         0x00000000000000000000000099eb0f79e8bd2b9f24d9f3d01fcf298696717c02
>   ]
>   transactionHash: 0x8a5498e98cfb44d30695596eb6b04c71dae9dd87bc56f768151d96bda04d86e4
>   transactionIndex: 64
> ```

## 交易哈希 / 部署地址

-   `ORIGIN_ADDR` 部署地址: `ORIGIN_ADDR=0xaF869c8490F7b29CB42c183Dee56d2c82cD5D0af`
    

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/VigorQuant/images/2026-03-11-1773261219104-image.png)

-   `CALLBACK_ADDR`部署地址 `CALLBACK_ADDR=0xcE2067f3EF983d44b08E5027EaA74EcCB981b95D`
    

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/VigorQuant/images/2026-03-11-1773261535069-image.png)

-   交易哈希: \``ransactionHash: 0x8a5498e98cfb44d30695596eb6b04c71dae9dd87bc56f768151d96bda04d86e4`
    

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/VigorQuant/images/2026-03-11-1773261586875-image.png)
<!-- DAILY_CHECKIN_2026-03-12_END -->
<!-- Content_END -->
