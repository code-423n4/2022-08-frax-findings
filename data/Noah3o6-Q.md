-> EVENT IS MISSING INDEXED FIELDS
Each event should use three indexed fields if there are three or more fields.

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=event%20UpdateRate(uint256%20_ratePerSec%2C%20uint256%20_deltaTime%2C%20uint256%20_utilizationRate%2C%20uint256%20_newRatePerSec)%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=event%20RepayAsset(address%20indexed%20_sender%2C%20address%20indexed%20_borrower%2C%20uint256%20_amountToRepay%2C%20uint256%20_shares)%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=event%20Liquidate(,uint256%20_amountToAdjust
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=event%20LeveragedPosition(,uint256%20_amountCollateralOut


-> _SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE

_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_mint(address(this)%2C%20_feesShare)%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_mint(_receiver%2C%20_shares)%3B
