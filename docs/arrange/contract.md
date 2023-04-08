# 合约部署和代码开源验证
## hardhat
合约部署可以使用remix、hardhat或者其他框架进行部署。
```typescript
//   hardhat.config.js
networks: {
    mainnet: {
      url: "https://mainnet.infura.io/v3",  //  节点地址
      accounts: [私钥],
    },
}

//  部署命令
npx hardhat  run scripts/composeNFT.ts --network localhost
```

## remix
在remix中，等待编译之后点击下图按钮就能直接部署。
![contract abi](../../img/arrange/1.png)