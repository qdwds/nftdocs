# 可合成的NFT
NFT是`独一无二`的,但它却不是一成不变的。所以NFT是可以成长的,可以通过两个不同的NFT合成一个新的NFT。 生成的一个新的NFT,有的自己属性和技能同样继承NFT的属性让NFT实现更多玩法。

## 案例介绍
项目中，我们初次mint出来的NFT等级是0级，并且它们的创建者也是0，通过两个NFT合成之后创建一个新的NFT,新的NFT等级会+1，并且会继承原NFT的的Id。

### 代码

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
import "@openzeppelin/contracts/utils/Strings.sol";
import "hardhat/console.sol";

contract ComposeNFT is  ERC721, ERC721Enumerable, ERC721URIStorage {
    using Counters for Counters.Counter;
    using Strings for uint256;
    Counters.Counter private _tokenIdCounter;
    
    struct Donkey {
        address owner;
        uint tokenId;
        uint8 level;
        uint dadId; //  父
        uint mumId; //  母
        string uri;
    }
    Donkey[] public donkeys;

    constructor() ERC721("Donkeys NFT", "DONKEYS") {

    }

    function _baseURI() internal pure override returns (string memory) {
        return "https://raw.githubusercontent.com/qdwds/NFT-metadata/master/metadata/donkeys/";
    }

    function _setURI(uint256 _level) internal view returns (string memory uri){
        uint random = _random(100) + 1;
        uri =  string(abi.encodePacked(_level.toString(),"/images/",random.toString(),".png"));
    }

    function _random(uint _max) internal   view returns(uint256){
        uint256 random = uint256(keccak256(abi.encodePacked(msg.sender, block.timestamp, block.coinbase, gasleft())));
        return (random > block.number) ? (random - block.number) % _max : (block.number - random) % _max;
    }

    function safeMint(address _to) public {
        _innerMint(_to,0,0,0);
    }

    function batchSafeMint(address _to,uint8 _numWords) public {
        require(_numWords <= 100, "MAX mint 10");
        for (uint i = 0; i < _numWords; i++) {
            _innerMint(_to,0,0,0);
        }
    }

    function _innerMint(address _to, uint8 _level, uint256 _dadId, uint256 _mumId) internal {
        uint256 tokenId = _tokenIdCounter.current();
        _tokenIdCounter.increment();
        string memory uri =  string(abi.encodePacked(_baseURI(),_setURI(_level)));
        Donkey memory donkey = Donkey({
            owner: _to,
            tokenId: tokenId,
            level: _level,
            mumId: _mumId,
            dadId: _dadId,
            uri: uri
        });

        donkeys.push(donkey);
        _safeMint(_to, tokenId);
        _setTokenURI(tokenId, uri);
    }

    function getDonkey(uint256 _tokenId) public view returns (Donkey memory donkey) {
        donkey = donkeys[_tokenId];
    }

    function getDonkeys(address _owner) public view returns(Donkey[] memory ownerDonkeys) {
        uint balance = balanceOf(_owner);
        ownerDonkeys = new Donkey[](balance);
        for (uint256 i = 0; i < balance; i++){
            uint index = tokenOfOwnerByIndex(_owner, i);
            ownerDonkeys[i] = donkeys[index];
        }
    }

    function breed(uint256 _dadId, uint256 _mumId) public {
        Donkey memory dad = donkeys[_dadId];
        Donkey memory mum = donkeys[_mumId]; 
        require(dad.owner == msg.sender && mum.owner == msg.sender, "Pass ?");
        require(dad.level == mum.level, "level fild");
        require(dad.level < 3,"Create MAX level 3");
        _innerMint(msg.sender, dad.level + 1, dad.tokenId, mum.tokenId);
    }






    // The following functions are overrides required by Solidity.
    function _beforeTokenTransfer(address from, address to, uint256 tokenId, uint256 batchSize)
        internal
        override(ERC721, ERC721Enumerable)
    {
        super._beforeTokenTransfer(from, to, tokenId, batchSize);
    }

    function _burn(uint256 tokenId) internal override(ERC721, ERC721URIStorage) {
        super._burn(tokenId);
    }

    function tokenURI(uint256 tokenId)
        public
        view
        override(ERC721, ERC721URIStorage)
        returns (string memory)
    {
        return super.tokenURI(tokenId);
    }

    function supportsInterface(bytes4 interfaceId)
        public
        view
        override(ERC721, ERC721Enumerable)
        returns (bool)
    {
        return super.supportsInterface(interfaceId);
    }
}

```
### 部署测试

```typescript
import { ethers } from "hardhat";
import { createAbi } from "../utils/createAbi";

const main = async() => {
    const [acc1] = await ethers.getSigners();
    

    // 部署NFT合约
    const ComposeNFT = await ethers.getContractFactory("ComposeNFT");
    const compose = await ComposeNFT.deploy();
    await compose.deployed();

    await compose.batchSafeMint(acc1.address, "3");

    await createAbi( "ComposeNFT", compose.address)
}


main()
    .catch(err =>{
        console.log(err);
        process.exit(1);
    })
```