## [L-01] MISSING RETURN VALUE CHECK FOR APPROVE()
Some tokens do not revert but return false when approval fails. Please consider checking the return values of the following `approve` functions to prevent silent approval failures.
```
contracts\FraxlendPairCore.sol
  1103: _assetContract.approve(_swapperAddress, _borrowAmount);
  1184: _collateralContract.approve(_swapperAddress, _collateralToSwap);
```

## [L-02] MISSING ZERO-ADDRESS CHECK FOR CRITICAL ADDRESSES
To prevent unintended behaviors, the critical address inputs should be checked against `address(0)`. Please consider checking the following address variables.
```
contracts\FraxlendPairCore.sol
	163-168:
		(
			address _circuitBreaker,
			address _comptrollerAddress,
			address _timeLockAddress,
			address _fraxlendWhitelistAddress
		) = abi.decode(_immutables, (address, address, address, address));

	179-187:
		(
			address _asset,
			address _collateral,
			address _oracleMultiply,
			address _oracleDivide,
			uint256 _oracleNormalization,
			address _rateContract,

		) = abi.decode(_configData, (address, address, address, address, uint256, address, bytes));

contracts\FraxlendPairDeployer.sol
	98-103:
                constructor(
                        address _circuitBreaker,
                        address _comptroller,
                        address _timelock,
                        address _fraxlendWhitelist
                ) Ownable() {
```

## [N-01] STATE VARIABLE WITH CHANGEABLE VALUES CAN BE NAMED WITH LOWERCASE LETTERS
It is a convention to use lowercase letters for naming state variables that have changeable values. Please consider renaming the following variable by using lowercase letters because its value gets assigned in another function.
```
contracts\FraxlendPairCore.sol
    address public TIME_LOCK_ADDRESS;
```

## [N-02] CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS
To improve readability and maintainability, a constant can be used instead of the magic number. Please consider replacing the magic numbers used in the following code with constants.
```
contracts\FraxlendPair.sol
  105: _assetsPerUnitShare = totalAsset.toAmount(1e18, false);

contracts\FraxlendPairCore.sol
  468: _interestEarned = (_deltaTime * _totalBorrow.amount * _currentRateInfo.ratePerSec) / 1e18;
  522: uint256 _price = uint256(1e36);

contracts\FraxlendPairDeployer.sol
  171: bytes memory _firstHalf = BytesLib.slice(_creationCode, 0, 13000);
  173: if (_creationCode.length > 13000) {
  174: bytes memory _secondHalf = BytesLib.slice(_creationCode, 13000, _creationCode.length - 13000);

contracts\VariableInterestRate.sol
  69: uint256 _deltaUtilization = ((MIN_UTIL - _utilization) * 1e18) / MIN_UTIL;
  76: uint256 _deltaUtilization = ((_utilization - MAX_UTIL) * 1e18) / (UTIL_PREC - MAX_UTIL);
```

## [N-03] UNDERSCORES IN NUMBER LITERALS CAN BE USED
Underscores in number literals can be used for improving readability and maintainability. Please consider using underscores for the following number literals.
```
contracts\FraxlendPairDeployer.sol
  49: uint256 public DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision
  51: uint256 public DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision

contracts\LinearInterestRate.sol
  34: uint256 private constant MAX_INT = 146248508681; // 10,000% annual rate

contracts\VariableInterestRate.sol
  40: uint64 private constant MIN_INT = 79123523; // 0.25% annual rate
  41: uint64 private constant MAX_INT = 146248508681; // 10,000% annual rate 
```

## [N-04] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. @param or @return comments are missing for the following constructor and functions. Please consider completing NatSpec comments for them.
```
contracts\FraxlendPairCore.sol
  151: constructor(   
  393: function addInterest()
  409: function _addInterest()

contracts\FraxlendPairDeployer.sol
  191: function _deployFirst( 
```

## [N-05] FLOATING PRAGMAS
It is a best practice to lock pragmas instead of using floating pragmas to ensure that contracts are tested and deployed with the intended compiler version. Accidentally deploying contracts with different compiler versions can lead to unexpected risks and undiscovered bugs. Please consider locking pragmas for the following files.
```
contracts\FraxlendPair.sol
  2: pragma solidity ^0.8.15;

contracts\FraxlendPairConstants.sol
  2: pragma solidity ^0.8.15;

contracts\FraxlendPairCore.sol
  2: pragma solidity ^0.8.15;

contracts\FraxlendPairDeployer.sol
  2: pragma solidity ^0.8.15;

contracts\FraxlendPairHelper.sol
  2: pragma solidity ^0.8.15;

contracts\FraxlendWhitelist.sol
  2: pragma solidity ^0.8.15;

contracts\LinearInterestRate.sol
  2: pragma solidity ^0.8.15;

contracts\VariableInterestRate.sol
  2: pragma solidity ^0.8.15;

contracts\interfaces\IERC4626.sol
  2: pragma solidity >=0.8.15;

contracts\interfaces\IFraxlendPair.sol
  2: pragma solidity >=0.8.15;

contracts\interfaces\IFraxlendWhitelist.sol
  2: pragma solidity >=0.8.15;

contracts\interfaces\IRateCalculator.sol
  2: pragma solidity >=0.8.15;

contracts\interfaces\ISwapper.sol
  2: pragma solidity >=0.8.15;

contracts\libraries\SafeERC20.sol
  2: pragma solidity ^0.8.15;

contracts\libraries\VaultAccount.sol
  2: pragma solidity ^0.8.15;
```