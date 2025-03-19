# 地址

## 1、数据类型

地址是一个 solidity 的基本数据类型，其数据结构大概拥有这样一些字段：

```json
HardhatEthersSigner {
  _gasLimit: 30000000,
  address: '0x70997970C51812dc3A010C7d01b50e0d17dc79C8',
  provider: HardhatEthersProvider {
    _hardhatProvider: LazyInitializationProviderAdapter {
      _providerFactory: [AsyncFunction (anonymous)],
      _emitter: [EventEmitter],
      _initializingPromise: [Promise],
      provider: [BackwardsCompatibilityProviderAdapter]
    },
    _networkName: 'hardhat',
    _blockListeners: [],
    _transactionHashListeners: Map(0) {},
    _eventListeners: []
  }
}

// 上面是一个 Hardhat 的测试 address
```

其中最重要的是 address 字段，使用 user.address 可以获取这个地址。

## 2、调用者的地址

```solidity
contract testAddr { 
  	address public user;
    function getUserAddress() public {
        user = msg.sender;
    }
}
```

以上面的合约为例，使用 msg.sender 可以获取这个方法的调用者的地址。

## 3、合约部署者的地址

```solidity
contract testAddr { 
		// ...
    function _onlyOwner() internal view {
        require(owner() == msg.sender, "调用者不是 Owner");
        _;
    }
}
```

通过使用 owner() 函数，可以获取当前这个合约的部署者的地址。

## 4、payable 地址

在 Solidity 中，`address` 和 `address payable` 的主要区别在于，`address payable` 允许进行转账操作，如 `transfer` 和 `send`，而 `address` 类型没有这些功能。

### 转换

- 从 `address` 转 `address payable`：
  - 在 Solidity 0.6+ 中，可以使用 `payable(addr)` 将 `address` 转换为 `address payable`。
  - 在 Solidity 0.5 中，需要使用 `address(uint160(addr))` 进行转换。

### 风险

即使可以强制将 `address` 转换为 `address payable`，如果地址对应的是一个合约地址，但这个合约没有实现 `receive()` 或 `fallback()` 函数来接收 ETH，转账操作将会失败。

- **转换不会引发错误**：
   强制转换本身并不会报错，因为在 Solidity 中，`address` 类型本质上是一个指向 Ethereum 地址的指针，它们的格式是兼容的。编译器会允许这种转换。
- **转账时可能失败**：
   **如果你强制转换一个没有 `receive()` 或 `fallback()` 函数的合约地址并尝试转账**，转账将失败，并抛出 `revert` 错误。因为没有可接收 ETH 的方法，转账操作会被回滚。

```solidity
address addr = 0x1234567890abcdef1234567890abcdef12345678;
address payable ap = payable(addr);  // 强制转换，允许向该地址发送ETH

// 如果 addr 是合约地址且没有实现 payable 接收方法
ap.transfer(1 ether);  // 这将失败并抛出 revert 错误
```



