# 练习

### **基础合约**（适合初学者）

1. **代币合约（ERC20）**：实现一个简单的 ERC20 代币，支持转账、授权和余额查询。
2. **投票合约**：创建一个去中心化投票系统，允许用户提交候选人并投票。
3. **彩票合约**：用户可以购买彩票，定期随机抽取赢家，并发送奖金池中的资金。
4. **简单银行合约**：实现存款、取款、余额查询等功能，并确保安全性（如防止重入攻击）。
5. **访问控制合约**：使用 `Ownable` 或 `Roles` 机制来管理权限（如管理员可以修改某些变量）。
6. **代币时间锁（Vesting）合约**：设置代币锁定机制，用户到达特定时间才能领取代币。

### **进阶合约**（适合熟练 Solidity 语法后）

1. **NFT（ERC721）合约**：实现一个 NFT 合约，允许用户铸造、转让和销毁 NFT。
2. **众筹合约**：用户可以向项目捐款，达到目标后资金释放，未达到则退款。
3. **时间锁合约**：创建一个资金托管合约，用户存入的资金要到一定时间后才能取出。
4. **预言机整合合约**：使用 Chainlink 获取链外数据，比如获取 ETH/USD 价格。
5. **订阅支付合约**：用户可以设置自动扣款，比如按月订阅服务。
6. **多签钱包（Multisig Wallet）合约**：需要多个签名才可执行转账，提高安全性。
7. **保险合约**：用户支付保费，如果满足特定条件（如航班延误），则自动赔付。
8. **自动收益分配合约**：类似股息分配，持有某种代币的用户按比例自动获得收益。

### **高级合约**（适合深入学习 Solidity 及安全性）

1. **去中心化交易所（DEX）**：实现一个简单的 AMM（自动做市商）交易池，如 Uniswap V1。
2. **流动性挖矿合约**：用户可以存入 LP 代币，按照一定规则获得奖励代币。
3. **链上拍卖合约**：实现 English Auction（递增式竞价拍卖）或 Dutch Auction（降价式拍卖）。
4. **闪电贷合约**（Flash Loan）**：用户可以无抵押借款，必须在同一交易中还款，否则交易回滚。
5. **去中心化身份管理（DID）合约**：用户可以存储和管理自己的链上身份信息。
6. **去中心化社交合约**：允许用户发布内容、点赞、打赏，类似 Web3 版的社交平台。
7. **链上游戏合约**：例如一个简单的猜数字游戏，或 Play-to-Earn 经济系统。
8. **收益聚合器（Yield Aggregator）合约**：类似 Yearn Finance，自动帮用户寻找最佳收益策略。

### **扩展性与安全**

1. **可升级合约（Proxy Pattern）**：实现可升级的智能合约，如 UUPS 或 Transparent Proxy。
2. **MEV 保护合约**：防止三明治攻击、抢跑等 MEV 问题。
3. **治理合约（DAO）**：允许代币持有者投票决定合约参数，如去中心化基金管理。
4. **合成资产（Synthetic Asset）合约**：创建类似 Synthetix 这样的去中心化衍生品协议。
5. **债务市场合约**：允许用户借贷资产，设定利率机制，类似 Aave。