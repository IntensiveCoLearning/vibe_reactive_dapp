---
timezone: UTC+8
---

# yuyang128

**GitHub ID:** yuyang128

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
学习内容

# **Lesson 2: How Events and Callbacks Work**

## 1\. 核心概念：为什么需要“事件”？

在以太坊中，智能合约像一个“黑盒”，外界无法主动感知其内部变化。

-   传统交互： 外部需要不断“轮询”合约来获取状态，成本高且低效。
    
-   事件 (Events) 的价值：
    
    -   广播机制： 合约通过 `emit` 发出“叮”的一声，外部应用（dApp）即可实时监听到。
        
    -   数据检索： 事件被存储在交易日志（Logs）中，方便按参数搜索，且不占用合约主存储，不直接修改链上状态。
        

* * *

## 2\. 响应式合约 (RC) 的核心大脑：`react()` 与 `IReactive`

RC 通过实现 `IReactive` 接口，将“被动监听”转化为“自主执行”。

### 核心接口组件：

-   `LogRecord` 结构体： 这是 RC 的“感官”，包含了事件触发的所有元数据（链ID、合约地址、Topic 主题、数据载荷等）。
    
-   `react()` 方法： 这是 RC 的核心触发器。
    
    -   当网络检测到符合条件的事件时，会自动调用此函数。
        
    -   合约在 `react()` 内部执行逻辑。
        

* * *

## 3\. 跨链执行流程：回调 (Callbacks)

当 RC 在 A 链监听到事件，需要去 B 链执行操作时，通过 `Callback` 事件实现跨链控制。

### 处理流程：

1.  **触发回调：** RC 发出 `Callback` 事件。
    
2.  **网络捕获：** Reactive Network 检测到该事件。
    
3.  **解析负载 (Payload)：** 网络处理 `payload` 中的编码信息。
    
4.  **执行交易：** 在目标链（`chain_id`）上向目标合约发送交易。
    

### 关键架构逻辑图：

* * *

## 4\. 重点提示：安全与权限 (Authorization)

-   **自动替换机制：** 出于安全，Reactive Network 会强制将 `payload` 调用参数的前 160 位替换为 **RVM ID**（即 ReactVM 地址，也等同于合约部署者地址）。
    
-   **开发者须知：** 编写回调函数时，第一个参数永远是这个 RVM 地址，无论你在代码里如何命名变量。
    

* * *

## 5\. 挑战任务的核心技能点（总结表格）

| 动作 | 关键关键字/方法 | 作用 |
| 定义事件 | event EventName(...) | 声明要记录的数据结构 |
| 发出事件 | emit EventName(...) | 在逻辑执行后广播信息 |
| 逻辑处理 | react(LogRecord log) | 接收事件并进行自主决策 |
| 跨链操作 | emit Callback(...) | 通过 Payload 指令在目标链发起交易 |

## **Code Snippet**

-   **监听模版：** `react()` 函数的标准框架。
    

```
// 监听并处理事件的入口函数
function react(LogRecord calldata log) external override {
    // 1. 验证事件是否是我们感兴趣的 (例如通过 topic_0 过滤)
    if (log.topic_0 == EXPECTED_TOPIC_HASH) {
        
        // 2. 解析事件中的数据 (根据实际 Event 定义解析)
        // 使用 abi.decode 将 log.data 还原为可读变量
        (string memory symbol, uint256 price) = abi.decode(log.data, (string, uint256));
        
        // 3. 执行你的自定义逻辑 (例如状态存储、条件判断)
        if (price > THRESHOLD) {
            _executeCallback(log.chain_id);
        }
    }
}
```

-   **回调模版：** 上面这段 `abi.encodeWithSignature` 的构建逻辑。
    

```
function _executeCallback(uint256 chain_id) internal {
    // 1. 准备目标链上的函数调用参数
    // 注意：第一个 address(0) 会被 Reactive Network 自动替换为你的 RVM ID
    bytes memory payload = abi.encodeWithSignature(
        "targetFunctionName(address,uint256,string)", 
        address(0),       // 这里的占位符是必需的
        1000,             // 示例参数 1
        "ExecutionData"   // 示例参数 2
    );

    // 2. 发出 Callback 事件，触发网络在目标链上执行
    // chain_id: 目标链编号
    // targetContract: 目标链上的合约地址
    // GAS_LIMIT: 执行该回调所需的 Gas 上限
    emit Callback(chain_id, targetContract, CALLBACK_GAS_LIMIT, payload);
}
```
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

# **Reactive Contracts**

## 背景

用户交易运行的传统智能合约局限于外部指令，需要自主性发起操作或者响应区块链事件，这导致需要持有私钥并引入中心化的控制点。而Reactive Contracts（RCs）可以实现主动监控与EVM兼容的链上事件并自动操作。创新性的引入了去中心化机制，无需人工干预，实现复杂逻辑并自动响应链上事件。

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/yuyang128/images/2026-03-09-1773067335122-image.png)

（基础响应工作流）

## 优势

-   去中心化
    
-   自动化
    
-   跨链互操作性
    
-   增强效率和功能
    
-   DeFi及其其他领域的创新
    

## Lesson 1

### 区别于传统智能合约

RC于传统智能合约区别在于响应性。

-   **传统智能合约**：被动收到直接点EOA（事件获取协议）交易时才执行
    
-   **RC**：主动且持续性监视区块链上感兴趣的事件，响应后执行预定义的区块链操作。
    

### Inversion of Control

不同于需要EOA发起外部执行功能的传统智能合约，RC能根据预定义事件的发生自主决定何时执行。

这种IoC范式跳出了传统EOA/BOT主动发起才可执行对于事件的响应这个局限，从而可以根据预定义事件的发生自主性自主决定执行逻辑、构建出了更加动态和响应迅速的系统。

### 这里有疑问：原文提及根据预定义事件的发生自主决定何时执行，具体是什么含义，为什么预定义事件和EOA不一样？预定义也不是人干涉定义，和脚本或者BOT的区别又在哪里呢？

AI给我的回答是

-   **EOA发起**：重点在于**外部推入**，类比于人（EOA）去拿着要是开锁（合约）。若没有外部干涉，锁（合约）永远无法打开。
    
-   **预定义事件（Reactive）：它是内部/环境感知**。他类似安装与合约边的“智能传感器”，它不需要人（EOA）每隔一段时间去点击按钮，它可自行检测链上发生预定义的事件（如A链存入资金，自动触发B链上的代码）。
    

核心区别在于EOA需要主动命令（时刻在线），而事件驱动是自动响应（逻辑固定于系统中）。本质在于"托管地”**和**“原子性/信任成本”

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/yuyang128/images/2026-03-09-1773069608954-image.png)

### 为什么优于BOT？

-   **问题：** Bot 是“链下行为”，依赖个人服务器，存在中心化运维风险，且需要持续的 Gas 费用和监控维护。
    
-   **改进：** 睿应层将这种“跨链触发逻辑”变成了“协议原语”（Protocol Native），让这种响应变成了区块链系统自身的一种特性，不仅更可靠，而且移除了“中间人”角色。
    

### CS具体实现方式:

核心机制：闭环事件处理

1.  定义：指定目标链、合约地址以及关键事件（topic 0）。
    
2.  监听：RC与Reactive Network中持续监控目标链
    
3.  计算与响应：
    
    -   触发：事件发生，自动触发预设逻辑
        
    -   状态处理：RC具有**状态**，处理当下事件时，可以结合历史状态进行计算
        
4.  反馈：基于计算结果，更新状态，执行行动。
    

### **典型使用场景**

| 用例场景 | 核心逻辑 | 解决的痛点 |
| 多预言机聚合 | 聚合多源数据，执行加权平均等计算。 | 降低单点故障，实现更精准的数据输入。 |
| Uniswap 止损单 | 监控池内交易事件，触价自动执行 Swap。 | DEX 原生无止损功能，摆脱中继 Bot。 |
| 跨链套利 | 监控多链价格差，跨链自动化套利。 | 摆脱服务器运维，实现协议级全链套利。 |
| 资金池再平衡 | 自动监控并调配多链流动性。 | 自动化管理多链资产，提升资金效率。 |
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->




# **Reactive Contracts**

## 背景

用户交易运行的传统智能合约局限于外部指令，需要自主性发起操作或者响应区块链事件，这导致需要持有私钥并引入中心化的控制点。而Reactive Contracts（RCs）可以实现主动监控与EVM兼容的链上事件并自动操作。创新性的引入了去中心化机制，无需人工干预，实现复杂逻辑并自动响应链上事件。

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/yuyang128/images/2026-03-09-1773067335122-image.png)

（基础响应工作流）

## 优势

-   去中心化
    
-   自动化
    
-   跨链互操作性
    
-   增强效率和功能
    
-   DeFi及其其他领域的创新
    

## Lesson 1

### 区别于传统智能合约

RC于传统智能合约区别在于响应性。

-   **传统智能合约**：被动收到直接点EOA（事件获取协议）交易时才执行
    
-   **RC**：主动且持续性监视区块链上感兴趣的事件，响应后执行预定义的区块链操作。
    

### Inversion of Control

不同于需要EOA发起外部执行功能的传统智能合约，RC能根据预定义事件的发生自主决定何时执行。

这种IoC范式跳出了传统EOA/BOT主动发起才可执行对于事件的响应这个局限，从而可以根据预定义事件的发生自主性自主决定执行逻辑、构建出了更加动态和响应迅速的系统。

### 这里有疑问：原文提及根据预定义事件的发生自主决定何时执行，具体是什么含义，为什么预定义事件和EOA不一样？预定义也不是人干涉定义，和脚本或者BOT的区别又在哪里呢？

AI给我的回答是

-   **EOA发起**：重点在于**外部推入**，类比于人（EOA）去拿着要是开锁（合约）。若没有外部干涉，锁（合约）永远无法打开。
    
-   **预定义事件（Reactive）：它是内部/环境感知**。他类似安装与合约边的“智能传感器”，它不需要人（EOA）每隔一段时间去点击按钮，它可自行检测链上发生预定义的事件（如A链存入资金，自动触发B链上的代码）。
    

核心区别在于EOA需要主动命令（时刻在线），而事件驱动是自动响应（逻辑固定于系统中）。本质在于\*\*"托管地”**和**“原子性/信任成本”\*\*

![image.png](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/yuyang128/images/2026-03-09-1773069608954-image.png)

### 为什么优于BOT？

-   **问题：** Bot 是“链下行为”，依赖个人服务器，存在中心化运维风险，且需要持续的 Gas 费用和监控维护。
    
-   **改进：** 睿应层将这种“跨链触发逻辑”变成了“协议原语”（Protocol Native），让这种响应变成了区块链系统自身的一种特性，不仅更可靠，而且移除了“中间人”角色。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
