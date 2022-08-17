### Use defualt value rather than overwriite variable with their default value.

Overriting varibles with defualt values with their default value will waste only gas and not necessary.

_There are **9** instances of this issue:_

```solidity
File: ./src/contracts/FraxlendPair.sol::

289:     for (uint256 i = 0; i < _lenders.length; i++) {

308:     for (uint256 i = 0; i < _borrowers.length; i++) {
```

```solidity
File: ./src/contracts/FraxlendPairCore.sol

265:     for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {

270:     for (uint256 i = 0; i < _approvedLenders.length; ++i) {
```

```solidity
File: ./src/contracts/FraxlendPairDeployer.sol

402:     for (uint256 i = 0; i < _lengthOfArray; ) {
```

```solidity
File: ./src/contracts/FraxlendWhitelist.sol

51:     for (uint256 i = 0; i < _addresses.length; i++) {

66:     for (uint256 i = 0; i < _addresses.length; i++) {

81:     for (uint256 i = 0; i < _addresses.length; i++) {
```

```solidity
File: ./src/contracts/libraries/SafeERC20.sol

22: uint8 i = 0;
```

### Use `unchecked{++i}`/`unchecked{i++}` rather than this `++i`/`i++` in `for`-loops

Using a `unchecked{++i}`/`unchecked{i++}` is recommended in for loops if underflow and overflow is not possible. This will save some gas because the compiler woudn't make a safety check. This safety checks is came from version 0.8.0 and that applies also to above versions.

_There are **8** instances of this issue:_

```solidity
File: ./src/contracts/FraxlendPair.sol::

289:     for (uint256 i = 0; i < _lenders.length; i++) {

308:     for (uint256 i = 0; i < _borrowers.length; i++) {
```

```solidity
File: ./src/contracts/FraxlendPairCore.sol

265:     for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {

270:     for (uint256 i = 0; i < _approvedLenders.length; ++i) {
```

```solidity
File: ./src/contracts/libraries/SafeERC20.sol

27:     for (i = 0; i < 32 && data[i] != 0; i++) {
```

```solidity
File: ./src/contracts/FraxlendWhitelist.sol

51:     for (uint256 i = 0; i < _addresses.length; i++) {

66:     for (uint256 i = 0; i < _addresses.length; i++) {

81:     for (uint256 i = 0; i < _addresses.length; i++) {
```

### `++i`/`--i` are more cheap operations than `i++`/`i--`

Using a `++i`/`--i` operations can save about 6 gas for loop/instance because compiler will make less operations

_There are **6** instances of this issue:_

```solidity
File: ./src/contracts/FraxlendPair.sol::

289:     for (uint256 i = 0; i < _lenders.length; i++) {

308:     for (uint256 i = 0; i < _borrowers.length; i++) {
```

```solidity
File: ./src/contracts/FraxlendWhitelist.sol

51:     for (uint256 i = 0; i < _addresses.length; i++) {

66:     for (uint256 i = 0; i < _addresses.length; i++) {

81:     for (uint256 i = 0; i < _addresses.length; i++) {
```

```solidity
File: ./src/contracts/libraries/SafeERC20.sol

27:     for (i = 0; i < 32 && data[i] != 0; i++) {
```

### In for loop use outside variable for array length

For loop written like this`for (uint256 i; i < array.length; ++i) {` will cost more gas than `for (uint256 i; i < _lengthOfArray; ++i) {` because for every iteration we use mload and memory_offset
that will cost about 6 gas

_There are **7** instances of this issue:_

```solidity
File: ./src/contracts/FraxlendPair.sol

289:    for (uint256 i = 0; i < _lenders.length; i++) {

308:    for (uint256 i = 0; i < _borrowers.length; i++) {
```

```solidity
File: ./src/contracts/FraxlendPairCore.sol:

265:    for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {

270:    for (uint256 i = 0; i < _approvedLenders.length; ++i) {
```

```solidity
File: ./src/contracts/FraxlendWhitelist.sol

51:     for (uint256 i = 0; i < _addresses.length; i++) {

66:     for (uint256 i = 0; i < _addresses.length; i++) {

81:     for (uint256 i = 0; i < _addresses.length; i++) {
```

### Use > 0 instead of != 0 for Unsigned Integer Comparison

` > 0` will save about 3 gas per loop/instance when used on unsigned integers

_There are **3** instances of this issue:_

```solidity
File: ./src/contracts/FraxlendPairCore.sol:

257:     if (bytes(nameOfContract).length != 0) {
```

```solidity
File: ./src/contracts/libraries/SafeERC20.sol

23:     while (i < 32 && data[i] != 0) {

27:     for (i = 0; i < 32 && data[i] != 0; i++) {
```

### If you not use named return variables when functions returns this will wastes deployment gas because of overhead.

_There are **13** instances of this issue:_

```solidity
File: ./src/contracts/VariableInterestRate.sol

47:     return "Variable Time-Weighted Interest Rate";
```

```solidity
File: ./src/contracts/FraxlendPair.sol

81:     return string(abi.encodePacked("FraxlendV1 - ", collateralContract.safeSymbol(), "/",

85:     return 18;

90:     return totalAsset.shares;

97:     return address(assetContract);

101:    return totalAsset.amount;

121:    return totalAsset.toShares(_amount, false);

125:    return totalAsset.toAmount(_shares, true);

129:    return totalAsset.toShares(_amount, true);

133:    return totalAsset.toAmount(_shares, false);

145:    return totalAsset.toAmount(balanceOf(owner), false);

184:    return totalBorrow.toShares(_amount, _roundUp);

191:    return totalBorrow.toAmount(_shares, _roundUp);
```

```solidity
File: ./src/contracts/FraxlendPairCore.sol
300:    return _totalAsset.amount - _totalBorrow.amount;
321:    return maturityDate != 0 && block.timestamp > maturityDate;
```

### Long require()/revert() strings cost more gas.

String in `require()` and `revert()` that are bigger than 32 bytes (256 bits) will cost 3 gas for each memory work.

_There are **5** instances of this issue:_

```solidity
File: ./src/contracts/FraxlendPairDeployer.sol

205:  require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");

228:  require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
```

```solidity
File: ./src/contracts/LinearInterestRate.sol

57:       require(
58:        _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
59:       "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest 60:   >= MIN_INT"
60;       );

61:       require(
62:          _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,"LinearInterestRate: 
63:        _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
64:        );

65        require(
66            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
67            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
68        );
```

### Use custom errors rather than `revert()`/`require()` strings to save gas

Custom errors are available from solidity 0.8.4 and above. The code below match that and using them will save gas  each time they're hit by avoiding having to allocate and store the revert string.

_There are **5** instances of this issue:_

```solidity
File: ./src/contracts/FraxlendPairDeployer.sol

205:  require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");

228:  require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
```

```solidity
File: ./src/contracts/LinearInterestRate.sol

57:       require(
58:        _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
59:       "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest 60:   >= MIN_INT"
60;       );

61:       require(
62:          _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,"LinearInterestRate: 
63:        _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
64:        );

65        require(
66            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
67            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
68        );
```

### Replace `x <= y` with `x < y + 1`, and `x >= y` with `x > y - 1`

In the EVM machine command for `<=`/`>=` doesn't exist rather than two operations are performed `>`/`<` and `=` that will cost more gas than using one comparison operator.

_There are **14** instances of this issue:_

```solidity
File: ./src/contracts/FraxlendPairCore.sol

315:         return _ltv <= maxLTV;

472:         _interestEarned + _totalBorrow.amount <= type(uint128).max &&

473:         _interestEarned + _totalAsset.amount <= type(uint128).max

525:         if (_answer <= 0) {

533:         if (_answer <= 0) {

988:         _collateralForLiquidator = _leftoverCollateral <= 0

1000:        if (_leftoverCollateral <= 0) {
```

```solidity
File:  ./src/contracts/FraxlendPairDeployer.sol

365:         require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
```

```solidity
File:  ./src/contracts/FraxlendPairHelper.sol

134:         if (_answer <= 0) {

142:         if (_answer <= 0) {

284:         _collateralForLiquidator = _leftoverCollateral <= 0

292:         if (_leftoverCollateral <= 0 && (_borrowerShares - _sharesToLiquidate) > 0) {
```
```solidity
File:  ./src/contracts/LinearInterestRate.sol

58:          _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,

62:          _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
```

### `<x> += <y>` is more expensive than `<x> = <x> + <y>` for state variables

_There are **11** instances of this issue:_

```solidity
File: ./src/contracts/FraxlendPairCore.sol

475:         _totalBorrow.amount += uint128(_interestEarned);

476:         _totalAsset.amount += uint128(_interestEarned);

484:         _totalAsset.shares += uint128(_feesShare);

566:         _totalAsset.amount += _amount;

567:         _totalAsset.shares += _shares;

718:         _totalBorrow.amount += _borrowAmount;

719:         _totalBorrow.shares += uint128(_sharesAdded);

724:         userBorrowShares[msg.sender] += _sharesAdded;

772:         userCollateralBalance[_borrower] += _collateralAmount;

773:         totalCollateral += _collateralAmount;
```

```solidity
File ./src/test/e2e/BasePairTest.sol

539:          _sumOfInt += _interestEarned;
```