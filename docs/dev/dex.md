# NFT交易平台
因为NFT是可以被交易买卖的数字资产，而NFT交易所可以收集大量的NFT来撮合买家买家，所以交易所是生态中不可缺少的一个部分。

接下来我们实现自己的交易所，我们的交易所会实现以下几个功能
+ 上架NFT
+ 下架NFT
+ 购买NFT
+ 获取单个NFT信息
+ 获取所有NFT信息
+ 提取收益

## 代码实现
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
import "./ComposeNFT.sol";
contract Markets  {
    using Counters for Counters.Counter;
    Counters.Counter private indexCounter;
    struct Market {
        address seller;
        uint256 tokenId;
        uint256 index; //  订单
        uint256 price;
        string uri;
        bool active;
    }

    Market[] public markets;
    mapping (uint => Market) public tokenIdToMarket;
    ComposeNFT  compose;
    mapping (address => uint) public tokenIdToPrice;
    constructor(ComposeNFT _compose){
        compose = _compose;
    }
    // 上架状态
    function hasActive(uint256 _tokenId) public view returns(bool){
        return tokenIdToMarket[_tokenId].active;
    }
    
    
    // 上架 
    function shelve(uint256 _tokenId, uint256 _price) public {
        require(compose.ownerOf(_tokenId) == msg.sender, "The is owner?");
        require(!hasActive(_tokenId),"Already on shelves");
        require(
            compose.getApproved(_tokenId) == address(this) || 
            compose.isApprovedForAll(msg.sender, address(this)),
            "No approve"
        );

        uint256 index = indexCounter.current();
        indexCounter.increment();
        string memory uri = compose.tokenURI(_tokenId);
        console.log(uri);
        Market memory newMarket = Market(
            msg.sender,
            _tokenId,
            index,
            _price,
            uri,
            true
        );

        markets.push(newMarket);
        tokenIdToMarket[_tokenId] = newMarket;
    }
    // 下架
    function unShelve(uint256 _tokenId) public {
        require(compose.ownerOf(_tokenId) == msg.sender, "The is owner?");
        require(hasActive(_tokenId),"Removed from shelves");
        // require(compose.getApproved(_tokenId) == address(this), "No approve");

        _unShelve(_tokenId);
    }

    function allMarkets() public view  returns(Market[] memory allMarket){
        if(markets.length == 0){
            return new Market[](0);
        }

        uint256 count = 0;
        for (uint i = 0; i < markets.length; i ++){
            Market memory market = markets[i];
            if(market.active){
                count ++;
            }
        }

        allMarket = new Market[](count);
        uint j = 0;
        for (uint i = 0; i < markets.length; i++){
            if(markets[i].active){
                allMarket[j] = markets[i];
                j++;
            }
            if(j >= count){
                return allMarket;
            }
        }
    }

    function _unShelve(uint256 _tokenId) internal {
        uint index = tokenIdToMarket[_tokenId].index;
        delete markets[index];
        delete tokenIdToMarket[_tokenId];
         
    }
    // 全部市场信息
    // 购买
    function buy(uint256 _tokenId) public payable {
        Market memory market = tokenIdToMarket[_tokenId];
        require(msg.value >= market.price," Price error");
        require(msg.sender != market.seller, "Address error");
        require(market.active, "No market");


        _unShelve(market.tokenId);

        tokenIdToPrice[market.seller] = msg.value;

        compose.safeTransferFrom(market.seller, msg.sender, _tokenId);
    }
    // 取款
    function withdraw() public{
        uint balance = tokenIdToPrice[msg.sender];
        require(balance > 0, " no Price");
        tokenIdToPrice[msg.sender] = 0;
        (bool sent,) = msg.sender.call{value:balance}("");
        require(sent,"Withdraw error");
    }
}
```

## 项目部署
```typescript
import { ethers } from "hardhat";
import { address } from "../abi/ComposeNFT.json";
import { createAbi } from "../utils/createAbi";
const main  = async () => {
    const Markets = await ethers.getContractFactory("Markets");
    const markets = await Markets.deploy(address);
    await markets.deployed();
    console.log(markets.address);
    createAbi("Markets",markets.address);
} 

main()
    .catch(err =>{
        console.log(err);
        process.exit(1);
    })
```