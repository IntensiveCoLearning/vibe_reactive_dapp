---
timezone: UTC+8
---

# sgxy975-bit

**GitHub ID:** sgxy975-bit

**Telegram:** 

## Self-introduction

Let's vibe Reactive dApp！

## Notes

<!-- Content_START -->
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
Solidity：storage 和 memory 赋值行为 + 应用场景

一、先记住核心本质（决定所有行为）

1\. **storage = 引用赋值（改副本 = 改原数据）**

2\. **memory = 拷贝赋值（改副本 = 不改原数据）**

二、storage 赋值行为 & 应用场景

1\. 赋值特点

\- 把\*\*链上数据的引用\*\*给变量

\- 修改这个变量 = **直接修改区块链上的数据**

\- 永久生效，花费 Gas

2\. 典型应用场景

场景 1：在函数里直接修改链上状态

\`\`\`solidity

contract Demo {

uint256 public num = 10; // storage

function test() public {

// 把链上变量赋值给 storage 变量

uint256 storage x = num;

x = 100;

// num 也变成 100！直接改链上数据

}

}

\`\`\`

场景 2：操作结构体、数组（批量修改链上数据）

\`\`\`solidity

struct User {

uint256 age;

}

User\[\] public users; // storage 数组

function updateAge(uint256 index) public {

// 获取 storage 引用

User storage user = users\[index\];

user.age = 20; // 直接修改链上数据

}

\`\`\`

场景 3：睿应层合约中自动更新状态

反应式合约需要\*\*实时修改链上状态\*\*，必须用 storage。

\---

三、memory 赋值行为 & 应用场景

1\. 赋值特点

\- 复制一份\*\*临时数据\*\*

\- 修改副本\*\*不会影响原数据\*\*

\- 临时使用，不花 Gas，函数结束消失

2\. 典型应用场景

场景 1：读取数据但不修改（安全）

\`\`\`solidity

function getUserName() public view returns(string memory) {

string memory name = userName; // 复制一份

return name;

// 原数据 userName 完全不变

}

\`\`\`

场景 2：函数参数传递（不污染原数据）

\`\`\`solidity

function setName(string memory \_name) public {

// \_name 是临时拷贝，不会影响外部

userName = \_name;

}

\`\`\`

场景 3：临时计算、排序、逻辑处理

\`\`\`solidity

function calculate(uint256\[\] memory arr) public pure returns(uint256) {

arr\[0\] = 999; // 只改临时拷贝，原数据不变

return arr\[0\];

}

\`\`\`

场景 4：返回新创建的数组/结构体

\`\`\`solidity

function createUser() public pure returns(User memory) {

User memory user; // 临时创建

user.age = 18;

return user;

}

\`\`\`

\# 四、一张表看懂所有区别（必背）

| 特性 | storage 赋值 | memory 赋值 |

|-----|-------------|-------------|

| 数据位置 | 区块链硬盘 | 内存 |

| 赋值方式 | 引用（改副本=改原数据） | 拷贝（改副本=不改原） |

| 永久保存 | ✅ 是 | ❌ 否 |

| 花费 Gas | ✅ 费 | ❌ 不费 |

| 主要用途 | 修改链上状态 | 临时计算、传参、返回 |

\---

\# 五、最简单使用口诀

想改链上数据 → storage

只想临时用用 → memory

引用赋值会改原数据

拷贝赋值安全不影响

\`\`\`
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->

Solidity 语法基础

一、开头必须写：版本声明

所有合约第一行固定写法，指定编译器版本：

// 表示使用 0.8.20 及以上版本

pragma solidity ^0.8.20;

\-–

二、合约结构（固定骨架）

一个合约 = 一个 `contract`，所有代码都写在里面：

pragma solidity ^0.8.20;

// 合约定义

contract MyContract {

// 这里写变量、函数

}

三、变量与数据类型（必背）

1\. 值类型（最常用）

// 1. 无符号整数（正数，用得最多）

uint256 public a = 100;

// 2. 布尔值

bool public isTrue = true;

// 3. 地址类型（钱包/合约地址）

address public myWallet = 0x1234567890123456789012345678901234567890;

// 4. 字符串

string public name = “Reactive”;

// 5. 定长字节（存哈希、数据）

bytes32 public b32 = “hello”;

2\. 引用类型

// 数组

uint256\[\] public nums = \[1,2,3\];

// 映射（键值对，最重要！）

mapping(address => uint256) public balance;

四、变量作用域（必须懂）

1\. 状态变量（存在区块链上）

写在合约内、函数外，\*\*永久存储\*\*，需要消耗 Gas：

contract Test {

uint256 public count = 0; // 状态变量

}

2\. 局部变量（函数内临时用）

只在函数运行时存在，\*\*不存链上，不花 Gas\*\*：

function test() public pure {

uint256 x = 10; // 局部变量

}

五、函数语法（核心中的核心）

1\. 函数基本格式

function 函数名(参数) 权限修饰符 返回值 {

// 代码

}

2\. 最常用 4 种修饰符

\- **public**：任何人都能调用

\- **view**：只读数据，\*\*不花 Gas\*\*

\- **pure**：纯计算，不读不改数据

\- **private**：只有合约内部能调用

3\. 常见函数示例

// 只读函数（view）

function getNum() public view returns(uint256) {

return count;

}

// 修改数据函数

function setNum(uint256 \_num) public {

count = \_num;

}

// 纯计算函数（pure）

function add(uint256 x, uint256 y) public pure returns(uint256) {

return x + y;

}

六、流程控制：if / for

1\. 判断语句 if

function check(uint256 x) public pure returns(bool) {

if(x > 10) {

return true;

} else {

return false;

}

}

2\. 循环语句 for

function sum() public pure returns(uint256) {

uint256 s = 0;

for(uint256 i=0; i<10; i++) {

s += i;

}

return s;

}

七、映射 Mapping（区块链必备）

用来存“地址 → 数值”，比如余额、权限：

// 定义：key 是 address，value 是 uint256

mapping(address => uint256) public userBalance;

// 赋值

userBalance\[msg.sender\] = 1000;

// 取值

uint256 bal = userBalance\[msg.sender\];

八、内置关键字（常用）

\- **msg.sender**：调用合约的\*\*当前钱包地址\*\*

\- **msg.value**：发送的 ETH 数量

\- **this**：当前合约

\- **require**：条件判断，不满足就报错回滚

// 必须满足条件才能继续执行

function withdraw(uint256 amount) public {

require(amount <= 100, “金额不能超过100”);

}

九、完整极简示例（背）

pragma solidity ^0.8.20;

contract SimpleDemo {

uint256 public number;

function setNumber(uint256 \_num) public {

number = \_num;

}

function getNumber() public view returns(uint256) {

return number;

}

function add(uint256 x) public view returns(uint256) {

return number + x;

}

}

🎯 语法速记口诀

版本声明第一行

合约结构大括号

状态变量存链上

view只读不花gas

public调用最常用

mapping键值存数据

if判断for循环

require做校验
<!-- DAILY_CHECKIN_2026-03-10_END -->

# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->


反应式智能合约：解锁Web3自动化新范式——Ivan Ivanskitiy深度访谈解读

在区块链技术不断演进的今天，智能合约作为Web3世界的核心构建模块，正面临着从“被动响应”到“主动执行”的范式转型。近日，Reactive Network的开发者兼负责人Ivan Ivanskitiy分享了对反应式智能合约的深度思考，从技术定位、风险控制到生态布局，全面揭示了这一创新方向的潜力与挑战。

一、重新定义智能合约：从被动触发到主动响应

传统智能合约如同“沉睡的程序”，只有在外部交易触发时才会执行，这一局限严重制约了复杂应用场景的实现。而反应式智能合约则彻底改变了这一逻辑，它能够主动监听链上事件、实时响应状态变化，并自动执行预设逻辑。

Ivan强调，Reactive Network并非要颠覆现有EVM或Solidity生态，而是在其之上构建了一层增强能力。开发者无需重写现有合约，即可通过反应式框架赋予其自动化能力，这大大降低了技术迁移成本。这种模式特别适合高频金融场景，如自动化做市、动态清算、套利策略执行等，能够实现传统合约难以企及的实时性与效率。

二、风险与安全：构建自动化的防护网

自动化执行带来了效率提升，也引入了新的安全挑战。Ivan坦言，反应式合约的自动操作可能放大MEV攻击、逻辑漏洞和数据依赖风险。为此，Reactive Network设计了多层防护机制：

\- **模块化风险控制**：提供可插拔的风险组件，支持紧急暂停、操作回滚和细粒度权限控制。

\- **最小权限原则**：限制合约自动操作的范围，避免单点故障引发连锁反应。

\- **安全审计工具**：与专业安全团队合作，推出针对性审计框架和最佳实践指南。

这些措施并非消除风险，而是将其可控化，让开发者能够在创新与安全之间找到平衡。

三、技术突破：动态实体与流数据服务

为了支撑更复杂的自动化场景，Reactive Network推出了两大核心技术：动态实体（Dynamic Entities）和流数据服务（FDS）。

动态实体允许合约在运行时动态创建、修改和删除状态对象，打破了传统合约的静态结构限制。而FDS则结合了实时数据流处理能力，使得智能合约能够基于连续的市场数据、用户行为等信息做出智能决策。这两项技术的结合，为DeFi、GameFi等领域开辟了全新的可能性，例如能够根据市场波动自动调整流动性策略的做市商，或是随玩家行为动态演变的游戏规则。

四、生态布局：从EVM到跨链的未来蓝图

在生态支持方面，Reactive Network制定了清晰的路线图：

\- **短期**：深化与EVM生态的集成，优化Solidity支持，推出完善的SDK和调试工具。

\- **中期**：扩展至Solana的SVM等高性能链，满足不同场景的性能需求。

\- **长期**：打造跨链反应式合约框架，实现多链间的自动化协同。

Ivan透露，团队正计划举办黑客松活动，并提供专项开发基金，以吸引更多开发者参与生态建设。这些举措将加速反应式智能合约从概念走向落地。

五、机遇与挑战：开启Web3自动化新纪元

反应式智能合约为Web3带来了前所未有的机遇：它开创了自动化金融（AutoFi）、实时游戏等新赛道，让“自主运行”的应用成为可能。但同时，开发者也需要适应新的编程范式和安全思维，生态建设也需要时间来成熟。

从原子化的单链自动化到未来的跨链协同，Reactive Network正逐步构建起Web3的自动化基础设施。正如Ivan所言，这不是对现有生态的替代，而是一次赋能升级——让智能合约真正“活”起来，成为驱动Web3世界高效运转的核心引擎。

随着技术的不断完善和生态的日益壮大，反应式智能合约有望成为下一代Web3应用的标准配置，重塑我们对区块链应用的想象边界。
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
