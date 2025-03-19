# 测试脚本

当前文件描述的是，在 hardhat 框架下，使用 js 脚本进行测试的时候的测试脚本的一些笔记。

## 1、获取账户

```js
[owner, user] = await ethers.getSigners();
```

通过这个指令，可以获取两个或者更多测试用的账户。在进行合约部署的时候，默认使用第一个账户。

## 2、创建合约

```js
        HelloCreator = await ethers.getContractFactory("HelloCreator");
        helloCreator = await HelloCreator.deploy();
        await helloCreator.waitForDeployment();
        console.log("helloCreator deployed at:", helloCreator.target);
        console.log("owner address of helloCreator:", owner.address);
```

