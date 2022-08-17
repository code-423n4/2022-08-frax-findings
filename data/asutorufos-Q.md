L-1 `SAFEMINT()` SHOULD BE USED RATHER THAN `_MINT()` WHEREVER POSSIBLE
`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements IERC721Receiver. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/transmissions11/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function

[FraxlendPairCore.sol L#570](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_mint(_receiver%2C%20_shares)%3B)

L-2 EVENT SHOULD BE EMITTED IN SETTERS
[FraxlendPairDeployer.sol L#170](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#:~:text=function%20setCreationCode(bytes%20calldata%20_creationCode)%20external%20onlyOwner%20%7B)


N-1 EVENTS INDEXING
Each `event` should three `indexed` fields if there are three or more firleds

[FraxlendPair.sol L#228](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=event%20WithdrawFees(uint128%20_shares%2C%20address%20_recipient%2C%20uint256%20_amountToTransfer)%3B)