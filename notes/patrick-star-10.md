---
timezone: UTC+8
---

# SSS

**GitHub ID:** patrick-star-10

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->
# 今日学习内容

1.  **学习了 Reactive Network 的跨链事件与回调机制。**
    
2.  **Origin Contract（链 A）emit 事件，触发流程。**
    
3.  **REM 节点监听事件，自动生成交易，发送到链 B。**
    
4.  **Destination Contract（链 B）接收 REM 交易，执行回调逻辑。**
    
5.  **合约写法与普通合约相同，Reactive Contract 不需要账户。**
    
6.  **REM 节点代替合约签名交易，实现 自动执行。**
    
7.  **跨链回调原理：REM 节点作为桥梁，映射事件到目标链。**
    
8.  **REM 网络多个节点协作，保证触发可靠性。**
    
9.  **核心流程：事件发生 → REM 监听 → 自动交易 → 回调执行。**
    
10.  **类比理解：普通 EVM 被动执行，REM 主动监听并操作，实现事件驱动合约。**
     
11.  **Reactive的大概框架：**
     
     1.  ┌───────────────────────────┐
         
         │        用户/前端          │
         
         │  调用 Origin Contract API │
         
         └───────────┬───────────────┘
         
                     │ 交易请求
         
                     ▼
         
         ┌───────────────────────────┐
         
         │     链 A: Origin Contract │
         
         │ - 业务逻辑                │
         
         │ - emit Event              │
         
         └───────────┬───────────────┘
         
                     │ 事件日志
         
                     ▼
         
         ┌───────────────────────────┐
         
         │       REM 节点网络         │
         
         │ - 监听 Origin Contract Event
         
         │ - 解析事件触发条件
         
         │ - 生成交易调用 Destination Contract
         
         │ - 分布式协作，保证可靠触发
         
         └───────────┬───────────────┘
         
                     │ 交易
         
                     ▼
         
         ┌───────────────────────────┐
         
         │      链 B: Destination Contract │
         
         │ - 接收 REM 交易
         
         │ - 执行回调逻辑
         
         │ - 更新链上状态
         
         └───────────────────────────┘
         

# 学习感悟：

今天的学习让我对 **Reactive Network 和事件驱动合约**有了更直观的理解。之前我只知道 EVM 合约需要手动发交易才能执行，但今天通过跨链事件和 REM 节点的实验，我真正体会到 **事件触发、自动交易、跨链回调**的完整流程。我发现，Reactive Contract 本身不需要账户，所有自动执行和跨链回调都是 REM 节点代为完成的，这让我明白了“链上自动化”的本质：**合约逻辑被事件驱动，节点来完成签名和执行，完全解放了人工操作**。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
