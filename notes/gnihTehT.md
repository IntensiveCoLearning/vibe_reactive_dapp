---
timezone: UTC+8
---

# ShadowDogee

**GitHub ID:** gnihTehT

**Telegram:**

## Self-introduction

shadowdoge，web3新人

## Notes

<!-- Content_START -->
# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->
# 为什么你 deploy 会 revert

关键在这个逻辑：

```
if (!vm) {
    service.subscribe(...)
}
```

`vm` 是 Reactive 用来区分：

```
ReactVM
vs
普通 EVM
```

Reactive lib 会通过：

```
extcodesize(system contract)
```

检测运行环境。

如果不是 ReactVM：

```
vm = false
```

于是 constructor 会调用：

```
service.subscribe()
```

但你当前 RPC：

```
https://lasna-rpc.rnk.dev
```

**并没有 system contract**

所以：

```
subscribe() call → revert
```

这就是你遇到的：

```
execution reverted
```
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->

**Reactive Network Demo 的核心逻辑是一个跨链的“监听-触发”机制。**

这个 Demo 流程展示了从用户在源链（Origin Chain）发起交易，到响应网络（Reactive Network）处理，最后在目标链（Destination Chain）执行回调的完整闭环。

### 过程分为四个关键动作：

1.  **资产触发 (Origin Chain)**
    
    -   **User** $\\xrightarrow{\\text{Send ETH}}$ **BasicDemoL1Contract**
        
    -   合约逻辑执行，抛出 `Received` 事件。
        
    -   _关键数据：_ `topic_0` (事件签名), `topic_3` (金额)。
        
2.  **异步监听 (Reactive Network)**
    
    -   **Reactive Node** 扫描源链区块。
        
    -   **BasicDemoReactiveContract** 捕获匹配 `topic_0` 的日志。
        
3.  **逻辑过滤与指令下发**
    
    -   **判定：** `if (topic_3 >= 0.01 ETH)`。
        
    -   **执行：** 构造 `Callback` 负载（Payload），发往目标链。
        
    -   _注意：_ 这一步在 Reactive Network 内部完成，具有低延迟特性。
        
4.  **目标确认 (Destination Chain)**
    
    -   **Callback Proxy** 接收指令 $\\rightarrow$ 调用 **BasicDemoL1Callback**。
        
    -   **验证：** 检查调用者是否为授权代理。
        
    -   **结果：** 抛出 `CallbackReceived` 事件，完成闭环。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
