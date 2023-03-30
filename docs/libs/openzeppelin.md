# openzeppelin

> [OpenZeppelin/Contracts](https://docs.openzeppelin.com/) 是一个用于安全智能合约开发的库。

它提供了 ERC20、 ERC721、ERC777、ERC1155 等标准的实现，还提供 Solidity 组件来构建自定义合同和更复杂的分散系统。

OpenZeppelin/Contracts是由`OpenZeppelin`官网维护的一个开源智能合约库，代码都是经过测试和社区审核过的，相对来说是一个比较安全的智能合约开发库。

## 下载安装
nodejs管理，通过npm下载到本地使用，可以配合[truffle](https://trufflesuite.com/)或[hardhat](https://hardhat.org/)脚手架使用
```
npm install @openzeppelin/contracts
```

## 用法
使用openzeppelin创建的一个基本职能合约，以下案例可用直接在remix中直接使用。
```solidity
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

contract MyNFT is ERC721 {
    constructor() ERC721("MyNFT", "MNFT") {
    }
}
```