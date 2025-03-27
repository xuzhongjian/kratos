## call 与 delegatecall

`call` 是常规调用，`delegatecall` 为委托调用，`staticcall` 是静态调用，不修改合约状态， 相当于普通的 `view` 方法调用。

常规调用 `call` 与 委托调用 `delegatecall` 的区别是什么呢？

当我们在用钱包发起交易时，用[合约接口调用函数](https://decert.me/tutorial/solidity/solidity-basic/interface)，都是常规调用，每次常规调用都会切换上下文，切换上下文可以这样理解：每一个地址在 EVM 有一个独立的空间，空间有各自的摆设（变量布局），切换上下文就像从一个空间进入另一个空间（也可以携带一些东西进入另一个空间），每次进入一个空间后，只能使用当前空间内的东西。

委托调用不一样，没有上下文的切换，它像是给你一个主人身份（委托），你可以在当下空间做你想做的事。

我们用一个代码实例看看常规调用 `call` 与 委托调用 `delegatecall` 的不同的：

```solidity
pragma solidity ^0.8.0;

contract Counter {
    uint public counter;
    address public sender;

    function count() public {
        counter += 1;
        sender = msg.sender;
    }
}

contract CallTest {
    uint public counter;
    address public sender;


    function lowCallCount(address addr) public {
    //  (Counter(c)).count();
        bytes memory methodData =abi.encodeWithSignature("count()");
        addr.call(methodData);
    }

    // 只是调用代码，合约环境还是当前合约。
    function lowDelegatecallCount(address addr) public {
        bytes memory methodData = abi.encodeWithSignature("count()");
        addr.delegatecall(methodData);
    }

}
```

结果：

```livescript
lowCallCount()  ->  Counter::counter + 1   

lowDelegatecallCount() -> CallTest::counter + 1   
```



`lowCallCount` 函数中使用`call`，上下文从 `CallTest` 地址空间跳到了 `Counter`地址空间， 因此是`Counter`内部的 `counter` 值 + 1 了。

`lowDelegatecallCount` 函数中使用`delegatecall`，上下文保证在 `CallTest` 地址空间，因此是`CallTest`的 `counter` 值 + 1 了。