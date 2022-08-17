# Gas Report

## Use `!= 0` instead of `> 0` for Unsigned Integer Comparison

### Locations:

```solidity
FraxlendPairCore.sol::477 => if (_currentRateInfo.feeToProtocolRate > 0) {
FraxlendPairCore.sol::754 => if (_collateralAmount > 0) {
FraxlendPairCore.sol::835 => if (userBorrowShares[msg.sender] > 0) {
FraxlendPairCore.sol::1002 => if (_leftoverBorrowShares > 0) {
FraxlendPairCore.sol::1094 => if (_initialCollateralAmount > 0) {
FraxlendPairDeployer.sol::379 => _approvedBorrowers.length > 0,
FraxlendPairDeployer.sol::380 => _approvedLenders.length > 0
FraxlendPairHelper.sol::235 => if (_feeToProtocolRate > 0) {
FraxlendPairHelper.sol::292 => if (_leftoverCollateral <= 0 && (_borrowerShares - _sharesToLiquidate) > 0) {
LinearInterestRate.sol::66 => _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
```

### Suggestion:

It is cheaper to deal with unsigned integers by using `!= 0` than `> 0`.

---

## Unspecific Compiler Version Pragma

### Locations:

```solidity
FraxlendPair.sol::2 => pragma solidity ^0.8.15;
FraxlendPairConstants.sol::2 => pragma solidity ^0.8.15;
FraxlendPairCore.sol::2 => pragma solidity ^0.8.15;
FraxlendPairDeployer.sol::2 => pragma solidity ^0.8.15;
FraxlendPairHelper.sol::2 => pragma solidity ^0.8.15;
FraxlendWhitelist.sol::2 => pragma solidity ^0.8.15;
LinearInterestRate.sol::2 => pragma solidity ^0.8.15;
VariableInterestRate.sol::2 => pragma solidity ^0.8.15;
interfaces/IERC4626.sol::2 => pragma solidity >=0.8.15;
interfaces/IFraxlendPair.sol::2 => pragma solidity >=0.8.15;
interfaces/IFraxlendWhitelist.sol::2 => pragma solidity >=0.8.15;
interfaces/IRateCalculator.sol::2 => pragma solidity >=0.8.15;
interfaces/ISwapper.sol::2 => pragma solidity >=0.8.15;
libraries/SafeERC20.sol::2 => pragma solidity ^0.8.15;
libraries/VaultAccount.sol::2 => pragma solidity ^0.8.15;
```

### Suggestion:

It is suggested to use a concrete compiler version. This is because a new version compiler may be vulnerable and cause fall back in older version of compiler.

---

## `++i` costs less than `i++` (same for `--i/i--`)

### Locations:

```solidity
FraxlendPair.sol::289 => for (uint256 i = 0; i < _lenders.length; i++) {
FraxlendPair.sol::308 => for (uint256 i = 0; i < _borrowers.length; i++) {
FraxlendWhitelist.sol::51 => for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol::66 => for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol::81 => for (uint256 i = 0; i < _addresses.length; i++) {
SafeERC20.sol::27 => for (i = 0; i < 32 && data[i] != 0; i++) {
```

### Suggestion:

Save 6 gas per loop.

---

## Splitting `require()` which uses `&&` saves gas

### Locations:

```solidity
LinearInterestRate.sol::57-68

require(
    _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
    "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
);
require(
    _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
    "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
);
require(
    _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
    "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
);
```

### Suggestion:

Instead of using operator && on single `require` check, using double or more `require` check can save more gas. Here is an example [issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128).