https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L289
In FraxlendPair.sol at line 289
Instead of "i++" using "++i" might reduce gas consumption.

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L308
In FraxlendPair.sol at line 308
Instead of "i++" using "++i" might reduce gas consumption.

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L754
In FraxlendPairCore.sol at line 754
Because "_collateralAmount" is uint type it cannot be lower than 0. Thus, using "_collateralAmount != 0" instead of "_collateralAmount > 0" will be the same and might reduce gas consumption.

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L835
In FraxlendPairCore.sol at line 835
Because "userBorrowShares[msg.sender]" is uint type it cannot be lower than 0. Thus, using "userBorrowShares[msg.sender] != 0" instead of "userBorrowShares[msg.sender] > 0" will be the same and might reduce gas consumption.

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L1002
In FraxlendPairCore.sol at line 1002
Because "_leftoverBorrowShares" is uint type it cannot be lower than 0. Thus, using "_leftoverBorrowShares != 0" instead of "_leftoverBorrowShares > 0" will be the same and might reduce gas consumption.

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L1094
In FraxlendPairCore.sol at line 1094
Because "_initialCollateralAmount" is uint type it cannot be lower than 0. Thus, using "_initialCollateralAmount != 0" instead of "_initialCollateralAmount > 0" will be the same and might reduce gas consumption.