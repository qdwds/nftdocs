# hardhat

[Hardhat](https://hardhat.org/)是一个编译、部署、测试和调试以太坊应用的开发环境。它可以帮助开发人员管理和自动化构建智能合约和dApps过程中固有的重复性任务，并围绕这一工作流程轻松引入更多功能。这意味着hardhat在最核心的地方是编译、运行和测试智能合约。

## Hardhat框架优点
+ Hardhat 拥有大量插件，并允许自定义、灵活性和可扩展性。
+ Hardhat 运行内置ether.js 5
+ Hardhat 有良好的 console.log() 调试能力；会在调试时提供代码中发生的堆栈跟踪。
+ Hardhat 提供原生Typescript支持，并且还有一个Vscode扩展，为 Vscode 编辑器添加了可靠的支持。
+ Hardhat 带有一个内置的本地以太坊网络，称为Hardhat Network，用于在本地机器上运行和部署智能合约，是一个专为开发而设计的本地以太坊网络节点。
+ Hardaht 可以fork网络节点到本地上模拟调用使用

## 创建项目

```
npx hardhat init
```

## 编译合同
```
npx hardhat compile
```

## 测试合同
```
npx hardhat test
```

## fork主网
```
networks: {
  hardhat: {
    forking: {
      url: "https://mainnet.infura.io/v3/<key>",
    }
  }
}
```