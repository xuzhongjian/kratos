# 关键字

Solidity 关键字（keywords）是编写智能合约时的保留字，它们具有特定的功能，不能作为标识符（变量名、函数名等）。以下是 Solidity 的主要关键字，按类别分类：

------

## **1. 版本声明**

- `pragma` —— 指定编译器版本，例如 `pragma solidity ^0.8.0;`
- `import` —— 引入其他 Solidity 文件，例如 `import "./MyLibrary.sol";`

------

## **2. 变量类型**

- `uint` / `uint8` / `uint256` —— 无符号整数
- `int` / `int8` / `int256` —— 有符号整数
- `bool` —— 布尔值（`true` 或 `false`）
- `address` —— 以太坊地址（可用于收发 ETH）
- `bytes` / `bytes32` / `bytes1` —— 字节数组
- `string` —— 字符串
- `mapping` —— 映射（键值对）
- `struct` —— 结构体
- `enum` —— 枚举类型
- `array`（数组）—— 例如 `uint[] myArray;`

------

## **3. 访问控制**

- `public` —— 任何人都可以访问
- `private` —— 仅合约内部访问
- `internal` —— 仅当前合约和继承的合约可访问
- `external` —— 仅外部合约或账户可调用

------

## **4. 合约相关**

- `contract` —— 定义一个合约
- `interface` —— 定义一个接口
- `library` —— 定义一个库（library）
- `abstract` —— 定义一个抽象合约
- `is` —— 继承合约，例如 `contract Child is Parent`

------

## **5. 函数关键字**

- `function` —— 定义函数
- `modifier` —— 定义修饰符
- `constructor` —— 构造函数（合约部署时执行）
- `receive` —— 处理 ETH 交易
- `fallback` —— 备用函数（接收未知调用）

------

## **6. 变量 & 状态控制**

- `storage` —— 存储变量（链上存储）
- `memory` —— 临时变量（仅在函数执行时生效）
- `calldata` —— 只读参数（适用于 `external` 函数）
- `constant` / `immutable` —— 定义常量
- `payable` —— 允许函数接受 ETH
- `view` —— 只读函数，不修改状态
- `pure` —— 纯函数，不读取或修改状态
- `delete` —— 删除变量，例如 `delete myVar;`

------

## **7. 事件 & 日志**

- `event` —— 定义事件
- `emit` —— 触发事件，例如 `emit MyEvent(msg.sender, value);`

------

## **8. 错误处理**

- `require` —— 失败时回滚并返回错误信息
- `assert` —— 仅用于调试，失败时回滚（通常用于不应该失败的检查）
- `revert` —— 手动回滚事务，例如 `revert("Error message");`
- `try` / `catch` —— 处理外部调用错误

------

## **9. 继承 & 可升级**

- `override` —— 重写父合约的方法
- `virtual` —— 允许子合约重写函数
- `super` —— 调用父合约的函数

------

## **10. 其他**

- `this` —— 当前合约的地址
- `msg.sender` —— 发送交易的人
- `msg.value` —— 交易附带的 ETH 数量
- `block.timestamp` —— 当前区块的时间戳
- `block.number` —— 当前区块号
- `tx.origin` —— 原始交易发送者
- `keccak256` —— 哈希函数，例如 `keccak256(abi.encodePacked(value));`
- `type` —— 获取类型信息，例如 `type(MyContract).creationCode`

------

### **总结**

Solidity 关键字大致分为： ✅ **变量类型**（`uint`, `bool`, `address`, `string`）
 ✅ **访问控制**（`public`, `private`, `internal`, `external`）
 ✅ **函数相关**（`function`, `modifier`, `constructor`, `fallback`）
 ✅ **存储相关**（`storage`, `memory`, `calldata`）
 ✅ **事件与错误处理**（`event`, `emit`, `require`, `assert`, `revert`）

这些关键字是 Solidity 编程的基础，掌握它们可以更高效地编写智能合约 