# 合约接收转账

## 1、从合约转出

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
