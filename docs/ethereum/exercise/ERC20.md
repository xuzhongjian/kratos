# ERC20 合约解析

ERC20 合约标准是定义在 openzeppelin 的一个标准的以太链的 memecoin 的标准合约。

```solidity
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
```

## 1、合约基本信息

### 1.1、合约签名

```solidity
abstract contract ERC20 is Context, IERC20, IERC20Metadata, IERC20Errors {
	//...
}
```

`ERC20` 继承了 `Context` 和多个接口，但它可能还没有完全实现接口的方法，仍然是抽象合约。

#### 1、Context

```solidity
abstract contract Context {
    function _msgSender() internal view virtual returns (address) {
        return msg.sender;
    }

    function _msgData() internal view virtual returns (bytes calldata) {
        return msg.data;
    }

    function _contextSuffixLength() internal view virtual returns (uint256) {
        return 0;
    }
}
```

提供了一些简便开发的方法，用户获取方法调用者的信息。

#### 2、IERC20

```solidity
// SPDX-License-Identifier: MIT
// OpenZeppelin Contracts (last updated v5.1.0) (token/ERC20/IERC20.sol)
pragma solidity ^0.8.20;

interface IERC20 {
    event Transfer(address indexed from, address indexed to, uint256 value);

    event Approval(address indexed owner, address indexed spender, uint256 value);

    function totalSupply() external view returns (uint256);

    function balanceOf(address account) external view returns (uint256);

    function transfer(address to, uint256 value) external returns (bool);

    function allowance(address owner, address spender) external view returns (uint256);

    function approve(address spender, uint256 value) external returns (bool);

    function transferFrom(address from, address to, uint256 value) external returns (bool);
}

```

`IERC20` 是 ERC-20 标准的接口，定义了代币的核心功能，包括转账、授权、查询余额等。以下是接口中每个方法的详细解读：

------

**1. `totalSupply()`**

```solidity
function totalSupply() external view returns (uint256);
```

- **功能**：返回代币的总供应量（总发行量）。
- **修饰符**：
  - `external`：只能被合约外部调用，不能在合约内部直接调用（但可以通过 `this.totalSupply()` 方式调用）。
  - `view`：不改变区块链状态，只读取数据，不消耗 gas（除非在合约内部调用）。
- **返回值**：当前代币的总供应量（`uint256` 类型）。

------

**2. `balanceOf(address account)`**

```solidity
function balanceOf(address account) external view returns (uint256);
```

- **功能**：返回 `account` 地址当前持有的代币数量。
- **参数**：
  - `account`：查询余额的地址。
- **返回值**：`account` 的代币余额（`uint256` 类型）。

------

**3. `transfer(address to, uint256 value)`**

```solidity
function transfer(address to, uint256 value) external returns (bool);
```

- **功能**：从调用者的账户转移 `value` 数量的代币到 `to` 账户。
- **参数**：
  - `to`：接收代币的地址。
  - `value`：要转移的代币数量。
- **返回值**：
  - `true`：转账成功。
  - `false`（理论上不会返回，但可能因 `require` 失败而回滚）。
- **事件**：
  - **`Transfer(from, to, value)`**：成功转账后触发，`from` 是 `msg.sender`（调用者）。

------

**4. `allowance(address owner, address spender)`**

```solidity
function allowance(address owner, address spender) external view returns (uint256);
```

- **功能**：查询 `spender` 还能从 `owner` 账户使用多少代币（剩余授权额度）。
- **参数**：
  - `owner`：授权者的地址。
  - `spender`：被授权可使用 `owner` 代币的地址。
- **返回值**：`spender` 还能使用的代币数量（`uint256` 类型）。

------

**5. `approve(address spender, uint256 value)`**

```solidity
function approve(address spender, uint256 value) external returns (bool);
```

- **功能**：授权 `spender` 可以使用调用者账户中的 `value` 数量的代币。
- **参数**：
  - `spender`：被授权的地址。
  - `value`：授权的代币数量。
- **返回值**：
  - `true`：授权成功。
  - `false`（理论上不会返回，但可能因 `require` 失败而回滚）。
- **事件**：
  - **`Approval(owner, spender, value)`**：成功授权后触发，`owner` 是 `msg.sender`（调用者）。

------

**6. `transferFrom(address from, address to, uint256 value)`**

```solidity
function transferFrom(address from, address to, uint256 value) external returns (bool);
```

- **功能**：使用 `from` 账户的授权额度，向 `to` 账户转移 `value` 数量的代币。
- **参数**：
  - `from`：代币来源地址（必须事先批准 `msg.sender` 具有一定的额度）。
  - `to`：接收代币的地址。
  - `value`：转移的代币数量。
- **返回值**：
  - `true`：转账成功。
  - `false`（理论上不会返回，但可能因 `require` 失败而回滚）。
- **事件**：
  - **`Transfer(from, to, value)`**：成功转账后触发。

| 方法                                      | 作用                 | 备注                               |
| ----------------------------------------- | -------------------- | ---------------------------------- |
| `totalSupply()`                           | 获取代币总供应量     | 只读                               |
| `balanceOf(address)`                      | 获取账户代币余额     | 只读                               |
| `transfer(address, uint256)`              | 直接转账             | 只能从自己账户转                   |
| `allowance(address, address)`             | 查询授权额度         | 只读                               |
| `approve(address, uint256)`               | 允许他人使用自己代币 | 需要 `spender` 进行 `transferFrom` |
| `transferFrom(address, address, uint256)` | 使用授权额度转账     | 需要 `approve` 授权                |

这些方法组成了 ERC-20 代币的核心标准，所有兼容 ERC-20 的代币都必须实现它们。