## 1. USE SAFEAPPROVE INSTEAD OF APPROVE

approve() will fail for certain token implementations that do not return a boolean value (). Hence it is recommend to use safeApprove().

### Instances:
[FraxlendPairCore.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol)
```
src/contracts/FraxlendPairCore.sol:1103:        _assetContract.approve(_swapperAddress, _borrowAmount);
src/contracts/FraxlendPairCore.sol:1184:        _collateralContract.approve(_swapperAddress, _collateralToSwap);
```

### Recommended Mitigation Steps

Update to safeapprove  in the function.

----

## 2. USE OF FLOATING PRAGMA

Recommend using fixed solidity version

### Instances
```
src/contracts/FraxlendWhitelist.sol:2:pragma solidity ^0.8.15;
src/contracts/FraxlendPairCore.sol:2:pragma solidity ^0.8.15;
src/contracts/LinearInterestRate.sol:2:pragma solidity ^0.8.15;
src/contracts/VariableInterestRate.sol:2:pragma solidity ^0.8.15;
src/contracts/FraxlendPairConstants.sol:2:pragma solidity ^0.8.15;
src/contracts/libraries/SafeERC20.sol:2:pragma solidity ^0.8.15;
src/contracts/libraries/VaultAccount.sol:2:pragma solidity ^0.8.15;
src/contracts/FraxlendPair.sol:2:pragma solidity ^0.8.15;
src/contracts/FraxlendPairDeployer.sol:2:pragma solidity ^0.8.15;
```