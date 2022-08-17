# Fraxlend Contest QA Report

## Summary

The following QA issues were found during the code audit:

1. Unsafe ERC20 Operation(s) (11 instances)
2. Unspecific Compiler Version Pragma (32 instances)
3. File missing NatSpec (7 instances)

Total 50 instances of 3 issues.

## Unsafe ERC20 Operation(s) (11 instances)

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard. It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

```solidity
2022-08-frax/src/contracts/FraxlendPairCore.sol::1103 => _assetContract.approve(_swapperAddress, _borrowAmount);

2022-08-frax/src/contracts/FraxlendPairCore.sol::1184 => _collateralContract.approve(_swapperAddress, _collateralToSwap);

2022-08-frax/src/test/e2e/BasePairTest.sol::441 => asset.approve(address(pair), _amount);

2022-08-frax/src/test/e2e/BasePairTest.sol::454 => IERC20(_pair.asset()).approve(address(_pair), _amount);

2022-08-frax/src/test/e2e/BasePairTest.sol::473 => asset.approve(address(pair), _amount);

2022-08-frax/src/test/e2e/BasePairTest.sol::492 => collateral.approve(address(pair), _collateralAmount);

2022-08-frax/src/test/e2e/BasePairTest.sol::515 => asset.approve(address(pair), _amountToApprove);

2022-08-frax/src/test/e2e/InterestPairTest.sol::43 => asset.approve(address(pair), _amountToReturn);

2022-08-frax/src/test/e2e/LeveragePairTest.sol::54 => collateral.approve(address(pair), _initialCollateral);

2022-08-frax/src/test/e2e/LiquidationPairTest.sol::39 => asset.approve(address(pair), toBorrowAmount(_shares, true));

2022-08-frax/src/test/e2e/LiquidationPairTest.sol::65 => asset.approve(address(pair), toBorrowAmount(_shares, true));
```

## Unspecific Compiler Version Pragma (32 instances)

Avoid floating pragmas for non-library contracts. While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. It is recommended to pin to a concrete compiler version.

```solidity
2022-08-frax/src/contracts/FraxlendPair.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/contracts/FraxlendPairConstants.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/contracts/FraxlendPairCore.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/contracts/FraxlendPairHelper.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/contracts/FraxlendWhitelist.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/contracts/LinearInterestRate.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/contracts/VariableInterestRate.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/contracts/interfaces/IERC4626.sol::2 => pragma solidity >=0.8.15;

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::2 => pragma solidity >=0.8.15;

2022-08-frax/src/contracts/interfaces/IFraxlendWhitelist.sol::2 => pragma solidity >=0.8.15;

2022-08-frax/src/contracts/interfaces/IRateCalculator.sol::2 => pragma solidity >=0.8.15;

2022-08-frax/src/contracts/interfaces/ISwapper.sol::2 => pragma solidity >=0.8.15;

2022-08-frax/src/contracts/libraries/SafeERC20.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/contracts/libraries/VaultAccount.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/test/e2e/BasePairTest.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/test/e2e/FraxlendPairHelper.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/test/e2e/InterestPairTest.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/test/e2e/LendPairTest.t.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/test/e2e/LeveragePairTest.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/test/e2e/LinearRateTest.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/test/e2e/LiquidationPairTest.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/test/e2e/Scenarios.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/test/e2e/VariableRateTest.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/test/e2e/WithdrawPairTest.sol::2 => pragma solidity ^0.8.15;

2022-08-frax/src/test/interfaces/IUniswapV2Pair.sol::1 => pragma solidity >=0.8.0;

2022-08-frax/src/test/interfaces/IUniswapV2Router01.sol::1 => pragma solidity >=0.8.0;

2022-08-frax/src/test/interfaces/IUniswapV2Router02.sol::1 => pragma solidity >=0.8.0;

2022-08-frax/src/test/interfaces/SafeMath.sol::1 => pragma solidity >=0.8.0;

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::1 => pragma solidity >=0.8.0;
```

## File missing NatSpec (7 instances)

Providing NatSpec is a good practice for further development / debugging etc.

```solidity
File: 2022-08-frax/src/contracts/FraxlendPairConstants.sol

File: 2022-08-frax/src/contracts/FraxlendPairHelper.sol

File: 2022-08-frax/src/contracts/interfaces/IERC4626.sol

File: 2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol

File: 2022-08-frax/src/contracts/interfaces/IFraxlendWhitelist.sol

File: 2022-08-frax/src/contracts/interfaces/IRateCalculator.sol

File: 2022-08-frax/src/contracts/interfaces/ISwapper.sol
```
