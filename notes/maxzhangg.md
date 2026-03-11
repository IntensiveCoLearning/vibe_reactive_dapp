---
timezone: UTC-4
---

# Max

**GitHub ID:** maxzhangg

**Telegram:**

## Self-introduction

Web3 实习计划 2025 冬季实习生

## Notes

<!-- Content_START -->
# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->
# 学习笔记：构建一个跨链事件与回调合约

## 一、作业目标理解

这次作业的核心，是用 **Reactive Network** 做一个最基础的跨链事件响应流程：  
在**源链**上部署一个会发出事件的合约（Origin Contract），然后在 **Reactive Network** 上部署一个监听事件的 **Reactive Contract**，当它检测到符合条件的事件后，再去**目标链**调用回调合约（Destination Contract），从而完成一次“链 A 触发事件 → Reactive Network 监听 → 链 B 执行回调”的流程。这个 demo 官方仓库放在 `reactive-smart-contract-demos/src/demos/basic` 中。

* * *

## 二、三个合约的作用

### 1\. Origin Contract

`BasicDemoL1Contract` 是事件源合约。  
它在收到 ETH 时会触发 `Received` 事件，事件里记录了：

-   `origin`：交易发起者 `tx.origin`
    
-   `sender`：当前消息发送者 `msg.sender`
    
-   `value`：本次转入的金额 `msg.value`
    

之后它还会把收到的 ETH 再转回给 `tx.origin`。也就是说，这个合约的主要用途不是存钱，而是**发出一个可被监听的链上事件**。

### 2\. Reactive Contract

`BasicDemoReactiveContract` 是整个系统的核心。  
它在部署时会向 Reactive Network 的系统服务注册订阅，监听指定源链、指定合约、指定 `topic_0` 的日志。源码里可以看到它调用了 `service.subscribe(...)`，订阅的是 Origin Contract 的 `Received` 事件。

当 Reactive Contract 收到日志后，会执行 `react(LogRecord calldata log)`。  
如果事件里的 `topic_3`（这里对应金额）满足阈值条件，它就会构造一个回调 payload：

```
abi.encodeWithSignature("callback(address)", address(0))
```

然后通过发出 `Callback` 事件，请求在目标链执行对应回调。

### 3\. Destination Contract

`BasicDemoL1Callback` 是目标链上的回调合约。  
它继承 `AbstractCallback`，并通过 `authorizedSenderOnly` 和 `rvmIdOnly(sender)` 做权限校验，确保不是任意地址都能调用这个 callback。真正被触发时，它会发出 `CallbackReceived` 事件，记录：

-   `origin`
    
-   `sender`
    
-   `reactive_sender`
    

这样我们就能在目标链浏览器中看到回调确实被执行过。

* * *

## 三、整体执行流程

整个跨链流程可以理解成下面 4 步：

1.  在源链调用 Origin Contract，向它发送 ETH。
    
2.  Origin Contract 发出 `Received` 事件。
    
3.  Reactive Contract 监听到这个事件后，在 Reactive Network 上执行 `react()`，判断金额是否达到阈值。达到条件后发出 `Callback`。
    
4.  目标链上的 Destination Contract 收到跨链回调，执行 `callback(address)`，并发出 `CallbackReceived` 事件作为成功证明。
    

所以，这个 demo 的重点并不是“跨链转资产”，而是**跨链传递事件触发结果**。

* * *

## 四、部署前需要准备的环境变量

根据官方 README，部署前要准备三部分网络信息：源链、目标链、Reactive Network。主要包括：

-   `ORIGIN_RPC`
    
-   `ORIGIN_CHAIN_ID`
    
-   `ORIGIN_PRIVATE_KEY`
    
-   `DESTINATION_RPC`
    
-   `DESTINATION_CHAIN_ID`
    
-   `DESTINATION_PRIVATE_KEY`
    
-   `REACTIVE_RPC`
    
-   `REACTIVE_PRIVATE_KEY`
    
-   `SYSTEM_CONTRACT_ADDR`
    
-   `DESTINATION_CALLBACK_PROXY_ADDR`
    

这说明整个 demo 不是只在单链里跑，而是同时依赖：

-   一个发事件的链
    
-   一个接收回调的链
    
-   一个负责响应式执行的 Reactive Network
    

* * *

## 五、部署步骤总结

### 1\. 部署 Origin Contract

官方示例先部署 `BasicDemoL1Contract`，部署完成后拿到地址，保存为 `ORIGIN_ADDR`。

我部署后的信息：

-   **合约地址**：`<填写你的 ORIGIN_ADDR>`
    
-   **交易哈希**：`<填写你的 tx hash>`
    

* * *

### 2\. 部署 Destination Contract

然后部署 `BasicDemoL1Callback`，部署时需要传入 `DESTINATION_CALLBACK_PROXY_ADDR`，并附带 `0.02 ether`。部署完成后保存地址为 `CALLBACK_ADDR`。

我部署后的信息：

-   **合约地址**：`<填写你的 CALLBACK_ADDR>`
    
-   **交易哈希**：`<填写你的 tx hash>`
    

* * *

### 3\. 部署 Reactive Contract

最后部署 `BasicDemoReactiveContract`。  
这个合约在构造函数里要传入：

-   Reactive Network 的 system contract
    
-   源链 chain id
    
-   目标链 chain id
    
-   源链上的 Origin Contract 地址
    
-   被监听事件的 `topic_0`
    
-   回调合约地址 `CALLBACK_ADDR`
    

README 里给出的 `Received` 事件 `topic_0` 是：

`0x8cabf31d2b1b11ba52dbb302817a3c9c83e4b2a5194d35121ab1354d69f6a4cb`。

我部署后的信息：

-   **合约地址**：`<填写你的 Reactive Contract 地址>`
    
-   **交易哈希**：`<填写你的 tx hash>`
    

* * *

## 六、测试过程

部署完成后，需要向 `ORIGIN_ADDR` 发送 ETH 来触发事件。README 示例里使用：

```
cast send $ORIGIN_ADDR --rpc-url $ORIGIN_RPC --private-key $ORIGIN_PRIVATE_KEY --value 0.001ether
```

这样会触发 Origin Contract 的 `Received` 事件，随后 Reactive Contract 监听并尝试发起目标链回调。

### 一个我注意到的细节

README 的说明文字里有一处写的是 Reactive Contract 检查 `topic_3` 是否至少为 **0.01 Ether**，但源码里的真实条件是：

```
if (log.topic_3 >= 0.001 ether)
```

也就是说，**真正以源码为准的话，阈值是 0.001 ether，不是 0.01 ether**。README 后面的测试命令也同样使用了 `0.001ether`。

这个点很重要，因为如果只看文字说明，可能会误以为要转 0.01 ether 才能触发。

* * *

## 七、成功触发的证明

我这次实验里，成功证明主要看两部分：

### 1\. 源链证明

在源链浏览器中，可以看到对 `ORIGIN_ADDR` 的调用交易，并且日志中出现 `Received` 事件。  
证明内容包括：

-   调用交易哈希：`<填写>`
    
-   `Received` 事件截图：`<填写/附图>`
    

### 2\. 目标链证明

在目标链浏览器中，可以看到回调合约 `CALLBACK_ADDR` 触发了 `CallbackReceived` 事件。  
这说明 Reactive Network 已经完成了“监听 → 判断 → 回调”的整套流程。

证明内容包括：

-   回调交易哈希：`<填写>`
    
-   `CallbackReceived` 事件截图：`<填写/附图>`
    

* * *

## 八、我的收获与理解

通过这个 demo，我理解了 Reactive Network 的几个关键点：

第一，**事件驱动**是整个系统的入口。  
不是目标链主动轮询，而是源链先发事件，Reactive Contract 再响应。

第二，**Reactive Contract 更像自动化中间层**。  
它本身不直接处理复杂业务，而是负责“订阅日志、过滤条件、构造回调 payload、发起跨链执行请求”。

第三，**Destination Contract 负责业务落地与权限控制**。  
它通过继承 `AbstractCallback` 来保证只有授权来源能触发 callback，这比开放一个任意外部可调用函数更安全。

第四，这个模式很适合扩展到更多自动化场景，比如：

-   跨链通知
    
-   条件满足后的自动执行
    
-   链上风控联动
    
-   简单的跨链策略执行
<!-- DAILY_CHECKIN_2026-03-10_END -->
<!-- Content_END -->
