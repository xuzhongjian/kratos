# 创建

在 Solidity 中，`create` 和 `create2` 都用于部署合约，但它们在合约地址的计算方式上有区别。

## `create` 语法

```solidity
address newContract = new ContractName{value: msg.value}();
```

或者使用 `assembly`：

```solidity
assembly {
    let newContract := create(value, add(bytecode, 0x20), mload(bytecode))
}
```

- `value`：要发送的 ETH 数量（可选）。
- `bytecode`：要部署的合约字节码。

### 特点

- `create` 生成的合约地址取决于**部署者地址**和**nonce**： $address = keccak256(rlp(sender, nonce))$
- **无法提前预测地址**，因为 nonce 会随交易次数变化。

------

## `create2` 语法

```solidity
assembly {
    let newContract := create2(value, add(bytecode, 0x20), mload(bytecode), salt)
}
```

- `value`：要发送的 ETH 数量（可选）。
- `bytecode`：要部署的合约字节码。
- `salt`：一个 32 字节的任意值，影响地址计算。

### 特点

- `create2` 生成的合约地址取决于**部署者地址**、**salt** 和 **合约字节码**： $address = keccak256(0xff + sender + salt + keccak256(bytecode))$
- **可以提前预测地址**，只要 sender、salt 和 bytecode 不变，地址就固定。
- **可以用于合约预部署**（如合约钱包、代理合约等）。

### `create2` 的非 `assembly` 方式

```solidity
pragma solidity ^0.8.0;

contract Factory {
    event ContractDeployed(address contractAddress);

    function deploy(bytes32 _salt) external {
        DeployedContract newContract = new DeployedContract{salt: _salt}();
        emit ContractDeployed(address(newContract));
    }
}

contract DeployedContract {
    uint256 public value;

    constructor() {
        value = 100;
    }
}

```

`create2` **可以不使用 assembly**，直接使用 Solidity 提供的 `CREATE2` 版本：`new ContractName{salt: _salt}()`。

------

### 使用哪种方式

| 方式                              | 适用场景                                | 代码可读性  | 灵活性                |
| --------------------------------- | --------------------------------------- | ----------- | --------------------- |
| **`new Contract{salt: _salt}()`** | **静态合约**，代码已知                  | ✅（简单）   | ❌（只能创建已知合约） |
| **`assembly create2`**            | **动态合约部署**，从外部提供 `bytecode` | ❌（较复杂） | ✅（支持自定义字节码） |

👉 **一般情况下**，使用 Solidity 原生的 `new Contract{salt: _salt}()` 方式更安全、直观；
 👉 **如果需要更灵活的合约工厂模式**，如从 bytecode 生成合约，则需要 `assembly create2`。

## 总结

| 方法      | 地址计算方式                                            | 可预测地址 | 主要用途                       |
| --------- | ------------------------------------------------------- | ---------- | ------------------------------ |
| `create`  | `keccak256(sender, nonce)`                              | ❌          | 一般合约部署                   |
| `create2` | `keccak256(0xff + sender + salt + keccak256(bytecode))` | ✅          | 可预测地址、合约钱包、代理合约 |

如果你要创建一个固定地址的合约，比如在 Layer 2 预部署合约，推荐使用 `create2`。