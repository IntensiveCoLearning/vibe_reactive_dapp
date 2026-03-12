---
timezone: UTC+8
---

# kaka

**GitHub ID:** narnona

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
Reactive为什么分为RNK与RVM两个环境？

**效率和可扩展性 (Efficiency & Scalability)**

如果让一个单一的区块链既处理用户交易，又实时监听并响应成千上万个来自不同链的事件，网络会变得异常拥堵和缓慢。通过将“监听和反应”这个高频、重复性的工作剥离到专门的、可并行运行的 RVM 中，主网络 (RNK) 就可以专注于处理用户交互和状态共识，从而实现整个系统的高效和可扩展
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

## Reactive Network 存储查询不一致 (RNK vs RVM)

### **1\. 背景信息**

-   **网络**: Reactive Lasna Testnet
    
-   **合约地址**: [0xc1aa02EE5fAD3973Ff7F9B861AAeFC65A7Df07b2](https://lasna.reactscan.net/address/0x6af9d10d222b356ca61837629c89e4124461ad70/contract/0xc1aa02ee5fad3973ff7f9b861aaefc65a7df07b2)
    
-   **目标变量**: `number` (位于 Storage **Slot 0**)
    
-   **操作**: 已执行 `setNumber(666)` 交易 ([0x541f40…](https://lasna.reactscan.net/tx/0x541f40f25bc3823821abc2d6672645cb329dc6c21c2cb1e5f9a47cd3f99211dd))
    

* * *

### **2\. 现象描述**

### **现象 A：通过标准 RPC 调用 (正常)**

使用 `cast call` 查询合约状态，能够正确返回设置的值。 **命令**: `cast call 0xc1aa02EE5fAD3973Ff7F9B861AAeFC65A7Df07b2 "getNumber()(uint256)" --rpc-url <https://lasna-rpc.rnk.dev/`\> **结果**: `666` ✅

### **现象 B：通过** `rnk_getStorageAt` **查询 (异常)**

使用 Reactive 特有的 RVM 存储查询接口，返回值为全零。接口文档：[https://dev.reactive.network/rnk-rpc-methods#rnk\_getstorageat](https://dev.reactive.network/rnk-rpc-methods#rnk_getstorageat) **命令**:

```bash
curl --location '<https://lasna-rpc.rnk.dev/>' \\
--header 'Content-Type: application/json' \\
--data '{
  "jsonrpc": "2.0",
  "method": "rnk_getStorageAt",
  "params": [
    "0x6aF9D10d222b356CA61837629C89E4124461AD70",
    "0xc1aa02EE5fAD3973Ff7F9B861AAeFC65A7Df07b2",
    "0x0000000000000000000000000000000000000000000000000000000000000000",
    "latest"
  ],
  "id": 1
}' | jq
```

**结果**: `"result": "0x0000000000000000000000000000000000000000000000000000000000000000"`

* * *

### **3\. 结论**

在 **RNK (Reactive Network)** 层面，合约状态已更新为 `666`；但在 **RVM (ReactVM)** 存储层，同一合约地址在相同 Slot 下的值仍为 `0`。
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->


学习了reactive智能合约的编写，并尝试编写一个跨链传输的demo。

每个链可以部署一个MsgNode合约：

\`\`\`solidity

// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.20;

import {AbstractCallback} from "@reactive/contracts/abstract-base/AbstractCallback.sol";

contract MsgNode is AbstractCallback {

event SendMessage(

address indexed sender,

address indexed receiver,

uint256 indexed destinationChainid,

string message

);

event ReceivedMessage(

address indexed sender,

address indexed receiver,

uint256 indexed sourceChainid,

string message

);

constructor(address _callback_sender) AbstractCallback(\_callback\_sender) payable {}

function sendMessage(address receiver, uint256 destinationChainid, string calldata message) external {

emit SendMessage(

msg.sender,

receiver,

destinationChainid,

message

);

}

function ReceiveMessage(

address sender,

address receiver,

uint256 sourceChainid,

string memory message

) external authorizedSenderOnly rvmIdOnly(sender) {

emit ReceivedMessage(

sender,

receiver,

sourceChainid,

message

);

}

}

\`\`\`

以及reactive网络的监听合约：

\`\`\`solidity

// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.20;

import { IReactive } from "@reactive/contracts/interfaces/IReactive.sol";

import { AbstractReactive } from "@reactive/contracts/abstract-base/AbstractReactive.sol";

import { ISystemContract } from "@reactive/contracts/interfaces/ISystemContract.sol";

import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

contract ReactiveContract is IReactive, AbstractReactive, Ownable {

error AlreadySet();

error NeverSet();

error NodeNotExist();

event SetMsgNode(uint256 indexed chainId, address msgNode);

event DisableMsgNode(uint256 indexed chainId);

// keccak256("SendMessage(address,address,uint256,string)")

uint256 constant TOPIC\_0 = uint256(0x20cf7a31d7cd62c38d409eea8194ebbc741d82d1631d66f4dd7b0bac9c3d5e20);

mapping(uint256 chainId => address) msgNode;

uint64 private constant GAS\_LIMIT = 1000000;

constructor(address _service, address_ owner) Ownable(\_owner) {

service = ISystemContract(payable(\_service));

}

// 订阅

function setMsgNode(uint256 _chainId, address_ msgNode) external onlyOwner {

if(msgNode\[\_chainId\] == \_msgNode) revert AlreadySet();

msgNode\[\_chainId\] = \_msgNode;

if (!vm) {

service.subscribe(

\_chainId,

\_msgNode,

TOPIC\_0,

REACTIVE\_IGNORE,

REACTIVE\_IGNORE,

REACTIVE\_IGNORE

);

}

emit SetMsgNode(\_chainId, \_msgNode);

}

// 取消订阅

function disableMsgNode(uint256 \_chainId) external onlyOwner {

if(msgNode\[\_chainId\] == address(0)) revert NeverSet();

delete msgNode\[\_chainId\];

if(!vm) {

service.unsubscribe(

\_chainId,

msgNode\[\_chainId\],

TOPIC\_0,

REACTIVE\_IGNORE,

REACTIVE\_IGNORE,

REACTIVE\_IGNORE

);

}

emit DisableMsgNode(\_chainId);

}

function react(LogRecord calldata log) external vmOnly {

uint256 destinationChainid = log.topic\_3;

if(msgNode\[destinationChainid\] == address(0)) revert NodeNotExist();

address sender = address(uint160(log.topic\_1));

address receiver = address(uint160(log.topic\_2));

bytes memory payload = abi.encodeWithSignature(

"ReceiveMessage(address,address,uint256,string)",

sender,

receiver,

log.chain\_id,

string([log.data](http://log.data)) // message

);

emit Callback(log.topic\_3, msgNode\[destinationChainid\], GAS\_LIMIT, payload);

}

}

\`\`\`  
但在实际测试中遇到了回调失败的问题，后续会深入分析并解决。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
