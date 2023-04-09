# dos漏洞和修复
> 一定要注重安全

dos就是导致合约宕机无法正常用行。如果是智能合约，那么就回导致智能合约无法正常运行。一般这种情况就是因为对变量进行混用导致的。在我们现实生活中注重`专业的事情找专业的人`，所以编程中同样注重`一个变量只做一件事件`。


## 漏洞代码
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

contract AwesomeNFT is ERC721 {
    uint256 totalSupply;

    constructor() ERC721("Awesome NFT", "AWESOME") {}

    function mint() external {
        totalSupply += 1;
        _mint(msg.sender, totalSupply);
    }

    function burn(uint256 _tokenId) external {
        require(_isApprovedOrOwner(msg.sender, _tokenId));
        _burn(_tokenId);
        totalSupply--;
    }
}
```
## 漏洞修复
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

contract AwesomeNFT is ERC721 {
    uint256 public totalSupply;
    uint256 public tokenId;
    constructor() ERC721("Awesome NFT", "AWESOME") {}

    function mint() external {
        tokenId += 1; //1 2 3
        totalSupply += 1;
        _mint(msg.sender, tokenId);
    }

    function burn(uint256 _tokenId) external {
        require(_isApprovedOrOwner(msg.sender, _tokenId));
        totalSupply -= 1;  //  3 2 1
        _burn(_tokenId);
    }
}

```