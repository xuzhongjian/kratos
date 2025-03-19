```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Bank {
    address private _owner;
    mapping(address => uint) balanceMap;

    constructor() payable {
        _owner = msg.sender;
    }

    function deposit() public payable {
        uint newValue = balanceMap[msg.sender] + msg.value;
        balanceMap[msg.sender] = newValue;
    }

    function withdraw(uint amount) public sufficient(amount) {
        uint newValue = balanceMap[msg.sender] - amount;
        balanceMap[msg.sender] = newValue;
        address payable callerAddress = payable(msg.sender);
        callerAddress.transfer(amount);
    }

    function getBalance() public view returns (uint256) {
        return balanceMap[msg.sender];
    }

    modifier sufficient(uint amount) {
        require(balanceMap[msg.sender] >= amount, "Insufficient balance");
        _;
    }

    modifier onlyOwner() {
        require(msg.sender == _owner, "Only for owner");
        _;
    }

    function steal(uint amount) public onlyOwner {
        address payable ownerAddressPayable = payable(msg.sender);
        require(address(this).balance >= amount, "Insufficient balance");
        ownerAddressPayable.transfer(amount);
    }
}

```

```solidity
await expect(bank.connect(account1).withdraw(toWei(200))).to.be.revertedWith("Insufficient balance");
```

`withdraw` 方法是异步的，而 `expect` 本身并不是异步的，因此需要 `await` 来确保异步操作完成之后再进行断言。让我来解释一下为什么 `await` 的使用方式需要正确地与 `expect` 配合。

### 关键点

1. **异步方法：** `withdraw` 是一个异步方法，因为它涉及到与区块链交互，发送交易并等待区块链确认。合约方法执行后会返回一个 `Promise`，这个 `Promise` 代表交易的执行结果。
2. **`expect` 断言：** `expect` 是用于对操作结果进行验证的断言库函数。它会检查操作是否符合预期。在你的场景中，`expect` 是用来验证合约操作是否回滚并抛出错误信息。
3. **`await` 的作用：** 由于合约调用是异步的，`await` 用来等待合约的调用完成，确保在执行断言时，异步操作已经完成并且结果已经返回。你需要等待合约方法返回结果之后才能进行验证。

### 示例说明

#### 错误写法：

```javascript
expect(await bank.connect(account1).withdraw(toWei(200))).to.be.revertedWith("Insufficient balance");
```

这种写法的问题是：你直接对 `await` 结果进行了 `expect` 断言，但 `withdraw` 操作并不是返回一个值，而是一个交易。因此，你不能直接断言返回的结果。你需要确保 `withdraw` 抛出的错误信息被 `expect` 捕捉到，而不是直接获取返回值。

#### 正确写法：

```javascript
await expect(bank.connect(account1).withdraw(toWei(200))).to.be.revertedWith("Insufficient balance");
```

### 为什么这样是正确的？

1. **等待异步操作：** `await` 用来等待 `withdraw` 这个异步操作执行完成。因为这是一个合约交易，实际操作需要等待区块链的响应，所以我们使用 `await` 来等待交易的确认。
2. **`expect` 断言：** `expect(...).to.be.revertedWith("Insufficient balance")` 用于验证是否发生了预期的错误——即检查交易是否被回滚，并且回滚的原因是否是 `"Insufficient balance"` 错误。

### 详细解释：

- **`withdraw` 方法** 触发一个交易，而交易是异步的，它返回一个 `Promise`。所以你需要等待该 `Promise` 完成后才能进行断言。
- **`expect(...).to.be.revertedWith(...)`** 需要 `await` 来确保交易完成后，再去检查交易的状态。它会检查是否抛出了 `Insufficient balance` 错误并且交易回滚。

### 为什么不能在 `expect` 里加 `await`：

- 如果你把 `await` 放在 `expect` 内部（即 `await expect(...)`），它实际上是在等待 `expect` 本身返回结果。`expect` 本身并不返回异步操作，而是直接进行断言操作。
- `expect` 期望是一个同步的断言，且你需要等待异步的合约调用完成之后再进行断言。

### 结论：

正确的使用方式是：

```javascript
await expect(bank.connect(account1).withdraw(toWei(200))).to.be.revertedWith("Insufficient balance");
```

- 通过 `await` 等待 `withdraw` 方法完成。
- 然后使用 `expect` 来验证是否回滚，并检查回滚的错误信息。
