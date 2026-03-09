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
