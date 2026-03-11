---
timezone: UTC+8
---

# Thomas

**GitHub ID:** Thomas-YHS

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
# 文档目的

沉淀 `ReactiveDemo1` 的最小可复现工程流程：从编码到部署、触发、验收。

## 项目范围

-   Origin 合约：接收转账并发出 `Received` 事件。
    
-   Reactive 合约：订阅 Origin 事件，满足阈值后发出跨链 `Callback` 意图。
    
-   Destination 合约：接收 callback proxy 调用并发出 `CallbackReceived` 事件。
    

## 环境与前置

-   工具：Foundry（`forge`、`cast`）。
    
-   配置：复制 `.env.example` 到 `.env` 并填写私钥/RPC/链参数。
    
-   关键变量：
    
    -   Origin：`ORIGIN_RPC` `ORIGIN_PRIVATE_KEY` `ORIGIN_CHAIN_ID`
        
    -   Destination：`DESTINATION_RPC` `DESTINATION_PRIVATE_KEY` `DESTINATION_CHAIN_ID`
        
    -   Reactive：`REACTIVE_RPC` `REACTIVE_PRIVATE_KEY` `SYSTEM_CONTRACT_ADDR`
        
    -   回调参数：`DESTINATION_CALLBACK_PROXY_ADDR` `EXPECTED_REACTIVE_SENDER`
        
    -   地址回填：`ORIGIN_ADDR` `CALLBACK_ADDR`
        
    -   阈值/触发值：`MIN_TRIGGER_WEI` `TRIGGER_VALUE_WEI`
        

## 编码流程（实现顺序）

1.  定义事件源合约 `src/OriginContract.sol`
    
2.  定义回调接收合约 `src/DestinationContract.sol`
    
3.  定义反应式合约 `src/ReactiveContract.sol`
    
4.  编写单测 `test/ReactiveContract.t.sol`
    

## 关键代码片段

### 1) Origin 事件触发

```solidity
event Received(address indexed origin, address indexed sender, uint256 indexed value);

receive() external payable {
    emit Received(tx.origin, msg.sender, msg.value);

    (bool ok,) = payable(tx.origin).call{value: msg.value}("");
    require(ok, "Refund failed");
}
```

### 2) Reactive 订阅 + 阈值过滤 + 回调意图

```solidity
constructor(
    address _service,
    uint256 _originChainId,
    uint256 _destinationChainId,
    address _originContract,
    uint256 _originTopic0,
    address _destinationCallback,
    uint256 _minTriggerWei
) payable {
    service = ISystemContract(payable(_service));

    originChainId = _originChainId;
    destinationChainId = _destinationChainId;
    destinationCallback = _destinationCallback;
    minTriggerWei = _minTriggerWei;

    if (!vm) {
        service.subscribe(
            originChainId, _originContract, _originTopic0, REACTIVE_IGNORE, REACTIVE_IGNORE, REACTIVE_IGNORE
        );
    }
}

function react(LogRecord calldata log) external vmOnly {
    if (log.topic_3 < minTriggerWei) {
        return;
    }

    address originEoa = address(uint160(log.topic_1));
    address originSender = address(uint160(log.topic_2));

    bytes memory payload = abi.encodeWithSignature(
        "callback(address,address,address,uint256,uint256)",
        address(this),
        originEoa,
        originSender,
        log.topic_3,
        log.tx_hash
    );

    emit Callback(destinationChainId, destinationCallback, GAS_LIMIT, payload);
}
```

### 3) Destination 回调接收

```solidity
constructor(address _callbackProxy, address _expectedReactiveSender) payable AbstractCallback(_callbackProxy) {
    // If _expectedReactiveSender is zero, rvm check is disabled (wildcard).
    rvm_id = _expectedReactiveSender;
}

function callback(
    address reactiveSender,
    address originEoa,
    address originSender,
    uint256 amount,
    uint256 sourceTxHash
) external authorizedSenderOnly rvmIdOnly(reactiveSender) {
    emit CallbackReceived(tx.origin, msg.sender, reactiveSender, originEoa, originSender, amount, sourceTxHash);
}
```

### 4) 单测核心断言

```solidity
vm.expectEmit(true, true, true, true);
emit Callback(84532, destination, 1_000_000, expectedPayload);

reactive.react(log);
```

## 本地构建与测试

```bash
forge build
forge test
```

验收标准：`forge test` 全部通过（当前记录为 `2 passed, 0 failed`）。

## 部署与运行流程（生产步骤）

按顺序执行，且每一步完成后回填 `.env`。

### Step 1 部署 Origin

```bash
./script/deploy-origin.sh
```

等价核心命令：

```bash
forge create --broadcast --rpc-url "$ORIGIN_RPC" --private-key "$ORIGIN_PRIVATE_KEY" src/OriginContract.sol:OriginContract
```

### Step 2 部署 Destination

```bash
./script/deploy-destination.sh
```

等价核心命令：

```bash
forge create --broadcast --rpc-url "$DESTINATION_RPC" --private-key "$DESTINATION_PRIVATE_KEY" src/DestinationContract.sol:DestinationContract --value "${DESTINATION_DEPLOY_VALUE_WEI}" --constructor-args "$DESTINATION_CALLBACK_PROXY_ADDR" "$EXPECTED_REACTIVE_SENDER"
```

### Step 3 部署 Reactive

```bash
./script/deploy-reactive.sh
```

等价核心命令：

```bash
TOPIC0=$(cast keccak "Received(address,address,uint256)")
forge create --broadcast --rpc-url "$REACTIVE_RPC" --private-key "$REACTIVE_PRIVATE_KEY" src/ReactiveContract.sol:ReactiveContract --value "${REACTIVE_DEPLOY_VALUE_WEI}" --constructor-args "$SYSTEM_CONTRACT_ADDR" "$ORIGIN_CHAIN_ID" "$DESTINATION_CHAIN_ID" "$ORIGIN_ADDR" "$TOPIC0" "$CALLBACK_ADDR" "$MIN_TRIGGER_WEI"
```

### Step 4 触发 Origin 事件

```bash
./script/trigger-origin.sh
```

等价核心命令：

```bash
cast send "$ORIGIN_ADDR" --rpc-url "$ORIGIN_RPC" --private-key "$ORIGIN_PRIVATE_KEY" --value "$TRIGGER_VALUE_WEI"
```

## 已执行记录（2026-03-10 Asia/Shanghai）

-   Origin Contract: `0x9A76EB25854B42B60B984C933b08eD12eeAeFCd8`
    
-   Destination Contract: `0x2F4dDA454F470537445e70F479EF9D7F24Ce40Ae`
    
-   Reactive Contract: `0x4DA4f5ebe4de9Da95Ab7DEdFa92a76755531918b`
    
-   Deploy Origin Tx: `0xe218241014d541437b7953d0e6c6d83a0b4de15b269020decdbdbd93277d4a87`
    
-   Deploy Destination Tx: `0xf0f7767ecd347404985e4f006461b1f0e709ec848ad9412413c4d994970b91f0`
    
-   Deploy Reactive Tx: `0xeaa98e9f8539e79b849fd273eb3de1e5ece215d13eb0b60530944ac1c8a2cfb1`
    
-   Trigger Origin Tx: `0xcccddb46d1cda1d193b8c353d687cc2a9949b6dd4028a16489c136371140783f`
    
-   Callback Tx: `0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35`
    

## 验收与证明

-   Origin 事件：
    
    -   `Received` in Sepolia tx event log
        
-   Destination 事件：
    
    -   `CallbackReceived` in Base Sepolia tx event log
        
-   归档材料：
    
    -   `DEPLOYMENT_RECORD.md`
        
    -   `SUBMISSION_TEMPLATE.md`
        

## 常见失败点

-   `.env` 未回填最新地址（`ORIGIN_ADDR` / `CALLBACK_ADDR`）。
    
-   `MIN_TRIGGER_WEI` 大于 `TRIGGER_VALUE_WEI` 导致不触发回调。
    
-   `EXPECTED_REACTIVE_SENDER` 设置错误导致 `rvmIdOnly` 校验失败。
    
-   部署资金不足（`DESTINATION_DEPLOY_VALUE_WEI` / `REACTIVE_DEPLOY_VALUE_WEI`）。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

今天完成了Demo的部署并运行，具体的实现如下文所示：  
\# 跨链事件与回调作业提交

\## 1) 部署信息

\- Origin Contract 地址: `0x9A76EB25854B42B60B984C933b08eD12eeAeFdC8`

\- Destination Contract 地址: `0x2F4dDA454F470537445e70F479EF9D7F24Ce40Ae`

\- Reactive Contract 地址: `0x4DA4f5ebe4de9Da95Ab7DEdFa92a76755531918b`

\## 2) 交易哈希

\- Deploy Origin Tx: `0xe218241014d541437b7953d0e6c6d83a0b4de15b269020decdbdbd93277d4a87`

\- Deploy Destination Tx: `0xf0f7767ecd347404985e4f006461b1f0e709ec848ad9412413c4d994970b91f0`

\- Deploy Reactive Tx: `0xeaa98e9f8539e79b849fd273eb3de1e5ece215d13eb0b60530944ac1c8a2cfb1`

\- Trigger Origin Tx: `0xcccddb46d1cda1d193b8c353d687cc2a9949b6dd4028a16489c136371140783f`

\- Callback Tx（目标链）: `0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35`

\## 3) 触发成功证明

!\[1773144120335\](image/SUBMISSION\_TEMPLATE/1773144120335.png)

\- Origin `Received` 事件浏览器链接: [https://sepolia.etherscan.io/tx/0xcccddb46d1cda1d193b8c353d687cc2a9949b6dd4028a16489c136371140783f#eventlog](https://sepolia.etherscan.io/tx/0xcccddb46d1cda1d193b8c353d687cc2a9949b6dd4028a16489c136371140783f#eventlog)

!\[1773144297441\](image/SUBMISSION\_TEMPLATE/1773144297441.png)

\- Destination `CallbackReceived` 事件浏览器链接: [https://sepolia.basescan.org/tx/0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35#eventlog](https://sepolia.basescan.org/tx/0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35#eventlog)

\- Reactscan Lasna 成功截图 [https://lasna.reactscan.net/address/0x8dc56a4e682b2e12f5e4056d8e82b30c2dee94bc/2](https://lasna.reactscan.net/address/0x8dc56a4e682b2e12f5e4056d8e82b30c2dee94bc/2)

!\[1773144433736\](image/SUBMISSION\_TEMPLATE/1773144433736.png)

\## 4) 简短说明

\- 本次测试中，向 Origin 合约发送 `1000000000000000` wei 后，Reactive 合约监听到 `Received` 事件并触发跨链回调，在 Base Sepolia 上成功执行 callback 交易 `0x58077A0F1FEF7B98CAA6214A003D0ACB049298E9632D2FD2B70DAA4E6037FB35`，完成 `CallbackReceived` 事件证明。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->


终于又开始了新的残酷共学，这次一定要坚持下来，这次想看看能不能做一个简单的链上套利机器人。  
今天看了看文档，把demo的代码拉下来，基础的配置调试了一下，明天部署到测试链上，下面是今天的笔记：

##   
Reactive Network

Reactive 的核心可以引用文档中的一句话来描述：

> Reactive Contracts monitor event logs across EVM chains and execute Solidity logic automatically when conditions are met. Instead of waiting for users or bots to trigger transactions, RCs run continuously and decide when to send cross-chain callback transactions — essentially providing **if-this-then-that automation for smart contracts**.

Reactive实现的最重要的一点是可以在链上持续监控并且发送callback到指定链，传统区块链必须有人发起交易，而Reactive Contracts **持续运行并监听事件**，当条件满足时会自动执行操作。

* * *

Reactive Network 的执行模型本质上是：

**监听 Origin 上的事件 → 在 Destination 上发送 callback → 通过 Callback Proxy 保证安全与可验证性**

Event on Origin → Reactive listens → Callback sent to Destination → Destination verifies through Callback Proxy

### Reactive 可以实现的场景

1.  自动止盈/止损
    
2.  清算保护
    
3.  自动切换投资组合
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
