# 异常处理

EVM 处理错误和我们常见的语言（如Java、JavaScript等）不一样，当 EVM 在执行时遇到错误，例如：访问越界的数组，除0等，EVM 会回退（revert）整个交易，当前交易所有调用（包含子调用）所改变的状态都会被撤销，因此不是出现部分状态被修改的情况。

在以太坊上，每个交易都是原子操作，在数据库里事务（transcation）一样，要么保证状态的修改要么全部成功，要么全部失败。

Solidity 有 3 个方法来抛出异常：`require()` 、`assert()`、`revert()`， 我们来逐个介绍。

## require()

`require`函数通常用来在执行逻辑前检查输入或合约状态变量是否满足条件，以及验证外部调用的返回值时候满足条件，在条件不满足时抛出异常。

`require`函数有两个形式：

- `require(bool condition)`：如果条件不满足，则撤销状态更改；
- `require(bool condition, string memory message)`：如果条件不满足，则撤销状态更改，可以提供一个错误消息。

以下是`require` 使用例子：

```solidity
pragma solidity >=0.8.0;

contract testRequire {
    function vote(uint age) public {
        require(age >= 18, "只有18岁以上才卡一投票");
                // ...
    }

    function transferOwnership(address newOwner) public {
        require(owner() == msg.sender, "调用者不是 Owner");
            // ...
    }
    
}
```



`vote()` 函数要求 `age >= 18`（表示在18岁以上才可以投票），否则撤销交易。

`transferOwnership()` 函数要求调用者是`owner()`， 否则撤销交易。

除了代码调用 `require()` 不满足表达式，会抛出异常外，下面这些情况也同样会触发 require 式异常（这类异常称为`Error`）：

- 通过消息调用调用某个函数，但该函数没有正确结束（它耗尽了gas，没有匹配函数，或者本身抛出一个异常）。 但不包括[低级别操作](https://decert.me/tutorial/solidity/solidity-adv/addr_call.md)：call、send、delegatecall、staticcall。低级操作不会抛出异常，而通过返回 false 来指示失败。
- 使用 new 关键字创建合约，但合约创建失败。
- 调用到了一个不存在的外部函数，即 EVM找不到外部函数的代码。
- 向一个没法[接收以太币](https://decert.me/tutorial/solidity/solidity-basic/receive) 的合约`transfer()` ， 或附加Ether 调用没有 payable修饰符的函数。

当 require 式异常发生时，EVM 使用 `REVERT` 操作码回滚交易，剩余未使用的 Gas 将返回给交易发起者。

## assert()

`assert(bool condition))` 函数通常用来检查内部逻辑，assert 总是假定程序满足条件检查（假定`condition`为true），否则说明程序出现了一个未知的错误，如果正确使用`assert()`函数，Solidity 分析工具（如 STMChecker 工具）可以帮我们分析出智能合约中的错误。

以下是`assert` 使用例子：

```solidity
pragma solidity >=0.8.0 ;

contract testAsset{
    bool public inited;

    function checkInitValue() internal  {
        // inited 应该永远为false
        assert(!inited);
        // 其他的逻辑...
    }
}
```



除了代码调用 `assert()` 不满足表达式，会抛出异常外，下面这些情况也同样会触发 assert 式异常（这类异常称为`Panic`）：

- 访问数组的索引太大或为负数（例如x[i]其中的i >= x.length或i < 0）。
- 访问固定长度bytesN的索引太大或为负数。
- 用零当除数做除法或模运算（例如 5 / 0 或 23 % 0 ）。
- 移位负数位。
- 将一个太大或负数值转换为一个枚举类型。
- 调用未初始化的内部函数类型变量。

在 0.8.0 版本之前，当 assert 式异常发生时，EVM 会触发 `invalid` 操作码，同时会消耗掉素有未使用的 Gas 。

在 0.8.0 及之后版本，当 assert 式异常发生时，EVM 会使用 `REVERT` 操作码回滚交易，剩余未使用的 Gas 将返回给交易发起者。

## require() 还是 assert()

以下是一些关于使用 `require` 还是 `assert` 的经验总结。

这些情况优先使用`require()`：

（1）用于检查用户输入。

（2）用于检查合约调用返回值，如`require(external.send(amount))`。

（3）用于检查状态，如`msg.send == owner`。

（4）通常用于函数的开头。

（5）不知道使用哪一个的时候，就使用require。

这些情况优先使用`assert()`：

（1）用于检查溢出错误，如`z = x + y ; assert(z >= x);`。

（2）用于检查不应该发生的异常情况。

（3）用于在状态改变之后，检查合约状态。

（4）尽量少使用assert。

（5）通常用于函数中间或结尾。

## revert()

也可以直接调用 `revert()` 来撤销交易，和`require()` 非常类似， revert 有两种形式：

- `revert CustomError(arg1, arg2); `: 回退交易，并抛出一个自定义错误（从 0.8.4 开始新增的语法）。
- `revert()` / `revert(string memory reason)`：回退交易，可选择提供一个解释性的字符串。

推荐使用第一种形式，自定义错误的方式来触发，因为只需要使用 4 个字节的编码就可以描述错误，比较使用解释性的字符串消耗更少的GAS。

```solidity
pragma solidity ^0.8.4;

contract testRevert() {
  public owner;
  error NotOwner();
    
  function transferOwnership(address newOwner) public {
     if(owner != msg.sender)  revert NotOwner();
     owner = newOwner;
  }

}
```

`require()` 和 `revert()` 在功能上其实是等价的，例如，以下两个写法在功能上一样：

```solidity
if(msg.sender != owner) { revert NotOwner(); }
require(msg.sender == owner, "调用者不是 Owner");
```

但使用自定义错误消耗的 Gas 更低。

## 对比总结

| 函数        | 适用场景                   | Gas 处理         | 可恢复性          | 是否支持自定义消息 |
| ----------- | -------------------------- | ---------------- | ----------------- | ------------------ |
| `require()` | 输入校验、权限检查等       | 剩余 Gas 退还    | ✅（可恢复）       | ✅                  |
| `assert()`  | 内部逻辑检查、不可恢复错误 | **消耗所有 Gas** | ❌（表示代码 bug） | ❌                  |
| `revert()`  | 复杂错误处理、手动触发错误 | 剩余 Gas 退还    | ✅（可恢复）       | ✅                  |

## 选择建议

- **输入检查、权限控制** ➝ `require()`
- **确保代码逻辑正确** ➝ `assert()`
- **复杂错误处理** ➝ `revert()`
