## 1. PLACE EVENT IN ONE 
Some functions are declared right before the function is declared. I suggest putting the function in the same place, at the beginning of the contract

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L45
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L60
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L75
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L376-L382
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L389
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L504
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L696-L701
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L760
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L795-L800
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L846
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L897-L904
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1045-L1052
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1143-L1149



## 2. REDUNDANT COMMENT
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1020-L1022

this comment is written twice below:

```// NOTE: reverts if _collateralForLiquidator > userCollateralBalance
// Collateral is removed on behalf of borrower and sent to liquidator
// NOTE: reverts if _collateralForLiquidator > userCollateralBalance
```

## 3. CHECK ZERO ADDRESS 
Should add zero address check for receiver in removeCollateral, deposit, and mint function
Should add zero address check for borrower in repayAsset, liquidate, and liquidateClean function 

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L855-L861

