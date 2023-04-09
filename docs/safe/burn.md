# 销毁漏洞和修复
> 一定要注重安全。

销毁漏洞就是在没有经过别人授权的情况下恶意销毁别人的物品等。销毁漏洞虽然比较低级，但是有的项目中确实会犯这个错误。只要出现就回造成不可挽回的顺序。

## 漏洞代码
一下代码存在的问题是，销毁的时候没有判断调用者是否拥有或者是否是当前NFT的被授权者。
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

contract MyToken is ERC721 {
    constructor() ERC721("MyToken", "MTK") {}
    function mint(uint256 _tokenId) public {
        _mint(msg.sender, _tokenId);
    }
    function burn(uint256 _tokenId) public {
        _burn(_tokenId);
    }
}
```

## 修复漏洞
销毁时候增加判断是否为NFT有拥有者或者是被授权者
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

contract MyToken is ERC721 {
    constructor() ERC721("MyToken", "MTK") {}
    function mint(uint256 _tokenId) public {
        _mint(msg.sender, _tokenId);
    }
    function burn(uint256 _tokenId) public {
        require(_isApprovedOrOwner(msg.sender, _tokenId), "ERC721: caller is not token owner or approved");
        _burn(_tokenId);
    }
}
```