# 合约的转账

## 1、合约的转入与转出

### 1.1、从合约转出

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract CallerAddress {
		//...
    function transferToCaller() public {
        address payable callerAddress = payable(msg.sender);
        uint amount = 100 ether;

        // 确保合约中有足够余额
        require(address(this).balance >= amount, "Insufficient balance in the contract");

        callerAddress.transfer(amount);
    }
}
```

1. 使用 `payable` 将调用者的地址强行转换成  `address payable` 类型。
2. 使用 `callerAddress.transfer(amount)` 方法，表示将合约的余额转到 `callerAddress` 里面。
3. 使用 `address(this).balance` 直接获取当前合约地址的余额，包括用户地址和合约地址。

### 1.2、通过 payable 方法转账到合约

```solidity
contract Bank {
		//...
    function deposit() public payable {
        uint newValue = balanceMap[msg.sender] + msg.value;
        balanceMap[msg.sender] = newValue;
    }
    // ...
}

```

1. 直接使用 `payable` 修饰的方法，就可以接受来自调用方的转账。
2. 转账的数额为 `msg.value` ，发起方自然是使用 `msg.sender` 获取。

### 1.3、直接转账到合约

上面的 payable 方法，虽然可以增加 contract 的余额，但是属于主动的转入，而不是被动的接收 ETH。如果需要被动的接收 ETH（随意接收而不是通过 payable 方法），那么需要在方法里面实现 receive 或者 fallback 方法。



## 2、合约如何接收

`receive` 函数和 `fallback` 函数都是在转账时被动调用的，通常称为：**回调函数**。

### 2.1、receive 函数

```solidity
pragma solidity ^0.8.0;

contract Example {
    event Received(address sender, uint amount);

    // receive 函数：专门用于接收 ETH
    receive() external payable {
        emit Received(msg.sender, msg.value);
    }
}
```

在 Solidity 中，`receive` 是一个特殊的回退函数（Fallback Function），用于接收以太币（Ether）。它的主要作用是：

- 当合约接收到以太币但没有提供 `msg.data` 时触发。
- 不能有参数，也不能有返回值。
- 必须声明为 `external` 且 `payable`，否则无法接收 ETH。

### 2.2、fallback 函数

`fallback` 是 Solidity 中的特殊函数，**当合约接收到调用时，且该调用不匹配任何已定义的函数时，`fallback` 函数就会被执行**。

同时，在合约没有定义 receive 函数的时候，那么就会尝试使用 fallback 函数来处理转账方法，如果 fallback 函数使用了 payable 进行修饰，那么也可以完成这一笔转账。

```solidity
pragma solidity ^0.8.0;

contract Example {
    event FallbackTriggered(address sender, uint amount);

    // 只定义 fallback，没有 receive，但 fallback 是 payable
    fallback() external payable {
        emit FallbackTriggered(msg.sender, msg.value);
    }
}
```

### 2.3、区别

| 交易情况                | `receive()` 存在  | `fallback()` `payable` 存在 | 触发函数                    |
| ----------------------- | ----------------- | --------------------------- | --------------------------- |
| `msg.data` 为空，转 ETH | ✅ 触发 `receive`  | ✅ 触发 `fallback`           | `receive()` 或 `fallback()` |
| `msg.data` 为空，转 ETH | ❌ 没有 `receive`  | ✅ 触发 `fallback`           | `fallback()`                |
| `msg.data` 为空，转 ETH | ❌ 没有 `receive`  | ❌ 没有 `fallback`           | 交易失败                    |
| `msg.data` 不为空       | ✅ 触发 `fallback` | ✅ 触发 `fallback`           | `fallback()`                |
| `msg.data` 不为空       | ❌ 没有 `fallback` | ❌ 没有 `receive`            | 交易失败                    |

| 对比项               | `receive()`               | `fallback()`       |
| -------------------- | ------------------------- | ------------------ |
| `payable` 是否必须   | 是                        | 可选               |
| 何时触发             | `msg.data` 为空，发送 ETH | 未找到匹配的函数   |
| 是否可以不接收 ETH   | ❌ 不行，必须 `payable`    | ✅ 可以不 `payable` |
| 是否可以处理函数调用 | ❌ 不能                    | ✅ 可以             |

<img src="../../../img/receive.png" alt="abc" style="zoom:50%;" />
