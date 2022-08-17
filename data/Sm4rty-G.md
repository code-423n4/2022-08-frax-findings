
## 1. An array’s length should be cached to save gas in for-loops

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.

### Instances:
[FraxlendWhitelist.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol)
```
src/contracts/FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
```

[FraxlendPairCore.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol)
```
src/contracts/FraxlendPairCore.sol:265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
src/contracts/FraxlendPairCore.sol:270:        for (uint256 i = 0; i < _approvedLenders.length; ++i) {
```
[FraxlandPair.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol)
```
src/contracts/FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
src/contracts/FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {

```

### Remediation:
Here, I suggest storing the array’s length in a variable before the for-loop, and use it instead. 

--------


## 2. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)


### Instances:
[FraxlendWhitelist.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol)
```
src/contracts/FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
```
[SafeERC20.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol)
```
src/contracts/libraries/SafeERC20.sol:27:            for (i = 0; i < 32 && data[i] != 0; i++) {
```
[FraxlandPair.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol)
```
src/contracts/FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
src/contracts/FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {

```

### Reference:
https://code4rena.com/reports/2022-05-cally#g-11-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops

-----


## 3. Use calldata instead of memory

Use calldata instead of memory to save gas. See here for reference:
https://code4rena.com/reports/2022-04-badger-citadel/#g-12-stakedcitadeldepositall-use-calldata-instead-of-memory

### Instances:
[FraxlendPairDeployer.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol)
```
src/contracts/FraxlendPairDeployer.sol:310:    function deploy(bytes memory _configData) external returns (address _pairAddress) {
src/contracts/FraxlendPairDeployer.sol:398:    function globalPause(address[] memory _addresses) external returns (address[] memory _updatedAddresses)
```

-----

## 4. Reduce the size of error messages (Long revert Strings)

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### Instances:
[LinearInterestRate.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol)
```
57:        require(
58:            _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
59:            "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
60:        );
61:        require(
62:            _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
63:            "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
64:        );
65:        require(
66:            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
67:            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
        );

```

[FraxlendPairDeployer.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol)
```
src/contracts/FraxlendPairDeployer.sol:205:            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
src/contracts/FraxlendPairDeployer.sol:228:            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
src/contracts/FraxlendPairDeployer.sol:253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
src/contracts/FraxlendPairDeployer.sol:365:        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
src/contracts/FraxlendPairDeployer.sol:366:        require(IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender), "FraxlendPairDeployer: Only whitelisted addresses");
```

I suggest shortening the revert strings to fit in 32 bytes, or that using custom errors as described next.****

Custom errors from Solidity 0.8.4 are cheaper than revert strings
(cheaper deployment cost and runtime cost when the revert condition is
met)

Source [Custom Errors](https://blog.soliditylang.org/2021/04/21/custom-errors/) in Solidity:

----

##  5. Custom Errors instead of Revert Strings to save Gas

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

Starting from Solidity v0.8.4,there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");),but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

### Instances Includes:
[LinearInterestRate.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol)
```
57:        require(
58:            _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
59:            "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
60:        );
61:        require(
62:            _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
63:            "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
64:        );
65:        require(
66:            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
67:            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
        );

```

[FraxlendPairDeployer.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol)
```
src/contracts/FraxlendPairDeployer.sol:205:            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
src/contracts/FraxlendPairDeployer.sol:228:            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
src/contracts/FraxlendPairDeployer.sol:253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
src/contracts/FraxlendPairDeployer.sol:365:        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
src/contracts/FraxlendPairDeployer.sol:366:        require(IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender), "FraxlendPairDeployer: Only whitelisted addresses");
src/contracts/FraxlendPairDeployer.sol:399:        require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");
      
```

### References:
https://blog.soliditylang.org/2021/04/21/custom-errors/

###  Remediation: 
I suggest replacing revert strings with custom errors.

------

## 6.  Variables: No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

We can use 	`uint number;` instead of `uint number = 0;`
### Instance Includes:
[LinearInterestRate.sol:33](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L33)
[FraxlendPairConstants.sol:47](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairConstants.sol#L47)
[SafeERC20.sol:22](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L22)
```
src/contracts/LinearInterestRate.sol:33:    uint256 private constant MIN_INT = 0; // 0.00% annual rate
src/contracts/FraxlendPairConstants.sol:47:    uint16 internal constant DEFAULT_PROTOCOL_FEE = 0; // 1e5 precision
src/contracts/libraries/SafeERC20.sol:22:            uint8 i = 0;
```

### Recommendation:
I suggest removing explicit initializations for default values.

---------
## 7. abi.encode() is less efficient than abi.encodePacked()

There are 6 instances of this issue:
[FraxlendPairCore.sol:450](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L450)
[LinearInterestRate.sol:47](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L47)
[VariableInterestRate.sol:53](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L53)
```
src/contracts/FraxlendPairCore.sol:450:                bytes memory _rateData = abi.encode(
src/contracts/LinearInterestRate.sol:47:        return abi.encode(MIN_INT, MAX_INT, MAX_VERTEX_UTIL, UTIL_PREC);
src/contracts/VariableInterestRate.sol:53:        return abi.encode(MIN_UTIL, MAX_UTIL, UTIL_PREC, MIN_INT, MAX_INT, INT_HALF_LIFE);
src/contracts/FraxlendPairDeployer.sol:213:                abi.encode(
```

[FraxlendPairDeployer.sol](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol)
```
src/contracts/FraxlendPairDeployer.sol:331:            abi.encode(CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS, TIME_LOCK_ADDRESS, FRAXLEND_WHITELIST_ADDRESS),
src/contracts/FraxlendPairDeployer.sol:374:            abi.encode(CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS, TIME_LOCK_ADDRESS, FRAXLEND_WHITELIST_ADDRESS),
```

### Reference:
https://code4rena.com/reports/2022-06-notional-coop#15-abiencode-is-less-efficient-than-abiencodepacked

----
