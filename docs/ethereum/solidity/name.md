# Solidity 规则

### 基本命名规则

#### 1. 字符范围
命名只能使用字母（大小写均可）、数字、下划线（`_`），但不能以数字开头。以下是一些合法和不合法命名的示例：

```solidity
// 合法命名
uint256 myVariable;
function myFunction() public {}

// 不合法命名，以数字开头
// uint256 123Variable; 

// 不合法命名，包含特殊字符（除下划线外）
// function my@Function() public {} 
```

#### 2. 关键字限制
不能使用 Solidity 的关键字作为变量名或方法名。Solidity 的关键字例如 `contract`、`function`、`if`、`else`、`return` 等。以下是错误示例：
```solidity
// 错误，使用了关键字作为变量名
// uint256 contract; 

// 错误，使用了关键字作为函数名
// function return() public {} 
```

### 命名约定
除了基本规则外，还有一些常见的命名约定有助于提高代码的可读性和可维护性。

#### 1. 变量命名
- **普通变量**：通常采用驼峰命名法（Camel Case），即第一个单词首字母小写，后续单词首字母大写。例如：
```solidity
uint256 myVariable;
address myContractAddress;
```
- **常量**：常量通常使用全大写字母，单词间用下划线分隔。例如：
```solidity
uint256 constant MAX_SUPPLY = 1000000;
```
- **私有变量**：以单个下划线开头，用于表示该变量是私有的或内部使用的。例如：
```solidity
uint256 private _privateVariable;
```

#### 2. 方法（函数）命名
- **普通函数**：同样使用驼峰命名法，函数名应具有描述性，能清晰表达函数的功能。例如：
```solidity
function calculateTotalSupply() public view returns (uint256) {
    // 函数体
    return 0;
}
```
- **事件**：事件名通常使用大驼峰命名法（Pascal Case），即每个单词的首字母都大写。例如：
```solidity
event Transfer(address indexed from, address indexed to, uint256 value);
```
- **修饰器**：修饰器名通常使用小写字母，多个单词之间用下划线分隔。例如：
```solidity
modifier onlyOwner() {
    require(msg.sender == owner, "Not owner");
    _;
}
```

### 特殊命名情况
#### 1. 合约命名
合约名使用大驼峰命名法，即每个单词的首字母都大写。例如：
```solidity
contract MyTokenContract {
    // 合约内容
}
```

#### 2. 结构体命名
结构体名也使用大驼峰命名法。例如：
```solidity
struct MyStruct {
    uint256 id;
    string name;
}
```

# 与 Java 的对比

### 命名规则
- **字符范围**
    - **Solidity**：只能使用字母、数字和下划线，且不能以数字开头。
    - **Java**：可以使用字母、数字、下划线和美元符号 `$`，同样不能以数字开头。例如，在Java中 `int $myVariable;` 是合法的，但在Solidity中不允许使用美元符号。
- **关键字限制**
    - **Solidity**：不能使用Solidity的关键字作为变量名或方法名，如 `contract`、`function` 等。
    - **Java**：不能使用Java的关键字，如 `class`、`public`、`void` 等作为变量或方法名。不过，Java和Solidity的关键字大部分是不同的，例如Java中的 `interface` 在Solidity中不是关键字，反之亦然。

### 命名约定
- **变量命名**
    - **Solidity**：普通变量采用驼峰命名法，常量使用全大写字母加下划线分隔，私有变量以单个下划线开头。
    - **Java**：普通变量采用小驼峰命名法；常量通常也是全大写字母加下划线分隔；对于私有变量，通常使用 `private` 关键字修饰，但没有强制要求以下划线开头。例如，在Java中更常见的是 `private int privateVariable;`。
- **方法命名**
    - **Solidity**：普通函数使用驼峰命名法，事件使用大驼峰命名法，修饰器使用小写字母加下划线分隔。
    - **Java**：方法名采用小驼峰命名法。例如，Java中的方法名通常像 `calculateTotal()`，而Solidity中可能是 `calculateTotalSupply()`，Java中没有像Solidity修饰器那样的特殊命名约定。

### 特殊命名情况
- **类型命名**
    - **Solidity**：合约名和结构体名使用大驼峰命名法。
    - **Java**：类名使用大驼峰命名法，与Solidity的合约名和结构体名命名约定类似，但Java中没有合约的概念，结构体在Java中通常用类来表示。

# Solidity 中 `_` 的使用

### 私有变量和函数命名
在 Solidity 里，通常会在私有变量和函数名前加 `_`，以此来表明它们是内部使用的，不应该被外部直接访问。虽然这只是一种命名约定，并非强制规则，但在社区中被广泛遵循。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Example {
    // 私有变量
    uint256 private _privateVariable;

    // 私有函数
    function _privateFunction() private {
        _privateVariable = 10;
    }

    // 公共函数，可以调用私有函数
    function publicFunction() public {
        _privateFunction();
    }
}
```

### 修饰器中的 `_`
在 Solidity 修饰器里，`_` 是一个特殊的占位符，代表被修饰函数的实际代码。当修饰器被应用到函数时，`_` 所在的位置会被替换成被修饰函数的代码。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Example {
    address private _owner;

    constructor() {
        _owner = msg.sender;
    }

    // 修饰器，检查调用者是否为合约所有者
    modifier onlyOwner() {
        require(msg.sender == _owner, "Not the owner");
        _; // 这里会被替换为被修饰函数的代码
    }

    // 使用修饰器的函数
    function restrictedFunction() public onlyOwner {
        // 只有合约所有者可以调用此函数
    }
}
```

### 忽略返回值
在接收多个返回值时，若某个返回值不需要使用，可使用 `_` 来忽略它。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract ReturnExample {
    function returnMultipleValues() public pure returns (uint256, uint256) {
        return (1, 2);
    }

    function useReturnValues() public pure {
        (uint256 value1, _) = returnMultipleValues();
        // 只使用了第一个返回值，忽略了第二个
    }
}
```
