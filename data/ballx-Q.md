
#### Impact
Issue Information: [L001](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l001---unsafe-erc20-operations)

#### Findings:
```
src/contracts/FraxlendPairCore.sol::783 => /// @dev msg.sender must call ERC20.approve() on the Collateral Token contract prior to invocation
src/contracts/FraxlendPairCore.sol::849 => /// @dev The payer must have called ERC20.approve() on the Asset Token contract prior to invocation
src/contracts/FraxlendPairCore.sol::878 => /// @dev Caller must first invoke ```ERC20.approve()``` for the Asset Token contract
src/contracts/FraxlendPairCore.sol::1055 => /// @dev Caller must invoke ```ERC20.approve()``` on the Collateral Token contract prior to calling function
src/contracts/FraxlendPairCore.sol::1103 => _assetContract.approve(_swapperAddress, _borrowAmount);
src/contracts/FraxlendPairCore.sol::1184 => _collateralContract.approve(_swapperAddress, _collateralToSwap);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Unspecific Compiler Version Pragma

#### Impact
Issue Information: [L003](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:
```
src/contracts/FraxlendPair.sol::2 => pragma solidity ^0.8.15;
src/contracts/FraxlendPairConstants.sol::2 => pragma solidity ^0.8.15;
src/contracts/FraxlendPairCore.sol::2 => pragma solidity ^0.8.15;
src/contracts/FraxlendPairDeployer.sol::2 => pragma solidity ^0.8.15;
src/contracts/FraxlendPairHelper.sol::2 => pragma solidity ^0.8.15;
src/contracts/FraxlendWhitelist.sol::2 => pragma solidity ^0.8.15;
src/contracts/LinearInterestRate.sol::2 => pragma solidity ^0.8.15;
src/contracts/VariableInterestRate.sol::2 => pragma solidity ^0.8.15;
src/contracts/interfaces/IERC4626.sol::2 => pragma solidity >=0.8.15;
src/contracts/interfaces/IFraxlendPair.sol::2 => pragma solidity >=0.8.15;
src/contracts/interfaces/IFraxlendWhitelist.sol::2 => pragma solidity >=0.8.15;
src/contracts/interfaces/IRateCalculator.sol::2 => pragma solidity >=0.8.15;
src/contracts/interfaces/ISwapper.sol::2 => pragma solidity >=0.8.15;
src/contracts/libraries/SafeERC20.sol::2 => pragma solidity ^0.8.15;
src/contracts/libraries/VaultAccount.sol::2 => pragma solidity ^0.8.15;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

