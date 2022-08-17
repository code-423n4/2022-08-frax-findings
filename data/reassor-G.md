# List

1. Cache array length outside of loop
2. Long revert error messages
3. Use custom errors instead of revert strings to save gas
4. ++i/--i costs less gas compared to i++, i += 1, i-- or i -= 1
5. Obsolete overflow/underflow check
6. No need to explicitly initialize variables with default values
7. Use != 0 instead of > 0 for unsigned integer comparison


# 1. Cache array length outside of loop
## Impact
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

## Proof of Concept
```
FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
FraxlendPairCore.sol:265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
FraxlendPairCore.sol:270:        for (uint256 i = 0; i < _approvedLenders.length; ++i) {
FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
```

## Recommended Mitigation Steps
It is recommended to cache the array length outside of for loop.


# 2. Long revert error messages
## Impact
Shortening revert error messages to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

## Proof of Concept
```
FraxlendPairDeployer.sol:205:            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
FraxlendPairDeployer.sol:228:            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
FraxlendPairDeployer.sol:253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
FraxlendPairDeployer.sol:365:        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
FraxlendPairDeployer.sol:366:        require(
            IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
            "FraxlendPairDeployer: Only whitelisted addresses"
        );
LinearInterestRate.sol:52:    function requireValidInitData(bytes calldata _initData) public pure {
LinearInterestRate.sol:57:        require(
            _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
            "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
        );
LinearInterestRate.sol:61:        require(
            _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
            "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
        );
LinearInterestRate.sol:65:        require(
        require(
            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
        );
```

## Tools Used
Manual Review / VSCode

## Recommended Mitigation Steps
It is recommended to decrease revert messages to maximum 32 bytes.


# 3. Use custom errors instead of revert strings to save gas
## Impact
Usage of custom errors reduces the gas cost.

## Proof of Concept
Contracts that should be using custom errors:
```
FraxlendPairCore.sol
FraxlendPairDeployer.sol
LinearInterestRate.sol
VariableInterestRate.sol
```

## Recommended Mitigation Steps
It is recommended to add custom errors to listed contracts.


# 4. ++i/--i costs less gas compared to i++, i += 1, i-- or i -= 1
## Impact
`++i` or `--i` costs less gas compared to `i++`, `i += 1`, `i--` or `i -= 1` for unsigned integer as pre-increment/pre-decrement is cheaper (about 5 gas per iteration).

## Proof of Concept
```
FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
FraxlendPairDeployer.sol:130:                i++;
FraxlendPairDeployer.sol:158:                i++;
FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
libraries/SafeERC20.sol:24:                i++;
libraries/SafeERC20.sol:27:            for (i = 0; i < 32 && data[i] != 0; i++) {
```

## Recommended Mitigation Steps
It is recommended to use `++i` or `--i` instead of `i++`, `i += 1`, `i--` or `i -= 1` to increment value of an unsigned integer variable.


# 5. Obsolete overflow/underflow check
## Impact
Starting from solidity `0.8.0` there is built-in check for overflows/underflows. This mechanism automatically checks if the variable overflows or underflows and throws an error. Multiple contracts use increments that cannot overflow but consume additional gas for checks.

## Proof of Concept
```
FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
FraxlendPairCore.sol:265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
FraxlendPairCore.sol:270:        for (uint256 i = 0; i < _approvedLenders.length; ++i) {
FraxlendPairDeployer.sol:130:                i++;
FraxlendPairDeployer.sol:158:                i++;
FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
libraries/SafeERC20.sol:24:                i++;
libraries/SafeERC20.sol:27:            for (i = 0; i < 32 && data[i] != 0; i++) {
```

## Recommended Mitigation Steps
It is recommended to wrap incrementing  with `unchecked` block, for example: `unchecked { ++i }` or `unchecked { --i }`


# 6. No need to explicitly initialize variables with default values
## Impact
If a variable is not set/initialized, it is assumed to have the default value (`0` for uint, `false` for bool, `address(0)` for addresses). Explicitly initializing it with its default value is an anti-pattern and waste of gas.

## Proof of Concept
```
FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
FraxlendPairConstants.sol:47:    uint16 internal constant DEFAULT_PROTOCOL_FEE = 0; // 1e5 precision
FraxlendPairCore.sol:265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
FraxlendPairCore.sol:270:        for (uint256 i = 0; i < _approvedLenders.length; ++i) {
FraxlendPairDeployer.sol:402:        for (uint256 i = 0; i < _lengthOfArray; ) {
FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
LinearInterestRate.sol:33:    uint256 private constant MIN_INT = 0; // 0.00% annual rate
libraries/SafeERC20.sol:22:            uint8 i = 0;
```

## Recommended Mitigation Steps
It is recommended to remove explicit initializations with default values.


# 7. Use != 0 instead of > 0 for unsigned integer comparison
## Impact
When dealing with unsigned integer types, comparisons with `!= 0` are cheaper than with `> 0`.

## Proof of Concept
```
FraxlendPairCore.sol:477:                if (_currentRateInfo.feeToProtocolRate > 0) {
FraxlendPairCore.sol:754:        if (_collateralAmount > 0) {
FraxlendPairCore.sol:835:        if (userBorrowShares[msg.sender] > 0) {
FraxlendPairCore.sol:1002:                if (_leftoverBorrowShares > 0) {
FraxlendPairCore.sol:1094:        if (_initialCollateralAmount > 0) {
FraxlendPairDeployer.sol:379:            _approvedBorrowers.length > 0,
FraxlendPairDeployer.sol:380:            _approvedLenders.length > 0
LinearInterestRate.sol:66:            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
```

## Recommended Mitigation Steps
It is recommended to use `!= 0` instead of `> 0`.

