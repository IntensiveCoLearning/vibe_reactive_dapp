---
timezone: UTC+8
---

# W5W8L9jlu

**GitHub ID:** W5W8L9jlu

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-12
<!-- DAILY_CHECKIN_2026-03-12_START -->
solidity持续中...其实今天还把basic demo拉下来读了，还没通读完，明天继续，并且把文档和demo中的内容对一对

# solidity语法基础回顾（啃basic demo在回顾一下吧

<aside> 💡

[https://github.com/CryptozombiesHQ/cryptozombies-lesson-code](https://github.com/CryptozombiesHQ/cryptozombies-lesson-code)

</aside>

## **版本指令**

所有的 Solidity 源码都必须冠以 "version pragma" — 标明 Solidity 编译器的版本. 以避免将来新的编译器可能破坏你的代码。

例如: `pragma solidity ^0.4.19;` (当前 Solidity 的最新版本是 0.4.19).

综上所述， 下面就是一个最基本的合约 — 每次建立一个新的项目时的第一段代码:

```
pragma solidity ^0.4.19;

contract HelloWorld {

}
```

## **状态变量和整数**

**_状态变量_**是被永久地保存在合约中。也就是说它们被写入以太币区块链中. 想象成写入一个数据库。

### 例子:

```solidity
contract Example{
  // 这个无符号整数将会永久的被保存在区块链中
  uint myUnsignedInteger = 100;
}
```

在上面的例子中，定义 `myUnsignedInteger` 为 `uint` 类型，并赋值100。

## **数学运算**

在 Solidity 中，数学运算很直观明了，与其它程序设计语言相同:

-   加法: `x + y`
    
-   减法: `x - y`,
    
-   乘法: `x * y`
    
-   除法: `x / y`
    
-   取模 / 求余: `x % y` _(例如,_ `13 % 5` _余_ `3`_, 因为13除以5，余3)_
    

Solidity 还支持 **_乘方操作_** (如：x 的 y次方） // 例如： 5 \*\* 2 = 25

```
uint x = 5 ** 2; // equal to 5^2 = 25
```

## **结构体**

有时你需要更复杂的数据类型，Solidity 提供了 **结构体**:

```csharp
struct Person {
  uint age;
  string name;
}
```

结构体允许你生成一个更复杂的数据类型，它有多个属性。

> _注：我们刚刚引进了一个新类型,_ `string`_。 字符串用于保存任意长度的 UTF-8 编码数据。 如：_ `string greeting = "Hello world!"`_。_

## **数组**

如果你想建立一个集合，可以用 **_数组_这样的数据类型. Solidity 支持两种数组: _静态_ 数组和_动态_** 数组:

```
// 固定长度为2的uint类型静态数组:
uint[2] fixedArray;
// 固定长度为5的string类型的静态数组:
string[5] stringArray;
// uint类型动态数组，长度不固定，可以动态添加元素:
uint[] dynamicArray;
```

你也可以建立一个 **_结构体_**类型的数组 例如，上一章提到的 `Person`:

```
Person[] people; // 这是类型为结构体（或者说Person）的名为people的动态数组，可以不断添加元素
```

记住：状态变量被永久保存在区块链中。所以在你的合约中创建动态数组来保存成结构的数据是非常有意义的。

## **公共数组**

你可以定义 `public` 数组, Solidity 会自动创建 **_getter_** 方法. 语法如下:

```
Person[] public people;
```

其它的合约可以从这个数组读取数据（但不能写入数据），所以这在合约中是一个有用的保存公共数据的模式。

## **定义函数**

在 Solidity 中函数定义的句法如下:

```
function eatHamburgers(string _name, uint _amount) {

}//函数 eatHamburgers 有两个参数，分别是 string 类型的 _name 和 unit 类型的 _amount
```

这是一个名为 `eatHamburgers` 的函数，它接受两个参数：一个 `string`类型的 和 一个 `uint`类型的。现在函数内部还是空的。

> _注：: 习惯上函数里的变量都是以(_`_`_)开头 (但不是硬性规定) 以区别全局变量。我们整个教程都会沿用这个习惯。_

我们的函数定义如下:

```
eatHamburgers("vitalik", 100);
```

## **创建新的结构体**

还记得上个例子中的 `Person` 结构体：

```csharp
struct Person {
  uint age;
  string name;
}

Person[] public people;
```

现在创建新的 `Person` 结构，然后把它加入到名为 `people` 的数组中.

```solidity
// 创建一个新的Person:
Person satoshi = Person(172, "Satoshi");

// 将新创建的satoshi添加进people数组:
people.push(satoshi);
```

你也可以两步并一步，用一行代码更简洁:

```solidity
people.push(Person(16, "Vitalik"));
```

> _注：_`array.push()` _在数组的_ **_尾部_** _加入新元素 ，所以元素在数组中的顺序就是我们添加的顺序， 如:_

```solidity
uint[] numbers;
numbers.push(5);
numbers.push(10);
numbers.push(15);
// The `numbers` array is now equal to [5, 10, 15]
```

## **私有 / 公共函数**

Solidity 定义的函数的属性默认为`公共`。 这就意味着任何一方 (或其它合约) 都可以调用你合约里的函数。

显然，不是什么时候都需要这样，而且这样的合约易于受到攻击。 所以将自己的函数定义为`私有`是一个好的编程习惯，只有当你需要外部世界调用它时才将它设置为`公共`。

如何定义一个私有的函数呢？

```solidity
uint[] numbers;

function _addToArray(uint _number) private{
  numbers.push(_number);
}
```

这意味着只有我们合约中的其它函数才能够调用这个函数，给 `numbers` 数组添加新成员。

> 可以看到，在函数名字后面使用关键字 `private` 即可。和函数的参数类似，私有函数的名字用(`_`)起始。

## **返回值**

要想函数返回一个数值，按如下定义：

```solidity
string greeting = "What's up dog";

function sayHello() public returns (string){
  return greeting;
}
```

Solidity 里，函数的定义里可包含返回值的数据类型(如本例中 `string`)。

## **函数的修饰符**

上面的函数实际上没有改变 Solidity 里的状态，即，它没有改变任何值或者写任何东西。

这种情况下我们可以把函数定义为 **_view_**, 意味着它只能读取数据不能更改数据:

```solidity
function sayHello() public view returns (string){
```

Solidity 还支持 **_pure_** 函数, 表明这个函数甚至都不访问应用里的数据，例如：

```solidity
function _multiply(uint a, uint b) private pure returns (uint){
  return a * b;
}
```

这个函数甚至都不读取应用里的状态 — 它的返回值完全取决于它的输入参数，在这种情况下我们把函数定义为 **_pure_**.

> _注：可能很难记住何时把函数标记为 pure/view。 幸运的是， Solidity 编辑器会给出提示，提醒你使用这些修饰符。_

## **Keccak256**

如何让 `_generateRandomDna` 函数返回一个全(半) 随机的 `uint`?

Ethereum 内部有一个散列函数`keccak256`，它用了SHA3版本。一个散列函数基本上就是**把一个字符串转换为一个256位的16进制数字**。字符串的一个微小变化会引起散列数据极大变化。

这在 Ethereum 中有很多应用，但是现在我们只是用它造一个伪随机数。

例子:

```
//6e91ec6b618bb462a4a6ee5aa2cb0e9cf30f7a052bb467b0ba58b8748c00d2e5
keccak256("aaaab");
//b1f078126895a1424524de5321b339ab00408010b7cf0e6ed451514981e58aa9
keccak256("aaaac");
```

显而易见，输入字符串只改变了一个字母，输出就已经天壤之别了。

> _注: 在区块链中安全地产生一个随机数是一个很难的问题， 本例的方法不安全，但是在我们的Zombie DNA算法里不是那么重要，已经很好地满足我们的需要了。_

## **类型转换**

有时你需要变换数据类型。例如:

```solidity
uint8 a = 5;
uint b = 6;
// 将会抛出错误，因为 a * b 返回 uint, 而不是 uint8:
uint8 c = a * b;
// 我们需要将 b 转换为 uint8:
uint8 c = a * uint8(b);
```

上面, `a * b` 返回类型是 `uint`, 但是当我们尝试用 `uint8` 类型接收时, 就会造成潜在的错误。如果把它的数据类型转换为 `uint8`, 就可以了，编译器也不会出错。

## **事件**

我们的合约几乎就要完成了！让我们加上一个**事件**.

**事件** 是合约和区块链通讯的一种机制。你的前端应用“监听”某些事件，并做出反应。

例子:

```solidity
// 这里建立事件
event IntegersAdded(uint x, uint y, uint result);

function add(uint _x, uint _y) public{
  uint result = _x + _y;
  //触发事件，通知app
  IntegersAdded(_x, _y, result);
  return result;
}
```

你的 app 前端可以监听这个事件。JavaScript 实现如下:

```solidity
YourContract.IntegersAdded(function(error, result){
  // 干些事
})
```

<aside> 💡

存疑：{ } 外不需 ；

</aside>

## **Addresses （地址）**

以太坊区块链由  **_account_**  (账户)组成，你可以把它想象成银行账户。一个帐户的余额是 **_以太_** （在以太坊区块链上使用的币种），你可以和其他帐户之间支付和接受以太币，就像你的银行帐户可以电汇资金到其他银行帐户一样。

每个帐户都有一个“地址”，你可以把它想象成银行账号。这是账户唯一的标识符，它看起来长这样：

`0x0cE446255506E92DF41614C46F1d6df9Cc969183`

**地址属于特定用户（或智能合约）**

## **Mapping（映射）**

**除了  _结构体_**  和  **_数组_  ，** **_映射_** 是另一种在 Solidity 中存储有组织数据的方法。

映射是这样定义的：

```solidity
//对于金融应用程序，将用户的余额保存在一个 uint类型的变量中：
mapping (address => uint) public accountBalance;
//或者可以用来通过userId 存储/查找的用户名
mapping (uint => string) userIdToName;
```

映射本质上是存储和查找数据所用的键-值对。在第一个例子中，键是一个 `address`，值是一个 `uint`，在第二个例子中，键是一个`uint`，值是一个 `string`。

## **msg.sender**

在 Solidity 中，有一些全局变量可以被所有函数调用。 其中一个就是 `msg.sender`，它指的是当前调用者（或智能合约）的 `address`。

> _注意：在 Solidity 中，功能执行始终需要从外部调用者开始。 一个合约只会在区块链上什么也不做，除非有人调用其中的函数。所以_ `msg.sender`_总是存在的。_

以下是使用 `msg.sender` 来更新 `mapping` 的例子：

```solidity
mapping (address => uint) favoriteNumber;

function setMyNumber(uint _myNumber) public{
  // 更新我们的 `favoriteNumber` 映射来将 `_myNumber`存储在 `msg.sender`名下
  favoriteNumber[msg.sender] = _myNumber;
  // 存储数据至映射的方法和将数据存储在数组相似
}

function whatIsMyNumber() public view returns (uint){
  // 拿到存储在调用者地址名下的值
  // 若调用者还没调用 setMyNumber， 则值为 `0`
  return favoriteNumber[msg.sender];
}
```

在这个小小的例子中，任何人都可以调用 `setMyNumber` 在我们的合约中存下一个 `uint` 并且与他们的地址相绑定。 然后，他们调用 `whatIsMyNumber` 就会返回他们存储的 `uint`。

使用 `msg.sender` 很安全，因为它具有以太坊区块链的安全保障 —— 除非窃取与以太坊地址相关联的私钥，否则是没有办法修改其他人的数据的。

跟在 JavaScript 中一样， 在 Solidity 中你也可以用 `++` 使 `uint` 递增。

```solidity
uint number = 0;
number++;
// `number` 现在是 `1`了
```

_注意：在 Solidity 中，关键词放置的顺序并不重要_

# **Require**

在 `require`使得函数在执行过程中，当不满足某些条件时抛出错误，并停止执行：

```solidity
function sayHiToVitalik(string _name) public returns (string){
  // 比较 _name 是否等于 "Vitalik". 如果不成立，抛出异常并终止程序
  // (敲黑板: Solidity 并不支持原生的字符串比较, 我们只能通过比较
  // 两字符串的 keccak256 哈希值来进行判断)
  require(keccak256(_name) == keccak256("Vitalik"));
  // 如果返回 true, 运行如下语句
  return "Hi!";
}
```

如果你这样调用函数 `sayHiToVitalik（“Vitalik”）` ,它会返回“Hi！”。而如果调用的时候使用了其他参数，它则会抛出错误并停止执行。

因此，在调用一个函数之前，用 `require` 验证前置条件是非常有必要的。

## **继承（Inheritance）**

有个让 Solidity 的代码易于管理的功能，就是合约 **_inheritance_** (继承)：

```solidity
contract Doge{
  function catchphrase() public returns (string){
    return "So Wow CryptoDoge";
  }
}

contract BabyDoge is Doge{
  function anotherCatchphrase() public returns (string){
    return "Such Moon BabyDoge";
  }
}
```

由于 `BabyDoge` 是从 `Doge` 那里 **_inherits_** （继承)过来的。 这意味着当你编译和部署了 `BabyDoge`，它将可以访问 `catchphrase()` 和 `anotherCatchphrase()`和其他我们在 `Doge` 中定义的其他**公共函数**。

这可以用于逻辑继承（比如表达子类的时候，`Cat` 是一种 `Animal`）。 但也可以简单地将类似的逻辑组合到不同的合约中以组织代码。

## **引入（Import）**

Solidity 中，当你有多个文件并且想把一个文件导入另一个文件时，可以使用 `import` 语句：

```
import "./someothercontract.sol";

contract newContract is SomeOtherContract {

}
```

这样当我们在合约（contract）目录下有一个名为 `someothercontract.sol` 的文件（ `./` 就是同一目录的意思），它就会被编译器导入。
<!-- DAILY_CHECKIN_2026-03-12_END -->

# 2026-03-11
<!-- DAILY_CHECKIN_2026-03-11_START -->

“崩溃😩，满课的1天根本多少事件看，，，明天只有一节早八😋好好勤能补拙一下”

# solidity语法基础回顾（啃basic demo在回顾一下吧

## **版本指令**

所有的 Solidity 源码都必须冠以 "version pragma" — 标明 Solidity 编译器的版本. 以避免将来新的编译器可能破坏你的代码。

例如: `pragma solidity ^0.4.19;` (当前 Solidity 的最新版本是 0.4.19).

综上所述， 下面就是一个最基本的合约 — 每次建立一个新的项目时的第一段代码:

```
pragma solidity ^0.4.19;

contract HelloWorld {

}
```

## **状态变量和整数**

**_状态变量_**是被永久地保存在合约中。也就是说它们被写入以太币区块链中. 想象成写入一个数据库。

### 例子:

```solidity
contract Example{
  // 这个无符号整数将会永久的被保存在区块链中
  uint myUnsignedInteger = 100;
}
```

在上面的例子中，定义 `myUnsignedInteger` 为 `uint` 类型，并赋值100。

## **数学运算**

在 Solidity 中，数学运算很直观明了，与其它程序设计语言相同:

-   加法: `x + y`
    
-   减法: `x - y`,
    
-   乘法: `x * y`
    
-   除法: `x / y`
    
-   取模 / 求余: `x % y` _(例如,_ `13 % 5` _余_ `3`_, 因为13除以5，余3)_
    

Solidity 还支持 **_乘方操作_** (如：x 的 y次方） // 例如： 5 \*\* 2 = 25

```
uint x = 5 ** 2; // equal to 5^2 = 25
```

## **结构体**

有时你需要更复杂的数据类型，Solidity 提供了 **结构体**:

```csharp
struct Person {
  uint age;
  string name;
}
```

结构体允许你生成一个更复杂的数据类型，它有多个属性。

> _注：我们刚刚引进了一个新类型,_ `string`_。 字符串用于保存任意长度的 UTF-8 编码数据。 如：_ `string greeting = "Hello world!"`_。_

## **数组**

如果你想建立一个集合，可以用 **_数组_这样的数据类型. Solidity 支持两种数组: _静态_ 数组和_动态_** 数组:

```
// 固定长度为2的uint类型静态数组:
uint[2] fixedArray;
// 固定长度为5的string类型的静态数组:
string[5] stringArray;
// uint类型动态数组，长度不固定，可以动态添加元素:
uint[] dynamicArray;
```

你也可以建立一个 **_结构体_**类型的数组 例如，上一章提到的 `Person`:

```
Person[] people; // 这是类型为结构体（或者说Person）的名为people的动态数组，可以不断添加元素
```

记住：状态变量被永久保存在区块链中。所以在你的合约中创建动态数组来保存成结构的数据是非常有意义的。

## **公共数组**

你可以定义 `public` 数组, Solidity 会自动创建 **_getter_** 方法. 语法如下:

```
Person[] public people;
```

其它的合约可以从这个数组读取数据（但不能写入数据），所以这在合约中是一个有用的保存公共数据的模式。

## **定义函数**

在 Solidity 中函数定义的句法如下:

```
function eatHamburgers(string _name, uint _amount) {

}//函数 eatHamburgers 有两个参数，分别是 string 类型的 _name 和 unit 类型的 _amount
```

这是一个名为 `eatHamburgers` 的函数，它接受两个参数：一个 `string`类型的 和 一个 `uint`类型的。现在函数内部还是空的。

> _注：: 习惯上函数里的变量都是以(_`_`_)开头 (但不是硬性规定) 以区别全局变量。我们整个教程都会沿用这个习惯。_

我们的函数定义如下:

```
eatHamburgers("vitalik", 100);
```

## **创建新的结构体**

还记得上个例子中的 `Person` 结构体：

```csharp
struct Person {
  uint age;
  string name;
}

Person[] public people;
```

现在创建新的 `Person` 结构，然后把它加入到名为 `people` 的数组中.

```solidity
// 创建一个新的Person:
Person satoshi = Person(172, "Satoshi");

// 将新创建的satoshi添加进people数组:
people.push(satoshi);
```

你也可以两步并一步，用一行代码更简洁:

```solidity
people.push(Person(16, "Vitalik"));
```

> _注：_`array.push()` _在数组的_ **_尾部_** _加入新元素 ，所以元素在数组中的顺序就是我们添加的顺序， 如:_

```solidity
uint[] numbers;
numbers.push(5);
numbers.push(10);
numbers.push(15);
// The `numbers` array is now equal to [5, 10, 15]
```

## **私有 / 公共函数**

Solidity 定义的函数的属性默认为`公共`。 这就意味着任何一方 (或其它合约) 都可以调用你合约里的函数。

显然，不是什么时候都需要这样，而且这样的合约易于受到攻击。 所以将自己的函数定义为`私有`是一个好的编程习惯，只有当你需要外部世界调用它时才将它设置为`公共`。

如何定义一个私有的函数呢？

```solidity
uint[] numbers;

function _addToArray(uint _number) private{
  numbers.push(_number);
}
```

这意味着只有我们合约中的其它函数才能够调用这个函数，给 `numbers` 数组添加新成员。

> 可以看到，在函数名字后面使用关键字 `private` 即可。和函数的参数类似，私有函数的名字用(`_`)起始。

<aside> 💡

存疑：{ } 外不需 ；

</aside>
<!-- DAILY_CHECKIN_2026-03-11_END -->

# 2026-03-10
<!-- DAILY_CHECKIN_2026-03-10_START -->


# 导论

> # **Why Reactive Contracts 为什么选择反应式合同**
> 
> In the Ethereum world, smart contracts have revolutionized how we conceive of executing trustless agreements. Traditionally, these contracts spring into action only upon a user-initiated transaction. This presents inherent limitations. For one, smart contracts can't autonomously initiate actions or respond to blockchain events without an external prompt — either from a user or an automated script like a trading bot. This requires holding private keys and introducing a centralized point of control.在以太坊世界中，智能合约彻底改变了我们对执行无信任协议的认知。传统上，这些合同只有在用户发起的交易时才会生效。这带来了固有的局限性。首先，智能合约无法在没有外部提示的情况下自主发起动作或响应区块链事件——无论是来自用户还是像交易机器人这样的自动脚本。这需要持有私钥并引入集中控制点。
> 
> Reactive Contracts (RCs) emerge as a solution to this constraint. RCs are designed to autonomously react to events in the Ethereum Virtual Machine (EVM) and trigger subsequent actions across the blockchain ecosystem. This capability for the implementation of complex logic that can source information from multiple chains and enact changes or transactions across various platforms without a central oversight.反应式合约（RCs）作为解决这一限制的方案而出现。RCs 设计为自主响应以太坊虚拟机（EVM）中的事件，并触发区块链生态系统内的后续作。这种能力实现了复杂逻辑，能够从多条链中获取信息，并在不同平台上执行变更或交易，而无需中央监督

1.  智能合约是什么
    

以前做交易需要**信任第三方**

比如：

-   信任银行
    
-   信任中介
    
-   信任律师
    

通过第三方的背书完成交易

**智能合约可以自动执行规则（执行无信任协议）**。

比如：

写一段代码

```
if 如果 A 给钱 : 
	就自动把 NFT 给 A
```

整个过程：

-   没人能作弊
    
-   不需要中间人
    
-   代码自动执行
    

1.  智能合约的局限
    

智能合约是非自动的，需要**靠用户/机器人 bot 调用执行**。

举个例子（Uniswap 交换代币）：

点击 swap → 签名交易 → 发送交易 → 合约才开始运行。如果没有人为触发，合约就一直睡觉。

智能合约的触发，必须有人持有**私钥**，并且产生一个中心化的控制点。

比如： 机器人监听链上事件 → 发现机会 → 用**私钥**发交易。这个机器人控制私钥并决定什么时候触发 = 一个中心控制点。

而这个中心控制点**违背了去中心化**

1.  **Reactive Contracts**
    

**Reactive Contracts，“无需别’人‘触发，会自己反应的合约” =** 监听链上的事件，满足特定条件时，自动对 EVM（以太坊虚拟机）中的事件做出反应。

RCs能从**多条区块链获取信息**，在**不同平台执行**操作或交易

举例：

Reactive Contract 可以同时看很多链（ETH链/BSC/Arbitrum/Polygon）的信息，然后做判断——如果A链价格低、B链价格高，就套利

RCs 不需要中央监管，因此不会造成中心控制点。

RCs 不需要：

-   中心服务器
    
-   执行策略
    
-   管理员
    
-   私钥控制
    

1.  RCs 的优势
    

-   去中心化：RC 在区块链上独立运行，消除集中控制点，通过降低作或失败的风险提升安全性。
    
-   自动化：RC 自动响应链上事件执行智能合约逻辑，减少人工干预需求，实现高效、实时的响应。
    
-   跨链互作性：RC 可以与多个区块链交互，实现复杂的跨链交互，提升多样性并弥合网络间的差距。
    
-   提升效率与功能性： 通过对实时数据的响应，RC 提升了智能合约的效率，支持复杂金融工具、动态 NFT 和创新 DeFi 应用等先进功能。
    
-   DeFi 及其他领域的创新：RC 为 DeFi 及其他区块链应用（如自动化交易和动态治理）带来了新可能，打造了一个更具响应性和互联互通的区块链生态系统。
    

* * *

# lesson 1

> Reactive vs. Traditional Contracts: Unlike traditional smart contracts, RCs autonomously monitor blockchain events and execute actions without user intervention, providing a more dynamic and responsive system. 反应式合约与传统合约： 与传统的智能合约不同，反应式合约能够自主监控区块链事件并执行操作，无需用户干预，从而提供更动态、响应更迅速的系统。
> 
> Inversion of Control: RCs invert the traditional execution model by allowing the contract itself to decide when to execute based on predefined events, eliminating the need for external triggers like bots or users. 控制反转： RC 合约通过允许合约本身根据预定义的事件决定何时执行，从而颠覆了传统的执行模型，消除了对机器人或用户等外部触发器的需求。
> 
> Decentralized Automation: RCs enable fully decentralized operations, automating processes like data collection, DEX trading, and liquidity management without centralized intermediaries. 去中心化自动化： RC 实现完全去中心化的运营，无需中心化的中介机构即可自动执行数据收集、DEX 交易和流动性管理等流程。
> 
> Cross-Chain Interactions: RCs can interact with multiple blockchains and sources, enabling sophisticated use cases like cross-chain arbitrage and multi-oracle data aggregation. 跨链交互： RC 可以与多个区块链和数据源进行交互，从而实现复杂的用例，例如跨链套利和多预言机数据聚合。
> 
> Practical Applications: RCs have diverse applications, including collecting data from oracles, implementing UniSwap stop orders, executing DEX arbitrage, and automatically rebalancing pools across exchanges. 实际应用： RC 有多种应用，包括从预言机收集数据、实施 UniSwap 止损单、执行 DEX 套利以及自动在交易所之间重新平衡资金池。

1.  控制反转（IoC）
    

传统智能合约是：机器人/用户 → 调用合约。依赖机器人/用户

Reactive Contract ：链上事件 → 自动触发合约。事件驱动，合约内部执行。

1.  Reactive Contract 是怎么工作的
    

创建 RC 时，需要先告诉它三件事：

### 1 要监听哪些链

比如

```
Ethereum
Arbitrum
Polygon
```

### 2 要监听哪些合约

例如

```
Uniswap pool
某个借贷协议
某个预言机
```

### 3 要监听哪些事件

例如

```
Swap
Transfer
Loan
Vote
Whale交易
```

然后系统就会：

```
监听事件
↓
事件发生
↓
触发 RC
↓
执行逻辑
↓
发交易
↓
更新**状态**

```

**状态包括：**

-   **记录历史数据**
    
-   **积累数据**
    
-   **根据条件触发操作**
    

1.  Reactive Contract 能做什么
    

## 1 预言机数据整合

RC 可以监听多个预言机：

例如：

```
Chainlink
Pyth
其他oracle
```

然后：

```
取平均价格
↓
执行操作
```

比如：

```
比赛结果 → 自动派奖
```

## 2 Uniswap 自动止损

RC 可以监听：

```
Uniswap swap事件
```

然后计算：

```
当前价格
```

如果价格达到：

```
止损价
```

就自动：

```
执行 swap
```

## 3 DEX 套利

RC 可以：

同时监听多个池子

例如：

```
Uniswap
SushiSwap
Balancer
```

如果发现：

```
价格差
```

就自动：

```
套利交易
```

可以：

-   单链套利（flash loan）
    
-   跨链套利
    

## 4 流动性池自动再平衡

RC 还可以：

监控多个交易所的流动性。

例如：

```
A链池子太多资金
B链池子太少
```

RC 就会：

```
自动转移资金
```

实现：

**自动再平衡 liquidity pools**

* * *
<!-- DAILY_CHECKIN_2026-03-10_END -->
<!-- Content_END -->
