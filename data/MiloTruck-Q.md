# Low Issues

|No.|Issue|Instances|
|:-|:-|:-:|
|1|`abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`|3|

Total: **3** instances over **1** issue

## `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
As seen from the [Solidity Docs](https://docs.soliditylang.org/en/v0.8.15/abi-spec.html#non-standard-packed-mode):
> If you use `keccak256(abi.encodePacked(a, b))` and both `a` and `b` are dynamic types, it is easy to craft collisions in the hash value by moving parts of `a` into `b` and vice-versa. More specifically, `abi.encodePacked("a", "bc") == abi.encodePacked("ab", "c")`. 
>
> If you use `abi.encodePacked` for signatures, authentication or data integrity, make sure to always use the same types and check that at most one of them is dynamic. Unless there is a compelling reason, `abi.encode` should be preferred.

Compared to `abi.encodePacked()`, `abi.encode()` pads all items to 32 bytes, which helps to prevent hash collisions. Additionally, if there is only one argument to `abi.encodePacked()`, it can be cast to `bytes()` or `bytes32()` instead.

_There are **3** instances of this issue:_  
```solidity
src/contracts/FraxlendPairDeployer.sol:
 204:        bytes32 salt = keccak256(abi.encodePacked(_saltSeed, _configData));
 204:        bytes32 salt = keccak256(abi.encodePacked(_saltSeed, _configData));
 372:        keccak256(abi.encodePacked(_name)),
```

# Non-Critical Issues

|No.|Issue|Instances|
|:-|:-|:-:|
|1|Floating compiler versions|5|
|2|Adding a `return` statement when the function defines a named return variable, is redundant|1|
|3|`constants` should be defined rather than using magic numbers|17|
|4|`event` is missing `indexed` fields|9|

Total: **32** instances over **4** issues

## Floating compiler versions
Non-library/interface files should use fixed compiler versions, not floating ones.

_There are **5** instances of this issue:_  
```solidity
src/contracts/VariableInterestRate.sol:
   2:        pragma solidity ^0.8.15;

src/contracts/FraxlendPairDeployer.sol:
   2:        pragma solidity ^0.8.15;

src/contracts/FraxlendWhitelist.sol:
   2:        pragma solidity ^0.8.15;

src/contracts/LinearInterestRate.sol:
   2:        pragma solidity ^0.8.15;

src/contracts/FraxlendPair.sol:
   2:        pragma solidity ^0.8.15;
```

## Redundant `return` statements
If a function has defined return variables, they will be returned when the function terminates. Thus, there is no need to explicitly specify them in a return statement.

_There is **1** instance of this issue:_  
```solidity
src/contracts/FraxlendPairCore.sol:
 422:        return (_interestEarned, _feesAmount, _feesShare, _newRate);
```

## `constants` should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) code can benefit from using readable constants instead of hex/numeric literals.

_There are **17** instances of this issue:_  
`1e18`:
```solidity
src/contracts/VariableInterestRate.sol:
  69:        uint256 _deltaUtilization = ((MIN_UTIL - _utilization) * 1e18) / MIN_UTIL;
```
`1e18`:
```solidity
src/contracts/VariableInterestRate.sol:
  76:        uint256 _deltaUtilization = ((_utilization - MAX_UTIL) * 1e18) / (UTIL_PREC - MAX_UTIL);
```
`13000`:
```solidity
src/contracts/FraxlendPairDeployer.sol:
 171:        bytes memory _firstHalf = BytesLib.slice(_creationCode, 0, 13000);
```
`13000`:
```solidity
src/contracts/FraxlendPairDeployer.sol:
 173:        if (_creationCode.length > 13000) {
```
`13000`:
```solidity
src/contracts/FraxlendPairDeployer.sol:
 174:        bytes memory _secondHalf = BytesLib.slice(_creationCode, 13000, _creationCode.length - 13000);
```
`13000`:
```solidity
src/contracts/FraxlendPairDeployer.sol:
 174:        bytes memory _secondHalf = BytesLib.slice(_creationCode, 13000, _creationCode.length - 13000);
```
`18`:
```solidity
src/contracts/FraxlendPair.sol:
  85:        return 18;
```
`1e18`:
```solidity
src/contracts/FraxlendPair.sol:
 105:        _assetsPerUnitShare = totalAsset.toAmount(1e18, false);
```
`1e18`:
```solidity
src/contracts/FraxlendPairCore.sol:
 468:        _interestEarned = (_deltaTime * _totalBorrow.amount * _currentRateInfo.ratePerSec) / 1e18;
```
`1e36`:
```solidity
src/contracts/FraxlendPairCore.sol:
 522:        uint256 _price = uint256(1e36);
```
`9000`:
```solidity
src/contracts/FraxlendPairCore.sol:
 194:        dirtyLiquidationFee = (_liquidationFee * 9000) / LIQ_PRECISION; // 90% of clean fee
```
`64`:
```solidity
src/contracts/libraries/SafeERC20.sol:
  19:        if (data.length >= 64) {
```
`32`:
```solidity
src/contracts/libraries/SafeERC20.sol:
  21:        } else if (data.length == 32) {
```
`32`:
```solidity
src/contracts/libraries/SafeERC20.sol:
  23:        while (i < 32 && data[i] != 0) {
```
`32`:
```solidity
src/contracts/libraries/SafeERC20.sol:
  27:        for (i = 0; i < 32 && data[i] != 0; i++) {
```
`32`:
```solidity
src/contracts/libraries/SafeERC20.sol:
  57:        return success && data.length == 32 ? abi.decode(data, (uint8)) : 18;
```
`18`:
```solidity
src/contracts/libraries/SafeERC20.sol:
  57:        return success && data.length == 32 ? abi.decode(data, (uint8)) : 18;
```

## `event` is missing `indexed` fields
Each `event` should use three `indexed` fields if there are three or more fields.
  
_There are **9** instances of this issue:_  
```solidity
src/contracts/FraxlendPair.sol:
 228:        event WithdrawFees(uint128 _shares, address _recipient, uint256 _amountToTransfer);

src/contracts/FraxlendPairCore.sol:
 376:        event AddInterest(
 377:            uint256 _interestEarned,
 378:            uint256 _rate,
 379:            uint256 _deltaTime,
 380:            uint256 _feesAmount,
 381:            uint256 _feesShare
 382:        );

 389:        event UpdateRate(uint256 _ratePerSec, uint256 _deltaTime, uint256 _utilizationRate, uint256 _newRatePerSec);

 696:        event BorrowAsset(
 697:            address indexed _borrower,
 698:            address indexed _receiver,
 699:            uint256 _borrowAmount,
 700:            uint256 _sharesAdded
 701:        );

 760:        event AddCollateral(address indexed _sender, address indexed _borrower, uint256 _collateralAmount);
 846:        event RepayAsset(address indexed _sender, address indexed _borrower, uint256 _amountToRepay, uint256 _shares);

 897:        event Liquidate(
 898:            address indexed _borrower,
 899:            uint256 _collateralForLiquidator,
 900:            uint256 _sharesToLiquidate,
 901:            uint256 _amountLiquidatorToRepay,
 902:            uint256 _sharesToAdjust,
 903:            uint256 _amountToAdjust
 904:        );


1045:        event LeveragedPosition(
1046:            address indexed _borrower,
1047:            address _swapperAddress,
1048:            uint256 _borrowAmount,
1049:            uint256 _borrowShares,
1050:            uint256 _initialCollateralAmount,
1051:            uint256 _amountCollateralOut
1052:        );


1143:        event RepayAssetWithCollateral(
1144:            address indexed _borrower,
1145:            address _swapperAddress,
1146:            uint256 _collateralToSwap,
1147:            uint256 _amountAssetOut,
1148:            uint256 _sharesRepaid
1149:        );
```
