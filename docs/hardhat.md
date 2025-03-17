# hardhat

## 1、创建项目

安装 hardhat

```shell
➜  harmonia git:(main) npm install --save-dev hardhat


npm warn deprecated inflight@1.0.6: This module is not supported, and leaks memory. Do not use it. Check out lru-cache if you want a good and tested way to coalesce async requests by a key value, which is much more comprehensive and powerful.
npm warn deprecated glob@8.1.0: Glob versions prior to v9 are no longer supported
npm warn deprecated ethereumjs-abi@0.6.8: This library has been deprecated and usage is discouraged.

added 247 packages in 1m

55 packages are looking for funding
  run `npm fund` for details
➜  harmonia git:(main) ✗ 
```

在安装**Hardhat**的目录下运行：

```bash
➜  harmonia git:(main) ✗ npx hardhat
```

如果碰到

```shell
Error: Cannot find module '@nomicfoundation/hardhat-toolbox'
```

执行

```shell
➜  harmonia git:(main) ✗ npm install --save-dev @nomicfoundation/hardhat-toolbox

npm warn deprecated lodash.isequal@4.5.0: This package is deprecated. Use require('node:util').isDeepStrictEqual instead.
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported
npm warn deprecated glob@5.0.15: Glob versions prior to v9 are no longer supported
npm warn deprecated glob@7.1.7: Glob versions prior to v9 are no longer supported

added 328 packages, and audited 576 packages in 45s

99 packages are looking for funding
  run `npm fund` for details

13 low severity vulnerabilities

To address issues that do not require attention, run:
  npm audit fix

Some issues need review, and may require choosing
a different dependency.

Run `npm audit` for details.
```

重新执行

```shell
➜  harmonia git:(main) ✗ npx hardhat                                            


Hardhat version 2.22.19

Usage: hardhat [GLOBAL OPTIONS] [SCOPE] <TASK> [TASK OPTIONS]

GLOBAL OPTIONS:

  --config           	A Hardhat config file. 
  --emoji            	Use emoji in messages. 
  --flamegraph       	Generate a flamegraph of your Hardhat tasks 
  --help             	Shows this message, or a task's help if its name is provided 
  --max-memory       	The maximum amount of memory that Hardhat can use. 
  --network          	The network to connect to. 
  --show-stack-traces	Show stack traces (always enabled on CI servers). 
  --tsconfig         	A TypeScript config file. 
  --typecheck        	Enable TypeScript type-checking of your scripts/tests 
  --verbose          	Enables Hardhat verbose logging 
  --version          	Shows hardhat's version. 


AVAILABLE TASKS:

  check              	Check whatever you need
  clean              	Clears the cache and deletes all artifacts
  compile            	Compiles the entire project, building all artifacts
  console            	Opens a hardhat console
  coverage           	Generates a code coverage report for tests
  flatten            	Flattens and prints contracts and their dependencies. If no file is passed, all the contracts in the project will be flattened.
  gas-reporter:merge 	
  help               	Prints this message
  node               	Starts a JSON-RPC server on top of Hardhat Network
  run                	Runs a user-defined script after compiling the project
  test               	Runs mocha tests
  typechain          	Generate Typechain typings for compiled contracts
  verify             	Verifies a contract on Etherscan or Sourcify


AVAILABLE TASK SCOPES:

  ignition           	Deploy your smart contracts using Hardhat Ignition
  vars               	Manage your configuration variables

To get help for a specific task run: npx hardhat help [SCOPE] <TASK>

➜  harmonia git:(main) ✗ 
```

## 2、安装其他的库

```bash
npm install @openzeppelin/contracts --save-dev
```

这个语句和上面的

```bash
➜  harmonia git:(main) ✗ npm install --save-dev @nomicfoundation/hardhat-toolbox
```

其实是一样的，就是安装 **@openzeppelin/contracts** 和 **@nomicfoundation/hardhat-toolbox** 这两个依赖。
