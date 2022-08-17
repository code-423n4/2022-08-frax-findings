**Overview**

Risk Rating | Number of issues
--- | ---
Gas Issues | 13

**Table of Contents:**

- [1. State variables only set in the constructor should be declared `immutable`](#1-state-variables-only-set-in-the-constructor-should-be-declared-immutable)
- [2. Multiple accesses of a mapping/array should use a local variable cache](#2-multiple-accesses-of-a-mappingarray-should-use-a-local-variable-cache)
- [3. Caching storage values in memory](#3-caching-storage-values-in-memory)
- [4. Use `calldata` instead of `memory`](#4-use-calldata-instead-of-memory)
- [5. Reduce the size of error messages (Long revert Strings)](#5-reduce-the-size-of-error-messages-long-revert-strings)
- [6. Multiple address mappings can be combined in a struct](#6-multiple-address-mappings-can-be-combined-in-a-struct)
- [7. Splitting `require()` statements that use `&&` saves gas](#7-splitting-require-statements-that-use--saves-gas)
- [8. `<array>.length` should not be looked up in every loop of a `for-loop`](#8-arraylength-should-not-be-looked-up-in-every-loop-of-a-for-loop)
- [9. `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`)](#9-i-costs-less-gas-compared-to-i-or-i--1-same-for---i-vs-i---or-i---1)
- [10. Increments/decrements can be unchecked in for-loops](#10-incrementsdecrements-can-be-unchecked-in-for-loops)
- [11. It costs more gas to initialize variables with their default value than letting the default value be applied](#11-it-costs-more-gas-to-initialize-variables-with-their-default-value-than-letting-the-default-value-be-applied)
- [12. Use Custom Errors instead of Revert Strings to save Gas](#12-use-custom-errors-instead-of-revert-strings-to-save-gas)
- [13. (Not recommended, but true) Functions guaranteed to revert when called by normal users can be marked `payable`](#13-not-recommended-but-true-functions-guaranteed-to-revert-when-called-by-normal-users-can-be-marked-payable)

## 1. State variables only set in the constructor should be declared `immutable`

Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around **20 000 gas** per variable) and replace the expensive storage-reading operations (around **2100 gas** per reading) to a less expensive value reading (**3 gas**)

```solidity
FraxlendPairDeployer.sol:57:    address public CIRCUIT_BREAKER_ADDRESS;
FraxlendPairDeployer.sol:58:    address public COMPTROLLER_ADDRESS;
FraxlendPairDeployer.sol:59:    address public TIME_LOCK_ADDRESS;
```

## 2. Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed multiple times saves **~42 gas per access** due to not having to perform the same offset calculation every time.

Affected code:

- `_addresses[i]` (calldata)

```solidity
FraxlendPairDeployer.sol:154:                _address: _addresses[i],
FraxlendPairDeployer.sol:155:                _isCustom: deployedPairCustomStatusByAddress[_addresses[i]]
```

```solidity
FraxlendWhitelist.sol:52:            oracleContractWhitelist[_addresses[i]] = _bool;
FraxlendWhitelist.sol:53:            emit SetOracleWhitelist(_addresses[i], _bool);
```

```solidity
FraxlendWhitelist.sol:67:            rateContractWhitelist[_addresses[i]] = _bool;
FraxlendWhitelist.sol:68:            emit SetRateContractWhitelist(_addresses[i], _bool);
```

```solidity
FraxlendWhitelist.sol:82:            fraxlendDeployerWhitelist[_addresses[i]] = _bool;
FraxlendWhitelist.sol:83:            emit SetFraxlendDeployerWhitelist(_addresses[i], _bool);
```

- `_lenders[i]` (calldata)

```solidity
FraxlendPair.sol:291:            if (_approval || _lenders[i] != msg.sender) {
FraxlendPair.sol:292:                approvedLenders[_lenders[i]] = _approval;
FraxlendPair.sol:293:                emit SetApprovedLender(_lenders[i], _approval);
```

- `_borrowers[i]` (calldata)

```solidity
FraxlendPair.sol:310:            if (_approval || _borrowers[i] != msg.sender) {
FraxlendPair.sol:311:                approvedBorrowers[_borrowers[i]] = _approval;
FraxlendPair.sol:312:                emit SetApprovedBorrower(_borrowers[i], _approval);
```

## 3. Caching storage values in memory

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Here, we can save 1 SLOAD for reading `DEFAULT_MAX_LTV` and 1 SLOAD for reading `DEFAULT_LIQ_FEE`

```solidity
src/contracts/FraxlendPairDeployer.sol:
  332:             DEFAULT_MAX_LTV, //@audit SLOAD 1 (DEFAULT_MAX_LTV)
  333:             DEFAULT_LIQ_FEE,//@audit SLOAD 1 (DEFAULT_LIQ_FEE)
  342:         _logDeploy(_name, _pairAddress, _configData, DEFAULT_MAX_LTV, DEFAULT_LIQ_FEE, 0);//@audit SLOAD 2 (DEFAULT_MAX_LTV) and SLOAD 2 (DEFAULT_LIQ_FEE)
```

## 4. Use `calldata` instead of `memory`

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly bypasses this loop.

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gas-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Affected code (around **60 gas** per occurrence):

```solidity
FraxlendPairDeployer.sol:310:    function deploy(bytes memory _configData) external returns (address _pairAddress) {
FraxlendPairDeployer.sol:398:    function globalPause(address[] memory _addresses) external returns (address[] memory _updatedAddresses) {
```

## 5. Reduce the size of error messages (Long revert Strings)

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

Revert strings > 32 bytes:

```solidity
FraxlendPairDeployer.sol:205:            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
FraxlendPairDeployer.sol:228:            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
FraxlendPairDeployer.sol:253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
FraxlendPairDeployer.sol:365:        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
FraxlendPairDeployer.sol:368:            "FraxlendPairDeployer: Only whitelisted addresses"
LinearInterestRate.sol:59:            "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
LinearInterestRate.sol:63:            "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
LinearInterestRate.sol:67:            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
```

Consider shortening the revert strings to fit in 32 bytes.

## 6. Multiple address mappings can be combined in a struct

Computing storage costs **~42 gas**, and this could be saved per access due to not having to recalculate the key's keccak256 hash.

```solidity
FraxlendPairCore.sol:127:    mapping(address => uint256) public userCollateralBalance; // amount of collateral each user is backed
FraxlendPairCore.sol:129:    mapping(address => uint256) public userBorrowShares; // represents the shares held by individuals
```

## 7. Splitting `require()` statements that use `&&` saves gas

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper

Affected code (saving around **3 gas** per instance):

```solidity
LinearInterestRate.sol:58:            _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
LinearInterestRate.sol:62:            _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
LinearInterestRate.sol:66:            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
```

## 8. `<array>.length` should not be looked up in every loop of a `for-loop`

Reading array length at each iteration of the loop consumes more gas than necessary.
  
In the best case scenario (length read on a memory variable), caching the array length in the stack saves around **3 gas** per iteration.
In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Here, consider storing the array's length in a variable before the for-loop, and use this new variable instead:

```solidity
FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
FraxlendPairCore.sol:265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
FraxlendPairCore.sol:270:        for (uint256 i = 0; i < _approvedLenders.length; ++i) {
FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
```

## 9. `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`)

Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

- `i += 1` is the most expensive form
- `i++` costs 6 gas less than `i += 1`
- `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

- `i -= 1` is the most expensive form
- `i--` costs 11 gas less than `i -= 1`
- `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name *post-increment*:

```solidity
uint i = 1;  
uint j = 2;
require(j == i++, "This will be false as i is incremented after the comparison");
```
  
However, pre-increments (or pre-decrements) return the new value:
  
```solidity
uint i = 1;  
uint j = 2;
require(j == ++i, "This will be true as i is incremented before the comparison");
```
  
In the pre-increment case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`.  
  
Affected code:  

```solidity
libraries/SafeERC20.sol:24:                i++;
libraries/SafeERC20.sol:27:            for (i = 0; i < 32 && data[i] != 0; i++) {
FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
FraxlendPairDeployer.sol:130:                i++;
FraxlendPairDeployer.sol:158:                i++;
FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
```

Consider using pre-increments and pre-decrements where they are relevant (meaning: not where post-increments/decrements logic are relevant).

## 10. Increments/decrements can be unchecked in for-loops

In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.  
  
[ethereum/solidity#10695](https://github.com/ethereum/solidity/issues/10695)

Consider wrapping with an `unchecked` block here (around **25 gas saved** per instance):

```solidity
libraries/SafeERC20.sol:27:            for (i = 0; i < 32 && data[i] != 0; i++) {
FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
FraxlendPairCore.sol:265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
FraxlendPairCore.sol:270:        for (uint256 i = 0; i < _approvedLenders.length; ++i) {
FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
```

The change would be:  
  
```diff
- for (uint256 i; i < numIterations; i++) {
+ for (uint256 i; i < numIterations;) {
 // ...  
+   unchecked { ++i; }
}  
```

The same can be applied with decrements (which should use `break` when `i == 0`).

The risk of overflow is non-existent for `uint256` here.

## 11. It costs more gas to initialize variables with their default value than letting the default value be applied

If a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for address...). Explicitly initializing it with its default value is an anti-pattern and wastes gas (around **3 gas** per instance).

Affected code:

```solidity
libraries/SafeERC20.sol:22:            uint8 i = 0;
FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
FraxlendPairCore.sol:265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
FraxlendPairCore.sol:270:        for (uint256 i = 0; i < _approvedLenders.length; ++i) {
FraxlendPairDeployer.sol:402:        for (uint256 i = 0; i < _lengthOfArray; ) {
FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
```

Consider removing explicit initializations for default values.

## 12. Use Custom Errors instead of Revert Strings to save Gas

Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

Additionally, custom errors can be used inside and outside of contracts (including interfaces and libraries).

Source: <https://blog.soliditylang.org/2021/04/21/custom-errors/>:
> Starting from [Solidity v0.8.4](https://github.com/ethereum/solidity/releases/tag/v0.8.4), there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., `revert("Insufficient funds.");`), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Consider replacing **all revert strings** with custom errors in the solution, and particularly those that have multiple occurrences:

```solidity
FraxlendPairDeployer.sol:205:            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
FraxlendPairDeployer.sol:228:            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
FraxlendPairDeployer.sol:253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
FraxlendPairDeployer.sol:365:        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
FraxlendPairDeployer.sol:366:        require(
FraxlendPairDeployer.sol:399:        require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");
LinearInterestRate.sol:57:        require(
LinearInterestRate.sol:61:        require(
LinearInterestRate.sol:65:        require(
```

## 13. (Not recommended, but true) Functions guaranteed to revert when called by normal users can be marked `payable`

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

```solidity
FraxlendPair.sol:204:    function setTimeLock(address _newAddress) external onlyOwner {
FraxlendPair.sol:234:    function withdrawFees(uint128 _shares, address _recipient) external onlyOwner returns (uint256 _amountToTransfer) {
FraxlendPair.sol:274:    function setSwapper(address _swapper, bool _approval) external onlyOwner {
FraxlendPairDeployer.sol:170:    function setCreationCode(bytes calldata _creationCode) external onlyOwner {
FraxlendWhitelist.sol:50:    function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
FraxlendWhitelist.sol:65:    function setRateContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
FraxlendWhitelist.sol:80:    function setFraxlendDeployerWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
```
