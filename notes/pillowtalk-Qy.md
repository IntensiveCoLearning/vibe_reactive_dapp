---
timezone: UTC+8
---

# Qy

**GitHub ID:** pillowtalk-Qy

**Telegram:** @Qy_originshift

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-18
<!-- DAILY_CHECKIN_2026-03-18_START -->
### **触发合同逻辑**

-   **自动执行**：当满足某个条件或事件时，智能合约会自动触发预定的行动，比如：
    
    -   **释放支付款项**：当完成某个里程碑或任务时，合约自动向自由职业者支付约定的款项。
        
    -   **应用惩罚条款**：如果任务未按时完成，合约会自动扣除相应的金额作为惩罚。
        
    -   **争议解决**：如果对支付条款产生争议，合约可以触发争议解决机制，如使用仲裁服务或按合同条款执行。
        

### 示例：自由职业者智能合约（Solidity代码）

```
pragma solidity ^0.8.0;

contract FreelancerContract {

    address public freelancer;
    address public client;
    uint256 public paymentAmount;
    uint256 public milestoneDeadline;
    uint256 public penaltyRate;
    bool public milestoneCompleted = false;

    event PaymentReleased(address to, uint256 amount);
    event MilestoneCompleted(address freelancer, uint256 milestoneAmount);
    event PenaltyApplied(address freelancer, uint256 penaltyAmount);

    constructor(address _freelancer, address _client, uint256 _paymentAmount, uint256 _milestoneDeadline, uint256 _penaltyRate) {
        freelancer = _freelancer;
        client = _client;
        paymentAmount = _paymentAmount;
        milestoneDeadline = _milestoneDeadline;
        penaltyRate = _penaltyRate;
    }

    // 标记里程碑完成并在按时完成时释放支付
    function completeMilestone(bool onTime) public {
        require(msg.sender == freelancer, "Only freelancer can complete milestone");

        if (onTime) {
            milestoneCompleted = true;
            emit MilestoneCompleted(freelancer, paymentAmount);
        } else {
            uint256 penalty = (paymentAmount * penaltyRate) / 100;
            emit PenaltyApplied(freelancer, penalty);
        }
    }

    // 如果里程碑完成，释放支付款项
    function releasePayment() public {
        require(msg.sender == client, "Only client can release payment");
        require(milestoneCompleted, "Milestone is not completed yet");

        uint256 payment = milestoneCompleted ? paymentAmount : (paymentAmount - (paymentAmount * penaltyRate) / 100);
        payable(freelancer).transfer(payment);
        emit PaymentReleased(freelancer, payment);
    }

    // 监控里程碑状态（可以集成外部API）
    function getMilestoneStatus() public view returns (bool) {
        return milestoneCompleted;
    }
}
```

### 这个智能合约的功能：

-   **支付释放**：当里程碑完成时，自动释放支付款项。
    
-   **惩罚应用**：如果自由职业者未按时完成任务，合约会自动扣除罚金。
    
-   **事件触发**：通过外部系统的触发事件（如任务完成、延迟等）来更新合约的状态并执行相应动作。
    

### 实现步骤：

1.  **在区块链上部署智能合约**：可以选择以太坊或币安智能链等区块链平台进行部署和执行。
    
2.  **前端或仪表盘**：需要一个用户界面（UI），让自由职业者和客户能够方便地查看任务进度、支付状态和违约情况。
    
3.  **与外部API集成**：例如，通过集成项目管理工具API来实时监控任务进度，或者通过支付网关API确认支付是否完成。
<!-- DAILY_CHECKIN_2026-03-18_END -->

# 2026-03-16
<!-- DAILY_CHECKIN_2026-03-16_START -->

**1.AI Contract → Reactive Execution**

```
Upload contract
AI parse clauses
Generate reactive rules
Monitor real world events
Trigger contract logic
```

2**.Freelancer Smart Contract**
<!-- DAILY_CHECKIN_2026-03-16_END -->

# 2026-03-15
<!-- DAILY_CHECKIN_2026-03-15_START -->


休息休息休息
<!-- DAILY_CHECKIN_2026-03-15_END -->

# 2026-03-14
<!-- DAILY_CHECKIN_2026-03-14_START -->



今天

![{\displaystyle \pi }](https://wikimedia.org/api/rest_v1/media/math/render/svg/9be4ba0bb8df3af72e90a0535fabcc17431e540a)

节，放假
<!-- DAILY_CHECKIN_2026-03-14_END -->

# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->




The meeting discussed how to use AI to build reactive contracts, including workflow, tips, and application examples, as follows:

1.  **Workflow for Building with AI**:
    

-   **Ask for a plan**: Explain what to build and specify using reactive contracts.
    
-   **Verify the plan**: Read the plan, ask additional questions, and iterate until satisfied.
    
-   **Implement the plan**: After the plan is approved, ask AI to write the code.
    
-   **Read and understand the code**: Read the code written by AI and ask questions to understand it.
    
-   **Deploy the contracts**: Deploy the contracts on testnets and share the results with AI for explanations.
    
-   **Create a summary**: Ask AI to create a summary and verify it.
    

2.  **Tips for Using AI**:
    

-   **Provide the latest information**: Give AI the latest links and code examples to avoid using old indexed data.
    
-   **Verify AI’s work**: Ask AI to verify what it has done and what you have done.
    
-   **Be clear in communication**: Over - explain and provide details to ensure AI understands your requirements.
    
-   **Understand the output**: Make sure you understand what AI has built and use AI as a tool to learn and grow.
    

3.  **Examples of Reactive Contracts**:
    

-   **Stop - loss and take - profit orders**: Build on decentralized exchanges.
    
-   **Bridge**: Use a ping - pong system to prevent reorgs and double - spending.
    
-   **Liquidation protection**: Protect positions on liquidity pools.
    
-   **Automatic fee collection**: Collect fees from multiple chains and transfer them to a vault.
    
-   **Auto - looper**: Implement on - chain leverage.
    
-   **Cross - chain Oracle**: Migrate data from one chain to another.
    

4.  **AI Security Concerns**:
    

-   **Security risks**: Although modern operating systems have permission controls, there may still be risks, especially for less - experienced developers.
    
-   **Alternative solution**: Working in a browser can be an alternative for those with security concerns.
    

5.  **Learning from AI**:
    

-   Ask AI questions in the same way as you would ask a human. AI is more patient and can answer as many questions as you want.
    

![a9b4e51b5c186618e8283e86451fd1dd.jpg](https://raw.githubusercontent.com/IntensiveCoLearning/vibe_reactive_dapp/main/assets/pillowtalk-Qy/images/2026-03-12-1773331112969-a9b4e51b5c186618e8283e86451fd1dd.jpg)
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->






# 第一关攻略：  

### 1.准备工作  
  
在开始之前，你需要准备以下工具和账户：

-   **Ethereum 钱包**（如 MetaMask 或其他钱包）
    
-   **Solidity 编译器**（如 Remix 或 Hardhat）
    
-   **支持 Reactive Smart Contracts 的链**（可以在示例代码库中找到合适的链或环境）
    

可以参考[这个仓库](https://github.com/Reactive-Network/reactive-smart-contract-demos/tree/main/src/demos/basic)来获取所需的代码和资源。

* * *

### 2\. 部署事件源合约（Origin Contract）

事件源合约是触发跨链事件的地方。当在这个合约上进行某些操作时，会触发事件，Reactive Smart Contract 会在另一个链上进行回调。

步骤：

1.  打开 Remix 或 Hardhat，加载事件源合约代码。
    
2.  连接到你想部署的链（例如，Ethereum 测试网如 Rinkeby）。
    
3.  编译并部署合约。
    

```
// Origin Contract: Event Trigger
pragma solidity ^0.8.0;

contract OriginContract {
    event Triggered(address indexed from, uint256 value);

    function triggerEvent(uint256 value) public {
        emit Triggered(msg.sender, value);
    }
}
```

-   `triggerEvent` 函数会触发 `Triggered` 事件。
    
-   事件包含发送者地址和一个值（可以是你自定义的任何信息）。
    

部署：

1.  在 Remix 中选择合约并点击 **Deploy**。
    
2.  部署成功后，记下合约地址和交易哈希。
    

* * *

### 3\. 部署 Reactive 合约

Reactive 合约会监听另一个链上的事件。当事件触发时，Reactive 合约会执行一些操作，通常是跨链回调。

步骤：

1.  在 Remix 或 Hardhat 中编写 Reactive 合约代码。
    
2.  Reactive 合约需要监听事件并做出响应。
    

```
// Reactive Contract: Listener and Callback
pragma solidity ^0.8.0;

interface IOriginContract {
    event Triggered(address indexed from, uint256 value);
}

contract ReactiveContract {
    address public originContract;

    constructor(address _originContract) {
        originContract = _originContract;
    }

    function listenToEvent() public {
        // 模拟监听事件的逻辑
        // 在实际实现中，需要使用链下监听工具或者 RPC 来获取事件
    }

    function onEventTriggered(address from, uint256 value) public {
        // 回调逻辑
        // 这里可以做跨链操作或者调用其他链上的合约
    }
}
```

-   这里用一个简单的 `onEventTriggered` 函数来模拟事件回调。
    
-   需要为 `originContract` 设置正确的地址（即第一步部署的合约地址）。
    

部署：

1.  在 Remix 中选择 Reactive 合约并点击 **Deploy**。
    
2.  记下部署地址。
    

* * *

### 4\. 部署回调合约（Destination Contract）

回调合约是接收和处理从另一个链上发送的回调事件的合约。可以理解为最终接收触发信息并进行进一步操作的合约。

```
// Destination Contract: Callback Receiver
pragma solidity ^0.8.0;

contract DestinationContract {
    event CallbackReceived(address indexed from, uint256 value);

    function receiveCallback(address from, uint256 value) public {
        emit CallbackReceived(from, value);
    }
}
```

-   该合约的 `receiveCallback` 函数接收来自 `ReactiveContract` 的回调，并触发 `CallbackReceived` 事件。
    

部署：

1.  在 Remix 中选择 Destination 合约并点击 **Deploy**。
    
2.  记下部署地址。
    

* * *

### 5\. 触发跨链事件并验证

1.  **在事件源合约上触发事件**：
    
    -   通过调用 `triggerEvent` 函数，触发一个事件。
        
    -   确保事件被正确触发，并记录事件信息。
        
2.  **在 Reactive 合约上监听事件**：
    
    -   通过调用 `listenToEvent` 函数，Reactive 合约模拟监听事件。
        
    -   在实际应用中，你需要通过链下工具或 RPC 来监听事件（例如 Web3.js 或 Ethers.js）。
        
3.  **回调合约处理事件**：
    
    -   当 Reactive 合约接收到事件后，调用 `onEventTriggered` 函数，并将信息传递到回调合约。
        
    -   回调合约将会触发 `CallbackReceived` 事件。
        

* * *

### 6\. 验证交易和事件

1.  在区块浏览器（如 Etherscan）上查看交易哈希。
    
2.  验证合约事件是否已成功触发，并确保跨链回调正常工作。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->







```

Origin Contract (链A)
 │
 │ emit Event
 ▼
Reactive Contract (Reactive Network)
 │
 │ 监听事件 + 判断条件
 ▼
Destination Contract (链B)
 │
 │ callback()
 ▼
触发 Destination 事件

```

### 跨链流程：Reactive Contract 会 **订阅 Origin 合约事件**，当检测到事件后执行逻辑，然后**回调 Destination 合约**  
  
Origin Contract（事件源）

### 负责 **触发事件**

```
// Origin.sol
pragma solidity ^0.8.20;

contract Origin {

 event Trigger(address sender, uint256 amount);

 function trigger() external payable {
 emit Trigger(msg.sender, msg.value);
 }

}
```

触发：

```
trigger()
```

链上日志：

```
Trigger(sender, amount)
```
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->








1.互换了些reactive的代币，为之后的作业和Dapp作准备

2.听了晚上的小课，更了解在reactive的nft的变化
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
