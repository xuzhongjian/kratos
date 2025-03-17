# Hardhat

## 1. 创建 Hardhat 项目

在 `harmonia` 目录下安装 Hardhat：

```sh
npm install --save-dev hardhat
```

初始化 Hardhat 项目：

```sh
npx hardhat
```

如果遇到如下错误：

```sh
Error: Cannot find module '@nomicfoundation/hardhat-toolbox'
```

执行以下命令安装缺失依赖：

```sh
npm install --save-dev @nomicfoundation/hardhat-toolbox
```

然后再次运行：

```sh
npx hardhat
```

## 2. 安装依赖库

```sh
npm install --save-dev @openzeppelin/contracts
```

## 3. 目录结构

```
my-hardhat-project/                    # 项目根目录
├── contracts/                         # 智能合约文件 (.sol)
│   ├── MyContract.sol                 # 主要合约文件
│   └── AnotherContract.sol            # 其他合约文件
├── ignition/                          # 使用 Hardhat Ignition 进行合约部署的目录
│   ├── deployments/                   # 合约部署相关文件
│   └── modules/                       # Ignition 模块文件
│       └── deploy-ignition.js         # 使用 Ignition 方式进行合约部署的脚本
├── scripts/                           # 部署和与合约交互的脚本
│   ├── deploy.js                      # 传统部署脚本，使用 ethers.js
│   └── interact.js                    # 与合约交互的脚本
├── test/                              # 测试代码
│   ├── MyContract.test.js             # 针对 MyContract 合约的测试
│   └── AnotherContract.test.js        # 针对 AnotherContract 合约的测试
├── node_modules/                      # 项目依赖
├── hardhat.config.js                  # Hardhat 配置文件
├── package.json                       # 项目依赖和脚本
├── package-lock.json                  # 依赖锁定文件
└── README.md                          # 项目说明文档
```

## 4. 编写合约

在 `contracts/` 目录下创建 `Counter.sol`：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Counter {
    uint counter;

    constructor() {
        counter = 0;
    }

    function count() public {
        counter += 1;
    }

    function get() public view returns (uint) {
        return counter;
    }
}
```

## 5. 编译合约

```sh
npx hardhat compile
```

## 6. 编写测试用例

在 `test/` 目录下创建 `Counter.js`：

```js
const { ethers } = require("hardhat");
const { expect } = require("chai");

describe("Counter", function () {
    let counter;

    before(async function () {
        const Counter = await ethers.getContractFactory("Counter");
        counter = await Counter.deploy();
        await counter.waitForDeployment();
    });

    it("should initialize to 0", async function () {
        expect(await counter.get()).to.equal(0);
    });

    it("should increment counter", async function () {
        await counter.count();
        expect(await counter.get()).to.equal(1);
    });
});
```

## 7. 运行测试

```sh
npx hardhat test
```
## 8. 配置 Hardhat

在 `hardhat.config.js` 中添加网络配置：

```js
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config();

module.exports = {
  solidity: "0.8.28",
  networks: {
    sepolia: {
      url: "https://ethereum-sepolia.publicnode.com",
      accounts: [process.env.SEPOLIA_PRIVATE_KEY],
      chainId: 11155111,
    },
  },
  etherscan: {
    apiKey: process.env.ETHERSCAN_API_KEY,
  },
};
```

在 `.env` 文件中添加：

```
SEPOLIA_PRIVATE_KEY=0x你的私钥
ETHERSCAN_API_KEY=你的Etherscan API Key
```

## 9. 部署合约

- 如果你只是想快速部署一个简单的合约，可以使用 **Ethers.js** 部署方式，它更加直接。
- 如果你需要更多的控制，或者需要在部署时使用动态的参数（例如 Hardhat Ignition 中的 `unlockTime` 和 `lockedAmount`），或者需要部署多个合约，**Hardhat Ignition** 会更合适。

### 1、Ethers.js

在 `scripts/` 目录下创建 `deploy.js`：

```js
const { ethers } = require("hardhat");

async function main() {
    const Counter = await ethers.getContractFactory("Counter");
    const counter = await Counter.deploy();
    await counter.waitForDeployment();
    console.log("Counter deployed at:", counter.target);
}

main();
```

执行部署：

```sh
npx hardhat run scripts/deploy.js --network sepolia
```

### 2、Hardhat Ignition

或者在 ignition/modules 目录下，写一个 deploy-ignition.js ，使用这种方式进行部署：

```solidity
// 1.  Import the `buildModule` function from the Hardhat Ignition module
const { buildModule } = require("@nomicfoundation/hardhat-ignition/modules");

// 2. define constants
const JAN_1ST_2030 = 1893456000;

// 3. Export a module using `buildModule`
module.exports = buildModule("CounterModule", (m) => {

    // 4. get constants from define
    const unlockTime = m.getParameter("unlockTime", JAN_1ST_2030);

    // 5. Use the `getAccount` method to select the deployer account
    const deployer = m.getAccount(0);

    // 6. Deploy the `Counter` contract
    const counter = m.contract("Counter", [], {  from: deployer  });

    // 7. Return an object from the module
    return { counter };
});

```

然后执行

```bash
➜  harmonia git:(main) ✗ npx hardhat ignition deploy ignition/modules/deploy-ignition.js --network sepolia
✔ Confirm deploy to network sepolia (11155111)? … yes
Hardhat Ignition 🚀

Deploying [ CounterModule ]

Batch #1
  Executed CounterModule#Counter

[ CounterModule ] successfully deployed 🚀

Deployed Addresses

CounterModule#Counter - 0x4Abe176c221b5D8083d68D66323C68Cd595BE9f5
➜  harmonia git:(main) ✗ 
```

## 10. 验证合约

```sh
npx hardhat verify --network sepolia 0x你的合约地址
```

如果合约有构造参数，例如：

```solidity
constructor(uint256 _initialCount, address _owner) {
    count = _initialCount;
    owner = _owner;
}
```

则验证时需要传递参数：

```sh
npx hardhat verify --network sepolia 0x你的合约地址 100 0x你的钱包地址
```

## 11. 确保验证成功

- 确保 `solidity` 版本与 `hardhat.config.js` 中配置一致。
- 如果合约有构造参数，必须正确提供。
- 确保 `.env` 文件中包含正确的 API Key。

验证成功后，可以在 Etherscan 上查看合约代码。

```sh
Successfully verified contract Counter on the block explorer.
https://sepolia.etherscan.io/address/0x你的合约地址#code
```
