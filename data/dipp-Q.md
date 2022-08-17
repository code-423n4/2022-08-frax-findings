# 1. State variables should be checked when set

## Line References

[FraxlendPairCore.sol#L151-L237](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L151-L237)

## Description

The liquidation fee for pair contracts should have a cap to prevent deployers mistakenly setting a fee too high. Consider capping the liquidation fee in the constructor of the ```FraxlendPairCore.sol``` contract.

The ```oracleNormalization``` variable should be more than 0 since this would cause the ```_updateExchangeRate``` function to revert, breaking core functionality of the contract.

```maturityDate``` should be at least >= block.timestamp.



# 2. leveragePosition and repayAssetWithCollateral may not work correctly if asset's approve logic is not supported

## Line References

[FraxlendPairCore.sol#L1103](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1103)

[FraxlendPairCore.sol#L1184](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1184)

## Description

Some tokens, such as USDT, have non-standard implementations of the ```approve``` function. Before an approval is updated, it must first be set to 0.

## Impact

The ```leveragePosition``` and the ```repayAssetWithCollateral``` functions will not function until the approval to the ```_swapperAddress``` is set to 0. Since the funtions may be called with a 0 value input, the approval to the ```_swapperAddress``` could be set to 0, however some users might not be aware.

## Recommended Mitigation Steps

Consider setting the approval of the ```_swapperAddress``` to 0 before setting a new approval amount.