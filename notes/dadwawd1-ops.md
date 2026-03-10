---
timezone: UTC+8
---

# dadwawd1-ops

**GitHub ID:** dadwawd1-ops

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->
### 1\. 事件源合约 (Origin Contract)

-   **核心目标**：在源链（比如以太坊 Sepolia）上执行某项操作，并向外广播一个信号。
    
-   **学习重点 - 事件 (Events)**：在 Solidity 中，你需要使用 `event` 关键字定义一个事件（例如 `event Received(address indexed sender, uint256 amount)`），并在函数执行完毕时使用 `emit Received(msg.sender, msg.value)` 触发它。
    
-   **Reactive 的作用**：Reactive Network 的节点会在链下持续监听（Subscribe）这个特定的事件日志（Log）。
    

### 2\. 响应式合约 (Reactive Contract)

-   **核心目标**：部署在 Reactive Network 本身上，充当“大脑”和“中继”。它决定了什么时候、以什么条件触发目标链的动作。
    
-   **学习重点 - 合约继承与订阅**：
    
    -   这个合约通常需要继承官方提供的基础合约（如 `AbstractReactive`）。
        
    -   需要在构造函数中设置**订阅（Subscription）**，告诉网络你要监听哪个链（Origin Chain ID）、哪个合约地址，以及哪个特定的事件（通过 Topic 0 哈希来识别）。
        
-   **学习重点 - 条件判断与跨链Payload**：当监听到事件时，合约内的回调函数（如 `react()`）会被触发。你可以在这里编写逻辑（例如：判断金额是否大于 `0.001 ether`）。如果条件满足，它会将需要传递给目标链的数据打包成 Payload，并发出一个特定的 `Callback` 事件，指示 Reactive 节点去目标链执行交易。
    

### 3\. 回调合约 (Destination Contract)

-   **核心目标**：部署在目标链上，接收来自 Reactive Network 的指令并执行最终的业务逻辑。
    
-   **学习重点 - 权限控制 (Access Control)**：这是非常关键的一环！由于这是一个公开在链上的合约，你必须确保**只有** Reactive Network 授权的代理地址（Proxy Address）才能调用你的回调函数。通常你会写一个 `modifier onlyReactiveProxy()` 来拦截恶意的伪造调用。
    
-   **学习重点 - 数据解析**：合约需要能够解包并处理源链传过来的元数据（如原始发送者是谁、发生了什么）。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->

先打卡
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
