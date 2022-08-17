## 1. USE SAFEAPPROVE INSTEAD OF APPROVE

approve() will fail for certain token implementations that do not return a boolean value (). Hence it is recommend to use safeApprove().

### Instances:
[FraxlendPairCore.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol)


src/contracts/FraxlendPairCore.sol:1103:        _assetContract.approve(_swapperAddress, _borrowAmount);
src/contracts/FraxlendPairCore.sol:1184:        _collateralContract.approve(_swapperAddress, _collateralToSwap);

## 2. Using of floating pragma

Contracts should be deployed with the same compiler version and flags that they have been tested the most with. Locking the pragma helps ensure that contracts do not accidentally get deployed using, for example, the latest compiler which may have higher risks of undiscovered bugs. Contracts may also be deployed by others and the pragma indicates the compiler version intended by the original authors.

```
// bad
pragma solidity ^0.4.4;


// good
pragma solidity 0.4.4;
```
*Note: a floating pragma version (ie. ^0.4.25) will compile fine with 0.4.26-nightly.2018.9.25, however nightly builds should never be used to compile code for production*
### Instances
https://code4rena.com/contests/2022-08-fraxlend-frax-finance-contest
all contracts in scope 
```
src/contracts/VariableInterestRate.sol:2:pragma solidity ^0.8.15;
src/contracts/FraxlendPairConstants.sol:2:pragma solidity ^0.8.15;
src/contracts/FraxlendPairCore.sol:2:pragma solidity ^0.8.15;
src/contracts/FraxlendPairDeployer.sol:2:pragma solidity ^0.8.15;
src/contracts/FraxlendPair.sol:2:pragma solidity ^0.8.15;
src/contracts/libraries/VaultAccount.sol:2:pragma solidity ^0.8.15;
src/contracts/libraries/SafeERC20.sol:2:pragma solidity ^0.8.15;
src/contracts/LinearInterestRate.sol:2:pragma solidity ^0.8.15;
src/contracts/FraxlendWhitelist.sol:2:pragma solidity ^0.8.15;
```
