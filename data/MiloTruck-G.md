# Gas Report

|No.|Issue|Instances|
|:-|:-|:-:|
|1|For-loops: Index initialized with default value|8|
|2|For-Loops: Cache array length outside of loops|7|
|3|For-Loops: Index increments can be left unchecked|8|
|4|Arithmetics: `++i` costs less gas compared to `i++` or `i += 1`|9|
|5|Errors: Reduce the length of error messages (long revert strings)|8|
|6|Errors: Use multiple `require` statements instead of `&&`|3|
|7|Errors: Use custom errors instead of revert strings|9|
|8|Unnecessary initialization of variables with default values|3|
|9|Storage variables should be declared `constant` when possible|4|
|10|Storage variables should be declared `immutable` when possible|4|
|11|State variables should be cached in stack variables rather than re-reading them from storage|2|
|12|`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables|2|
|13|Remove or replace unused state variables|1|
|14|Using `bool` for storage incurs overhead|9|
|15|Stack variable used to cache a state variable is only accessed once|1|
|16|Use `calldata` instead of `memory` for read-only arguments in external functions|7|
|17|Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead|35|
|18|Not using the named return variables when a function returns, wastes deployment gas|4|
|19|`abi.encode()` is less efficient than `abi.encodePacked()`|8|
|20|`keccak256()` should only need to be called on a fixed string literal once|1|

Total: **133** instances over **20** issues

## For-loops: Index initialized with default value
Uninitialized `uint` variables are assigned with a default value of `0`. 

Thus, in for-loops, explicitly initializing an index with `0` costs unnecesary gas. For example, the following code:
```js
for (uint256 i = 0; i < length; ++i) {
```
can be written without explicitly setting the index to `0`:  
```js
for (uint256 i; i < length; ++i) {
```

_There are **8** instances of this issue:_  
```solidity
src/contracts/FraxlendPairDeployer.sol:
 402:        for (uint256 i = 0; i < _lengthOfArray; ) {

src/contracts/FraxlendWhitelist.sol:
  51:        for (uint256 i = 0; i < _addresses.length; i++) {
  66:        for (uint256 i = 0; i < _addresses.length; i++) {
  81:        for (uint256 i = 0; i < _addresses.length; i++) {

src/contracts/FraxlendPair.sol:
 289:        for (uint256 i = 0; i < _lenders.length; i++) {
 308:        for (uint256 i = 0; i < _borrowers.length; i++) {

src/contracts/FraxlendPairCore.sol:
 265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
 270:        for (uint256 i = 0; i < _approvedLenders.length; ++i) {
```

## For-Loops: Cache array length outside of loops
Reading an array's length at each iteration has the following gas overheads:
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `mload` (**3 gas**)
* calldata arrays use `calldataload` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset. This would save around **3 gas** per iteration.

For example:
```js
for (uint256 i; i < arr.length; ++i) {}
```
can be changed to:
```js
uint256 len = arr.length;
for (uint256 i; i < len; ++i) {}
```

_There are **7** instances of this issue:_  
```solidity
src/contracts/FraxlendWhitelist.sol:
  51:        for (uint256 i = 0; i < _addresses.length; i++) {
  66:        for (uint256 i = 0; i < _addresses.length; i++) {
  81:        for (uint256 i = 0; i < _addresses.length; i++) {

src/contracts/FraxlendPair.sol:
 289:        for (uint256 i = 0; i < _lenders.length; i++) {
 308:        for (uint256 i = 0; i < _borrowers.length; i++) {

src/contracts/FraxlendPairCore.sol:
 265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
 270:        for (uint256 i = 0; i < _approvedLenders.length; ++i) {
```


## For-Loops: Index increments can be left unchecked
From Solidity v0.8 onwards, all arithmetic operations come with implicit overflow and underflow checks. 

In for-loops, as it is impossible for the index to overflow, index increments can be left unchecked to save **[30-40 gas](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)** per loop iteration. 

For example, the code below:
```js
for (uint256 i; i < numIterations; ++i) {  
    // ...  
}  
```
can be changed to:
```js
for (uint256 i; i < numIterations;) {  
    // ...  
    unchecked { ++i; }  
}  
```

_There are **8** instances of this issue:_  
```solidity
src/contracts/FraxlendWhitelist.sol:
  51:        for (uint256 i = 0; i < _addresses.length; i++) {
  66:        for (uint256 i = 0; i < _addresses.length; i++) {
  81:        for (uint256 i = 0; i < _addresses.length; i++) {

src/contracts/FraxlendPair.sol:
 289:        for (uint256 i = 0; i < _lenders.length; i++) {
 308:        for (uint256 i = 0; i < _borrowers.length; i++) {

src/contracts/FraxlendPairCore.sol:
 265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
 270:        for (uint256 i = 0; i < _approvedLenders.length; ++i) {

src/contracts/libraries/SafeERC20.sol:
  27:        for (i = 0; i < 32 && data[i] != 0; i++) {
```

## Arithmetics: `++i` costs less gas compared to `i++` or `i += 1`
`++i` costs less gas compared to `i++` or `i += 1` for unsigned integers, as pre-increment is cheaper (about **5 gas** per iteration). This statement is true even with the optimizer enabled.

`i++` increments `i` and returns the initial value of `i`. Which means:
```js
uint i = 1;  
i++; // == 1 but i == 2  
```
But `++i` returns the actual incremented value:
```js
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```

In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`, thus it costs more gas.

The same logic applies for `--i` and `i--`.

_There are **9** instances of this issue:_  
```solidity
src/contracts/FraxlendPairDeployer.sol:
 130:        i++;
 158:        i++;

src/contracts/FraxlendWhitelist.sol:
  51:        for (uint256 i = 0; i < _addresses.length; i++) {
  66:        for (uint256 i = 0; i < _addresses.length; i++) {
  81:        for (uint256 i = 0; i < _addresses.length; i++) {

src/contracts/FraxlendPair.sol:
 289:        for (uint256 i = 0; i < _lenders.length; i++) {
 308:        for (uint256 i = 0; i < _borrowers.length; i++) {

src/contracts/libraries/SafeERC20.sol:
  24:        i++;
  27:        for (i = 0; i < 32 && data[i] != 0; i++) {
```

## Errors: Reduce the length of error messages (long revert strings)
As seen [here](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings), revert strings longer than 32 bytes would incur an additional `mstore` (**3 gas**) for each extra 32-byte chunk. Furthermore, there are additional runtime costs for memory expansion and stack operations when the revert condition is met.

Thus, shortening revert strings to fit within 32 bytes would help to reduce runtime gas costs when the revert condition is met.

_There are **8** instances of this issue:_  
```solidity
src/contracts/FraxlendPairDeployer.sol:
 205:        require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
 228:        require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
 253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
 365:        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");

 366:        require(
 367:            IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
 368:            "FraxlendPairDeployer: Only whitelisted addresses"
 369:        );

src/contracts/LinearInterestRate.sol:
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
  68:        );
```

## Errors: Use multiple `require` statements instead of `&&`
Instead of using a single `require` statement with the `&&` operator, use multiple `require` statements. With reference to [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128), this helps to reduce runtime gas cost at the expense of a larger deployment gas cost, which becomes cheaper with enough runtime calls.

A `require` statement can be split as such:
```js
// Original code:
require(a && b, 'error');

// Changed to:
require(a, 'error: a');
require(b, 'error: b');
```

_There are **3** instances of this issue:_  
```solidity
src/contracts/LinearInterestRate.sol:
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
  68:        );
```

## Errors: Use custom errors instead of revert strings
Since Solidity v0.8.4, custom errors can be used rather than `require` statements.

Taken from [Custom Errors in Solidity](https://blog.soliditylang.org/2021/04/21/custom-errors/):
> Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., `revert("Insufficient funds.");`), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors reduce runtime gas costs as they save about **50 gas** each time they are hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Furthermore, not definiting error strings also help to reduce deployment gas costs. 

_There are **9** instances of this issue:_  
```solidity
src/contracts/FraxlendPairDeployer.sol:
 205:        require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
 228:        require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
 253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
 365:        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");

 366:        require(
 367:            IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
 368:            "FraxlendPairDeployer: Only whitelisted addresses"
 369:        );

 399:        require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");

src/contracts/LinearInterestRate.sol:
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
  68:        );
```

## Unnecessary initialization of variables with default values
Uninitialized variables are assigned with a default value depending on its type:
* `uint`: `0`
* `bool`: `false`
* `address`: `address(0)`

Thus, explicitly initializing a variable with its default value costs unnecesary gas. For example, the following code:
```js
bool b = false;
address c = address(0);
uint256 a = 0;
```
can be changed to:
```js
uint256 a;
bool b;
address c;
```

_There are **3** instances of this issue:_  
```solidity
src/contracts/FraxlendPairConstants.sol:
  47:        uint16 internal constant DEFAULT_PROTOCOL_FEE = 0; // 1e5 precision

src/contracts/LinearInterestRate.sol:
  33:        uint256 private constant MIN_INT = 0; // 0.00% annual rate

src/contracts/libraries/SafeERC20.sol:
  22:        uint8 i = 0;
```

## Storage variables should be declared `constant` when possible
If a state variable is initialized with an initial value and is not modified anywhere else in the contract, it should be declared as `constant`. 

This greatly reduce gas costs as calls to `constant` variables are much cheaper than regular state variables, as seen from the [Solidity Docs](https://docs.soliditylang.org/en/v0.8.16/contracts.html#constant-and-immutable-state-variables):
> Compared to regular state variables, the gas costs of constant and immutable variables are much lower. Immutable variables are evaluated once at construction time and their value is copied to all the places in the code where they are accessed.

This saves gas as it replaces each Gwarmacces (**100 gas**) with a `PUSH32` (**3 gas**).

_There are **4** instances of this issue:_  
```solidity
src/contracts/FraxlendPairDeployer.sol:
  49:        uint256 public DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision
  50:        uint256 public GLOBAL_MAX_LTV = 1e8; // 1000x (100,000%) with 1e5 precision, protects from rounding errors in LTV calc
  51:        uint256 public DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision

src/contracts/FraxlendPairCore.sol:
  51:        string public version = "1.0.0";
```

## Storage variables should be declared `immutable` when possible
If a storage variable is assigned only in the constructor, it should be declared as `immutable`. 

This would help to reduce gas costs as calls to `immutable` variables are much cheaper than regular state variables, as seen from the [Solidity Docs](https://docs.soliditylang.org/en/v0.8.13/contracts.html#constant-and-immutable-state-variables):
> Compared to regular state variables, the gas costs of constant and immutable variables are much lower. Immutable variables are evaluated once at construction time and their value is copied to all the places in the code where they are accessed.

This helps to save gas as it avoids a Gsset (**20000 gas**) in the constructor, and replaces each Gwarmacces (**100 gas**) with a `PUSH32` (**3 gas**).

_There are **4** instances of this issue:_  
```solidity
src/contracts/FraxlendPairDeployer.sol:
  57:        address public CIRCUIT_BREAKER_ADDRESS;
  58:        address public COMPTROLLER_ADDRESS;
  59:        address public TIME_LOCK_ADDRESS;

src/contracts/FraxlendPairCore.sol:
  86:        address public TIME_LOCK_ADDRESS;
```

## State variables should be cached in stack variables rather than re-reading them from storage
If a state variable is read from storage multiple times in a function, it should be cached in a stack variable.

Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

_There are **2** instances of this issue:_  
`DEFAULT_MAX_LTV` in `deploy()`:
```solidity
src/contracts/FraxlendPairDeployer.sol:
 332:        DEFAULT_MAX_LTV,
 342:        _logDeploy(_name, _pairAddress, _configData, DEFAULT_MAX_LTV, DEFAULT_LIQ_FEE, 0);
```
`DEFAULT_LIQ_FEE` in `deploy()`:
```solidity
src/contracts/FraxlendPairDeployer.sol:
 333:        DEFAULT_LIQ_FEE,
 342:        _logDeploy(_name, _pairAddress, _configData, DEFAULT_MAX_LTV, DEFAULT_LIQ_FEE, 0);
```

## `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
The above also applies to state variables that use `-=`.

_There are **2** instances of this issue:_  
```solidity
src/contracts/FraxlendPairCore.sol:
 773:        totalCollateral += _collateralAmount;
 815:        totalCollateral -= _collateralAmount;
```

## Remove or replace unused state variables
For initialized variables, this would save a storage slot and reduce gas costs as follows:
* If the variable is assigned a non-zero value, saves Gsset (**20000 gas**). 
* If it's assigned a zero value, saves Gsreset (**2900 gas**). 

For uninitialized variables, there is no gas savings, but they should be removed to prevent confusion. 

If the state variable is overriding an interface's public function, mark the variable as `constant` or `immutable` so that it does not use a storage slot, and manually add a getter function.

_There is **1** instance of this issue:_  
```solidity
src/contracts/FraxlendPairCore.sol:
  51:        string public version = "1.0.0";
```

## Using `bool` for storage incurs overhead
Declaring storage variables as `bool` is more expensive compared to `uint256`, as explained [here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol#L23-L27):

> Booleans are more expensive than `uint256` or any type that takes up a full word because each write operation emits an extra `SLOAD` to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

Use `uint256(1)` and `uint256(2)` rather than true/false to avoid a Gwarmaccess ([**100 gas**](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)) for the extra `SLOAD`, and to avoid Gsset (**20000 gas**) when changing from 'false' to 'true', after having been 'true' in the past.

_There are **9** instances of this issue:_  
```solidity
src/contracts/FraxlendPairDeployer.sol:
  94:        mapping(address => bool) public deployedPairCustomStatusByAddress;

src/contracts/FraxlendWhitelist.sol:
  32:        mapping(address => bool) public oracleContractWhitelist;
  35:        mapping(address => bool) public rateContractWhitelist;
  38:        mapping(address => bool) public fraxlendDeployerWhitelist;

src/contracts/FraxlendPairCore.sol:
  78:        mapping(address => bool) public swappers; // approved swapper addresses
 133:        bool public immutable borrowerWhitelistActive;
 134:        mapping(address => bool) public approvedBorrowers;
 136:        bool public immutable lenderWhitelistActive;
 137:        mapping(address => bool) public approvedLenders;
```

## Stack variable used to cache a state variable is only accessed once
If a state variable is only accessed once, avoid caching it in a local variable and use the state variable directly to save gas.

_There is **1** instance of this issue:_  
```solidity
src/contracts/FraxlendPair.sol:
 237:        VaultAccount memory _totalBorrow = totalBorrow;
```

## Use `calldata` instead of `memory` for read-only arguments in external functions
When an external function with a `memory` array is called, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). 

Using `calldata` directly instead of `memory` helps to save gas as values are read directly from `calldata` using `calldataload`, thus removing the need for such a loop in the contract code during runtime execution. 

Also, structs have the same overhead as an array of length one.

_There are **7** instances of this issue:_  
```solidity
src/contracts/FraxlendPairDeployer.sol:
 310:        function deploy(bytes memory _configData) external returns (address _pairAddress) {
 356:        string memory _name,
 357:        bytes memory _configData,
 362:        address[] memory _approvedBorrowers,
 363:        address[] memory _approvedLenders
 398:        function globalPause(address[] memory _addresses) external returns (address[] memory _updatedAddresses) {

src/contracts/FraxlendPairCore.sol:
1067:        address[] memory _path
```

## Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
As seen from [here](https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html):
> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

However, this does not apply to storage values as using reduced-size types might be beneficial to pack multiple elements into a single storage slot. Thus, where appropriate, use `uint256`/`int256` and downcast when needed.

_There are **35** instances of this issue:_  
```solidity
src/contracts/libraries/VaultAccount.sol:
   5:        uint128 amount; // Total amount, analogous to market cap
   6:        uint128 shares; // Total shares, analogous to shares outstanding

src/contracts/VariableInterestRate.sol:
  63:        function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {
  64:        (uint64 _currentRatePerSec, uint256 _deltaTime, uint256 _utilization, ) = abi.decode(

src/contracts/LinearInterestRate.sol:
  76:        function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {

src/contracts/FraxlendPair.sol:
  84:        function decimals() public pure override(ERC20, IERC20Metadata) returns (uint8) {
 165:        uint64 _DEFAULT_INT,
 166:        uint16 _DEFAULT_PROTOCOL_FEE,
 211:        event ChangeFee(uint32 _newFee);
 215:        function changeFee(uint32 _newFee) external whenNotPaused {
 228:        event WithdrawFees(uint128 _shares, address _recipient, uint256 _amountToTransfer);
 234:        function withdrawFees(uint128 _shares, address _recipient) external onlyOwner returns (uint256 _amountToTransfer) {

src/contracts/FraxlendPairCore.sol:
 106:        uint64 lastBlock;
 107:        uint64 feeToProtocolRate; // Fee amount 1e5 precision
 108:        uint64 lastTimestamp;
 109:        uint64 ratePerSec;
 116:        uint32 lastTimestamp;
 117:        uint224 exchangeRate; // collateral:asset ratio. i.e. how much collateral to buy 1e18 asset
 400:        uint64 _newRate
 415:        uint64 _newRate
 561:        uint128 _amount,
 562:        uint128 _shares,
 625:        uint128 _amountToReturn,
 626:        uint128 _shares,
 707:        function _borrowAsset(uint128 _borrowAmount, address _receiver) internal returns (uint256 _sharesAdded) {
 857:        uint128 _amountToRepay,
 858:        uint128 _shares,
 951:        uint128 _sharesToLiquidate,
 967:        uint128 _borrowerShares = userBorrowShares[_borrower].toUint128();
 993:        uint128 _amountLiquidatorToRepay = (_totalBorrow.toAmount(_sharesToLiquidate, true)).toUint128();
 997:        uint128 _sharesToAdjust;
 998:        uint128 _amountToAdjust;
1001:        uint128 _leftoverBorrowShares = _borrowerShares - _sharesToLiquidate;

src/contracts/libraries/SafeERC20.sol:
  22:        uint8 i = 0;
  55:        function safeDecimals(IERC20 token) internal view returns (uint8) {
```

## Not using the named return variables when a function returns, wastes deployment gas  

_There are **4** instances of this issue:_  
```solidity
src/contracts/VariableInterestRate.sol:
  53:        return abi.encode(MIN_UTIL, MAX_UTIL, UTIL_PREC, MIN_INT, MAX_INT, INT_HALF_LIFE);

src/contracts/LinearInterestRate.sol:
  47:        return abi.encode(MIN_INT, MAX_INT, MAX_VERTEX_UTIL, UTIL_PREC);

src/contracts/FraxlendPairCore.sol:
 403:        return _addInterest();
 519:        return _exchangeRate = _exchangeRateInfo.exchangeRate;
```

## `abi.encode()` is less efficient than `abi.encodePacked()`
`abi.encode()` pads its parameters to 32 bytes, regardless of their type. 

In comparison, `abi.encodePacked()` encodes its parameters using the minimal space required by their types. For example, encoding a `uint8` it will use only 1 byte. Thus, `abi.encodePacked()` should be used instead of `abi.encode()` when possible as it saves space, thus reducing gas costs.

_There are **8** instances of this issue:_  
```solidity
src/contracts/VariableInterestRate.sol:
  53:        return abi.encode(MIN_UTIL, MAX_UTIL, UTIL_PREC, MIN_INT, MAX_INT, INT_HALF_LIFE);

src/contracts/FraxlendPairDeployer.sol:
 213:        abi.encode(
 214:            _configData,
 215:            _immutables,
 216:            _maxLTV,
 217:            _liquidationFee,
 218:            _maturityDate,
 219:            _penaltyRate,
 220:            _isBorrowerWhitelistActive,
 221:            _isLenderWhitelistActive
 222:        )

 213:        abi.encode(
 214:            _configData,
 215:            _immutables,
 216:            _maxLTV,
 217:            _liquidationFee,
 218:            _maturityDate,
 219:            _penaltyRate,
 220:            _isBorrowerWhitelistActive,
 221:            _isLenderWhitelistActive
 222:        )

 331:        abi.encode(CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS, TIME_LOCK_ADDRESS, FRAXLEND_WHITELIST_ADDRESS),
 374:        abi.encode(CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS, TIME_LOCK_ADDRESS, FRAXLEND_WHITELIST_ADDRESS),

src/contracts/LinearInterestRate.sol:
  47:        return abi.encode(MIN_INT, MAX_INT, MAX_VERTEX_UTIL, UTIL_PREC);

src/contracts/FraxlendPairCore.sol:
 450:        bytes memory _rateData = abi.encode(
 451:            _currentRateInfo.ratePerSec,
 452:            _deltaTime,
 453:            _utilizationRate,
 454:            block.number - _currentRateInfo.lastBlock
 455:        );
 450:        bytes memory _rateData = abi.encode(
 451:            _currentRateInfo.ratePerSec,
 452:            _deltaTime,
 453:            _utilizationRate,
 454:            block.number - _currentRateInfo.lastBlock
 455:        );
```

## `keccak256()` should only need to be called on a fixed string literal once
If the argument provided to `keccak256()` is fixed (ie. always the same value), the result of `keccak256()` should be saved to a constant variable, and the variable used instead. This would help to save runtime gas costs as it avoids repeated calls to `keccak256()`.

_There is **1** instance of this issue:_  
```solidity
src/contracts/FraxlendPairDeployer.sol:
 329:        keccak256(abi.encodePacked("public")),
```