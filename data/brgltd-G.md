# [G-01] Prefix increment costs less gas than postfix increment

There are 9 instances of this issue.

```
File: src/contracts/FraxlendPair.sol
289: for (uint256 i = 0; i < _lenders.length; i++) {
308: for (uint256 i = 0; i < _borrowers.length; i++) {
```

```
File: src/contracts/FraxlendPairDeployer.sol
130: i++;
158: i++;
```

```
File: src/contracts/FraxlendWhitelist.sol
51: for (uint256 i = 0; i < _addresses.length; i++) {
66: for (uint256 i = 0; i < _addresses.length; i++) {
81: for (uint256 i = 0; i < _addresses.length; i++) {
```

```
File: src/contracts/libraries/SafeERC20.sol
24: i++;
27: for (i = 0; i < 32 && data[i] != 0; i++) {
```

# [G-02] The increment in for loop post condition can be made unchecked to save gas

There are 8 instances of this issue.

```
File: src/contracts/FraxlendPair.sol
289: for (uint256 i = 0; i < _lenders.length; i++) {
308: for (uint256 i = 0; i < _borrowers.length; i++) {
```

```
File: src/contracts/FraxlendPairCore.sol
265: for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
270: for (uint256 i = 0; i < _approvedLenders.length; ++i) {
```

```
File: src/contracts/FraxlendWhitelist.sol
51: for (uint256 i = 0; i < _addresses.length; i++) {
66: for (uint256 i = 0; i < _addresses.length; i++) {
81: for (uint256 i = 0; i < _addresses.length; i++) {
```

```
File: src/contracts/libraries/SafeERC20.sol
27: for (i = 0; i < 32 && data[i] != 0; i++) {
```

# [G-03] Cache the length of the array before the loop

There are 7 instances of this issue.

```
File: src/contracts/FraxlendPair.sol
289: for (uint256 i = 0; i < _lenders.length; i++) {
308: for (uint256 i = 0; i < _borrowers.length; i++) {
```

```
File: src/contracts/FraxlendPairCore.sol
265: for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
270: for (uint256 i = 0; i < _approvedLenders.length; ++i) {
```

```
File: src/contracts/FraxlendWhitelist.sol
51: for (uint256 i = 0; i < _addresses.length; i++) {
66: for (uint256 i = 0; i < _addresses.length; i++) {
81: for (uint256 i = 0; i < _addresses.length; i++) {
```

# [G-04] Initializing a variable with the default value wastes gas

There are 9 instances of this issue.

```
File: src/contracts/FraxlendPair.sol
289: for (uint256 i = 0; i < _lenders.length; i++) {
308: for (uint256 i = 0; i < _borrowers.length; i++) {
```

```
File: src/contracts/FraxlendPairCore.sol
265: for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
270: for (uint256 i = 0; i < _approvedLenders.length; ++i) {
```

```
File: src/contracts/FraxlendPairDeployer.sol
402: for (uint256 i = 0; i < _lengthOfArray; ) {
```

```
File: src/contracts/FraxlendWhitelist.sol
51: for (uint256 i = 0; i < _addresses.length; i++) {
66: for (uint256 i = 0; i < _addresses.length; i++) {
81: for (uint256 i = 0; i < _addresses.length; i++) {
```

```
File: src/contracts/libraries/SafeERC20.sol
22: uint8 i = 0;
```

# [G-05] Use != 0 instead of > 0 to save gas.

Replace `> 0` with `!= 0` for unsigned integers.

There are 7 instances of this issue.

```
File: src/contracts/FraxlendPairCore.sol
477: if (_currentRateInfo.feeToProtocolRate > 0) {
754: if (_collateralAmount > 0) {
835: if (userBorrowShares[msg.sender] > 0) {
1002: if (_leftoverBorrowShares > 0) {
1094: if (_initialCollateralAmount > 0) {
```

```
File: src/contracts/LinearInterestRate.sol
66: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
67: "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
```

# [G-06] Use custom errors rather than require strings to save gas

There are 5 instances of this issue.

```
File: src/contracts/FraxlendPairDeployer.sol
205: require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
228: require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
253: require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
365: require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
399: require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");
```

# [G-07] Long revert strings are gas wasteful

Custom errors and NatSpec comments can also provide rich information without wasting gas.

There are 4 instances of this issue.

```
205: require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
228: require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
253: require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
365: require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
```

# [G-08] x += y and x -= y costs more gas than x = x + y and x = x - y for state variables

Threre are 7 instances with this issue.

```
724: userBorrowShares[msg.sender] += _sharesAdded;
772: userCollateralBalance[_borrower] += _collateralAmount;
773: totalCollateral += _collateralAmount;
813: userCollateralBalance[_borrower] -= _collateralAmount;
815: totalCollateral -= _collateralAmount;
867: userBorrowShares[_borrower] -= _shares;
1008: totalAsset.amount -= _amountToAdjust;
```
