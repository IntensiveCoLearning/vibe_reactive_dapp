---
timezone: UTC+8
---

# vstral

**GitHub ID:** vstralcn

**Telegram:**

## Self-introduction

致力于学习研究 Web3安全

## Notes

<!-- Content_START -->
# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->
\# Task1

第一步：部署原始合约：

\`\`\`

$ forge create --broadcast --rpc-url $rpc --private-key $pk ./L1Contract.sol:BasicDemoL1Contract \[⠊\] Compiling...

\[⠒\] Compiling 1 files with Solc 0.8.30

\[⠢\] Solc 0.8.30 finished in 35.46ms

Compiler run successful!

Deployer: 0x038405665520AC945D26eB7936ffe0115B2f2BBd

Deployed to: 0xdB9F527Ba87658790D5AC07FF11Eadc26fdEa94b

Transaction hash: 0xacee82c4440ef2cd827da6f719dfc76e44414bf6271577cb34a7c735c70c3c1f

\`\`\`

第二步：部署目标合约：

\`\`\`

$ forge create --broadcast --rpc-url $rpc --private-key $pk ./L1Callback.sol:BasicDemoL1Callback --value 0.02ether --constructor-args 0xdB9F527Ba87658790D5AC07FF11Eadc26fdEa94b

\[⠊\] Compiling...

\[⠒\] Unable to resolve imports:

"../../../lib/reactive-lib/src/abstract-base/AbstractCallback.sol" in "/home/vstral/Code/Web3/ReactiveNetwork/Task1/L1Callback.sol"

with remappings:

@reactive/=/home/vstral/Code/Web3/ReactiveNetwork/Task1/lib/reactive-lib/src/

forge-std/=/home/vstral/Code/Web3/ReactiveNetwork/Task1/lib/reactive-lib/lib/forge-std/src/

reactive-lib/=/home/vstral/Code/Web3/ReactiveNetwork/Task1/lib/reactive-lib/src/

\[⠢\] Compiling 1 files with Solc 0.8.30

\[⠆\] Solc 0.8.30 finished in 10.48ms

Compiler run successful!

Deployer: 0x038405665520AC945D26eB7936ffe0115B2f2BBd

Deployed to: 0x85142BD0eE89D05f6883cA190C71d47eAaDbc7bE

Transaction hash: 0xa664950b5d86640009373583570462d4bf7dfda9f845058d26dc7a22d970df4c

\`\`\`

第三步：部署reactive合约

\`\`\`

$ forge create \\

\--broadcast \\

\--rpc-url $reactive\_rpc \\

\--private-key $pk \\

./ReactiveContract.sol:BasicDemoReactiveContract \\

\--value 0.1ether \\

\--constructor-args \\

$SYSTEM\_CONTRACT\_ADDR \\

$ORIGIN\_CHAIN\_ID \\

$DESTINATION\_CHAIN\_ID \\

$ORIGIN\_ADDR \\

0x8cabf31d2b1b11ba52dbb302817a3c9c83e4b2a5194d35121ab1354d69f6a4cb \\

$CALLBACK\_ADDR

\[⠊\] Compiling...

\[⠒\] Unable to resolve imports:

"../../../lib/reactive-lib/src/abstract-base/AbstractReactive.sol" in "/home/vstral/Code/Web3/ReactiveNetwork/Task1/ReactiveContract.sol"

"../../../lib/reactive-lib/src/interfaces/ISystemContract.sol" in "/home/vstral/Code/Web3/ReactiveNetwork/Task1/ReactiveContract.sol"

"../../../lib/reactive-lib/src/interfaces/IReactive.sol" in "/home/vstral/Code/Web3/ReactiveNetwork/Task1/ReactiveContract.sol"

with remappings:

@reactive/=/home/vstral/Code/Web3/ReactiveNetwork/Task1/lib/reactive-lib/src/

forge-std/=/home/vstral/Code/Web3/ReactiveNetwork/Task1/lib/reactive-lib/lib/forge-std/src/

reactive-lib/=/home/vstral/Code/Web3/ReactiveNetwork/Task1/lib/reactive-lib/src/

\[⠢\] Compiling 1 files with Solc 0.8.30

\[⠆\] Solc 0.8.30 finished in 15.54ms

Compiler run successful!

Deployer: 0x038405665520AC945D26eB7936ffe0115B2f2BBd

Deployed to: 0xc7bF61Bd261eB7F2FFA265fE2100BBC6e9312Bb3

Transaction hash: 0xda54e4efd8c7e1108705b9552d67f02ba4df1e0de7b75b9c78b0f4dd711e485b

\`\`\`

第四步：测试reactive回调

\`\`\`

$ cast send $ORIGIN\_ADDR --rpc-url $reactive\_rpc --private-key $pk --value 0.001ether

blockHash 0x19702a48cb3250c88b6057996479650940c0096d149337d8961aaa6ba3363632

blockNumber 2673144

contractAddress

cumulativeGasUsed 51382

effectiveGasPrice 112000000000

from 0x038405665520AC945D26eB7936ffe0115B2f2BBd

gasUsed 21000

logs \[\]

logsBloom 0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000

root

status 1 (success)

transactionHash 0xfe2902ccc8a0031a0852a11e96e64f125b83424d37ad2cb736f02fdf31de3f42

transactionIndex 1

type 2

blobGasPrice

blobGasUsed

to 0xdB9F527Ba87658790D5AC07FF11Eadc26fdEa94b

\`\`\`

原始合约地址：0xdB9F527Ba87658790D5AC07FF11Eadc26fdEa94b

目标合约地址：0x85142BD0eE89D05f6883cA190C71d47eAaDbc7bE

reactive合约地址：0xc7bF61Bd261eB7F2FFA265fE2100BBC6e9312Bb3

测试回调实现交易hash：0xfe2902ccc8a0031a0852a11e96e64f125b83424d37ad2cb736f02fdf31de3f42

!\[image.png\]([https://raw.githubusercontent.com/vstralcn/note\_image/main/20260309201313.png](https://raw.githubusercontent.com/vstralcn/note_image/main/20260309201313.png))

!\[image.png\]([https://raw.githubusercontent.com/vstralcn/note\_image/main/20260309201325.png](https://raw.githubusercontent.com/vstralcn/note_image/main/20260309201325.png))
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
