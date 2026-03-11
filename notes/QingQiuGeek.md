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
