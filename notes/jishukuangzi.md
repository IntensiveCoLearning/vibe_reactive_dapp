---
timezone: UTC+8
---

# jishukuangzi

**GitHub ID:** jishukuangzi

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->
## 1️⃣ 理解 Reactive 与传统 EVM 的根本区别

-   **传统 EVM 智能合约**：
    
    -   合约是“被动”的：它只在交易调用时执行（transaction-driven）。
        
    -   外部事件或条件不会主动触发合约逻辑，你必须显式发送交易来改变状态。
        
    -   比如 ERC20 的 `transfer`，必须有人发交易才会执行。
        
-   **Reactive 智能合约**：
    
    -   合约是“主动”的：它可以订阅事件，一旦事件发生，自动触发对应逻辑（event-driven）。
        
    -   执行逻辑与事件解耦，形成 **Event → Reactive Logic → Callback Transaction** 链路。
        
    -   类似前端的 React：状态变了（事件来了），系统自动响应（执行逻辑）
        

## 2️⃣ 核心架构：ReactVM + Reactive Network

-   **ReactVM**：
    
    -   类似 EVM，但加入了事件监听和调度机制。
        
    -   支持在链上或链下处理事件逻辑（可按需选择同步/异步处理）。
        
    -   执行逻辑的方式不再依赖主动交易触发，而是基于事件订阅。
        
-   **Reactive Network**：
    
    -   提供事件分发、状态同步、Callback Transaction 发送的基础设施。
        
    -   负责把链上事件传递给 Reactive Logic，并把逻辑执行结果发回链上。
        

**画一个简化链路：**链上事件 (Event) → Reactive Network 接收→ Reactive Logic 处理→ 生成 Callback Transaction→ 链上执行 → 更新状态  

## 3️⃣什么是事件驱动智能合约（Reactive Smart Contracts）

**定义**：合约逻辑不再只是响应交易，而是响应链上或链下事件，自动执行。

-   **特点**：
    
    1.  **自动化响应**：事件触发逻辑，不需要用户主动调用。
        
    2.  **可组合**：多个事件可以绑定不同逻辑，形成复杂业务流程。
        
    3.  **链下计算支持**：一些逻辑可以在链下执行，提高效率。  
        

**应用场景示例**：

-   NFT 盲盒开箱：购买事件触发生成随机 NFT。
    
-   DeFi 风控：价格波动事件触发清算逻辑。
    
-   DAO 自动管理：投票事件触发资金分配。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
