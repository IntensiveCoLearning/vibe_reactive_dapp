---
timezone: UTC+8
---

# PaulCoinmanlabs

**GitHub ID:** PaulCoinmanlabs

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->
-   传统的开发开发者需要自己维护后端服务器，不断向 RPC 节点发送请求询问，这种模式存在高延迟、中心化单点故障且浪费服务器资源，在Reactive模式下，通过 `subscribe` 向网络提交**逻辑预案**（If-this-then-that）。你不需要主动看链，而是让链在事件发生时主动推你。这种控制权的移交，使得智能合约第一次拥有了**原生的异步处理能力。**
    
-   在 Ethereum 上开发，交易是原子的（Atomic），要么全成，要么全败，在编写 `react()` 函数时，必须极其严谨地处理**权限校验 (**`onlySystem`**)**。因为 Reactive 响应不再依赖用户的私钥签名，而是依赖节点的共识触发，防御虚假回调是商业级安全的第一要素。
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

-   深入学习reactive的理论知识
    
-   构思自己的reactive的demo
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->


**1.学习Reacitve相关的理论知识，比如主网配置、测试TOKEN的获取**

-   直接跟合约交互（向合约发送Sepolia的ETH获取即可）：0x9b9BB25f1A81078C544C829c5EB7822d747Cf434
    

**2.使用Foundry进行开发一个测试demo合约**

-   自己刚好有一个合约就直接在这个合约上扩展，源合约在Sepolia
    

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

contract SimpleVault {
    // 记录每个地址的余额
    mapping(address => uint256) public balances;

    // 定义事件方便监听
    event Deposit(address indexed user, uint256 indexed amount);
    event Withdrawal(address indexed user, uint256 indexed amount);

    error ZeroAmount();
    error InsufficientBalance();
    error TransferFailed();

    function deposit() external payable {
        if (msg.value == 0) {
            revert ZeroAmount();
        }

        balances[msg.sender] += msg.value;
        emit Deposit(msg.sender, msg.value);
    }

    function withdraw(uint256 amount) external {
        if (balances[msg.sender] < amount) {
            revert InsufficientBalance();
        }
        balances[msg.sender] -= amount;
        (bool success, ) = msg.sender.call{value: amount}("");
        if (!success) {
            revert TransferFailed();
        }

        emit Withdrawal(msg.sender, amount);
    }
}
```

-   编写目标链合约在base的Sepolia上面 这里记得引入依赖 forge install Reactive-Network/reactive-lib
    

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "reactive-lib/abstract-base/AbstractCallback.sol";

contract SimpleVaultCallback is AbstractCallback {
    event CallbackReceived(
        address indexed origin,
        address indexed sender,
        address indexed reactive_sender
    );

    constructor(
        address _callback_sender
    ) payable AbstractCallback(_callback_sender) {}

    function callback(
        address sender
    ) external authorizedSenderOnly rvmIdOnly(sender) {
        emit CallbackReceived(tx.origin, msg.sender, sender);
    }
}
```

-   很多小伙伴按照官方提供的demo可能有问题 就是引入依赖的问题 可以查看映射 forge remappings > remappings.txt 类似下面的
    

```
forge-std/=lib/forge-std/src/
reactive-lib/=lib/reactive-lib/src/
```

-   编写SimpleVaultReactiveContract合约
    
    ```solidity
    // SPDX-License-Identifier: UNLICENSED
    pragma solidity ^0.8.13;
    
    import "reactive-lib/interfaces/IReactive.sol";
    import "reactive-lib/abstract-base/AbstractReactive.sol";
    import "reactive-lib/interfaces/ISystemContract.sol";
    
    contract SimpleVaultReactiveContract is IReactive, AbstractReactive {
        uint256 public originChainId;
        uint256 public destinationChainId;
        uint64 private constant GAS_LIMIT = 1000000;
    
        address private callback;
        constructor(
            address _service,
            uint256 _originChainId,
            uint256 _destinationChainId,
            address _contract,
            uint256 _topic_0,
            address _callback
        ) payable {
            service = ISystemContract(payable(_service));
            originChainId = _originChainId;
            destinationChainId = _destinationChainId;
            callback = _callback;
    
            if (!vm) {
                service.subscribe(
                    originChainId,
                    _contract,
                    _topic_0,
                    REACTIVE_IGNORE,
                    REACTIVE_IGNORE,
                    REACTIVE_IGNORE
                );
            }
        }
    
        function react(LogRecord calldata log) external vmOnly {
            if (log.topic_2 >= 0.001 ether) {
                bytes memory payload = abi.encodeWithSignature(
                    "callback(address)",
                    address(0)
                );
                emit Callback(destinationChainId, callback, GAS_LIMIT, payload);
            }
        }
    }
    ```
    

**3.合约部署脚本编写**

-   个人习惯用makefile编写 大家可以参考下：
    

```makefile
ETH_URL := "https://ethereum-sepolia-rpc.publicnode.com"

BASE_URL := "https://base-sepolia.drpc.org"

REACTIVE_RPC := "https://lasna-rpc.rnk.dev/"
# 这个值不用改 就是System Contract官方的
SYSTEM_CONTRACT_ADDR := "0x0000000000000000000000000000000000fffFfF"
# Sepolia的链id
ORIGIN_CHAIN_ID := "11155111"
# base的Sepolia的链id
DESTINATION_CHAIN_ID := "84532"
# 开始不用写这个 等你在源链部署合约上去了才能获取到 这里是Sepolia
ORIGIN_ADDR := "0x39C8eb8a21f551c1Fd18c6D8550cC806ff6047C9"
# 开始不用写这个 等你在目标链部署合约上去了才能获取到 这里是base的Sepolia
CALLBACK_ADDR := "0x094aB5EEA0764891C24c3889C356E4a405B1e94E"

PKS := "你的测试部署的钱包私钥"
#  目的链上的服务地址 直接去官网找 我这里是base的测试网络的
DESTINATION_CALLBACK_PROXY_ADDR :="0xa6eA49Ed671B8a4dfCDd34E36b7a75Ac79B8A5a6"

# .PHONY 告诉 make 这些是命令名字，不是文件夹名字
.PHONY: build test node d deploy-eth deploy-base  deploy-Reactive deposit-Reactive

# ==========================================
# 基础开发指令 (日常敲得最多的)
# ==========================================
build:
	forge build

test:
	forge test -vv

# 一键查看测试覆盖率
coverage:
	forge coverage

# 开始执行的命令链上交互
deploy-eth:
	forge create --broadcast --rpc-url $(ETH_URL) --private-key $(PKS) src/SimpleVault.sol:SimpleVault

deploy-base:
	forge create --broadcast --rpc-url $(BASE_URL) --private-key $(PKS) src/SimpleVaultCallback.sol:SimpleVaultCallback --value 0.001ether --constructor-args $(DESTINATION_CALLBACK_PROXY_ADDR)


# 这里需要注意下 0xe1fffcc4923d04b559f4d29a8bfc6cda04eb5b0d3c460751c2402c5c5cc9109c 是你event的Keccak-256 哈希值就是TOPIC_0 跟官方一样的不需要修改 不一样的比如我这里是deposit 就使用cast keccak "Deposit(address,uint256)" 获取到即可
deploy-Reactive:
	forge create --broadcast --rpc-url $(REACTIVE_RPC) --private-key ${PKS} src/SimpleVaultReactiveContract.sol:SimpleVaultReactiveContract --value 0.001ether --constructor-args $(SYSTEM_CONTRACT_ADDR) $(ORIGIN_CHAIN_ID) $(DESTINATION_CHAIN_ID) $(ORIGIN_ADDR) 0xe1fffcc4923d04b559f4d29a8bfc6cda04eb5b0d3c460751c2402c5c5cc9109c  $(CALLBACK_ADDR)

deposit-Reactive:
	cast send $(ORIGIN_ADDR) "deposit()" --value 0.001ether --private-key $(PKS) --rpc-url $(ETH_URL)
```

**4.测试结果**

```
sepolia合约地址：0x39C8eb8a21f551c1Fd18c6D8550cC806ff6047C9
Transaction hash: 0x1c144921d4106e78f6c7417aa15bfef550eb19a351e01bac3a6ebd7a3acd4d62

base的合约地址：0x094aB5EEA0764891C24c3889C356E4a405B1e94E
Transaction hash: 0x588f45e2aec037bf9666bdc41cc1fda199b42475cc0ad25de06ffc6ba402d992

Reactive合约地址：0x094aB5EEA0764891C24c3889C356E4a405B1e94E
Transaction hash: 0x8207a388387344fb77ec3f379d3fc36e4f7200b2fa24a8e92ee4a6ee79647014

触发deposit的hash：0x7f6294c9d5b09d5e816100c897c3b448abf0591f649ef100befc6bb3471b75ac
```
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
