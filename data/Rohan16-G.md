## 1. Custom Errors instead of Revert Strings to save Gas

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

Starting from Solidity v0.8.4,there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");),but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
### Instances
//Links to githubfile
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L205
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L228
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L253
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L365
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L399

```
//actual codes used
src/contracts/FraxlendPairDeployer.sol:205:            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
src/contracts/FraxlendPairDeployer.sol:228:            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
src/contracts/FraxlendPairDeployer.sol:253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
src/contracts/FraxlendPairDeployer.sol:365:        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
src/contracts/FraxlendPairDeployer.sol:399:        require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");
```

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L57-#L68
```
src/contracts/LinearInterestRate.sol:57:        require(
src/contracts/LinearInterestRate.sol:61:        require(
src/contracts/LinearInterestRate.sol:65:        require(
```


## 2.  IT COSTS MORE GAS TO INITIALIZE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED

### Optimizing variables
*Variable packing*

Solidity contracts have contiguous 32 byte (256 bit) slots used for storage. When we arrange variables so multiple fit in a single slot, it is called variable packing.

Variable packing is like a game of Tetris. If a variable we are trying to pack exceeds the 32 byte limit of the current slot, it gets stored in a new one. We must figure out which variables fit together the best to minimize wasted space.

Because each storage slot costs gas, variable packing helps us optimize our gas usage by reducing the number of slots our contract requires.
Let’s look at an example:
```
uint128 a;
uint256 b;
uint128 c;
```
These variables are not packed. If b was packed with a, it would exceed the 32 byte limit so it is instead placed in a new storage slot. The same thing happens with c and b.
```
uint128 a;
uint128 c;
uint256 b;
```
These variables are packed. Because packing c with a does not exceed the 32 byte limit, they are stored in the same slot.

Keep variable packing in mind when choosing data types — a smaller version of a data type is only useful if it helps pack the variable in a storage slot. If a uint128 does not pack, we might as well use a uint256.

### Data location
Variable packing only occurs in storage — memory and call data does not get packed. You will not save space trying to pack function arguments or local variables.

Reference data types
Structs and arrays always begin in a new storage slot — however their contents can be packed normally. A uint8 array will take up less space than an equal length uint256 array.

It is more gas efficient to initialize a tightly packed struct with separate assignments instead of a single assignment. Separate assignments makes it easier for the optimizer to update all the variables at once.
```
Initialize structs like this:

Point storage p = Point()
p.x = 0;
p.y = 0;
Instead of:

Point storage p = Point(0, 0);
```
### Inheritance
When we extend a contract, the variables in the child can be packed with the variables in the parent.

The order of variables is determined by C3 linearization. For most applications, all you need to know is that child variables come after parent variables.

### Data types
We have to manage trade-offs when selecting data types to optimize gas. Different situations can make the same data type cheap or expensive.

### Memory vs. Storage
Performing operations on memory — or call data, which is similar to memory — is always cheaper than storage.

A common way to reduce the number of storage operations is manipulating a local memory variable before assigning it to a storage variable.

We see this often in loops:
```
uint256 return = 5; // assume 2 decimal places
uint256 totalReturn;
function updateTotalReturn(uint256 timesteps) external {
    uint256 r = totalReturn || 1;
    for (uint256 i = 0; i < timesteps; i++) {
        r = r * return;
    }
    totalReturn = r;
}
```
In calculateReturn, we use the local memory variable r to store intermediate values and assign the final value to our storage variable totalReturn.

### Fixed vs. Dynamic
Fixed size variables are always cheaper than dynamic ones.

If we know how long an array should be, we specify a fixed size:
### uint256[12] monthlyTransfers;
This same rule applies to strings. A string or bytes variable is dynamically sized; we should use a byte32 if our string is short enough to fit.

If we absolutely need a dynamic array, it is best to structure our functions to be additive instead of subractive. Extending an array costs constant gas whereas truncating an array costs linear gas.

### Mapping vs. Array
Most of the time it will be better to use a mapping instead of an array because of its cheaper operations.

However, an array can be the correct choice when using smaller data types. Array elements are packed like other storage variables and the reduced storage space can outweigh the cost of an array’s more expensive operations. This is most useful when working with large arrays.

### Other techniques
There are a few other techniques when working with variables that can help us optimize gas cost.

*Initialization*
Every variable assignment in Solidity costs gas. When initializing variables, we often waste gas by assigning default values that will never be used.
```
uint256 value; is cheaper than uint256 value = 0;.
```
*Require strings*
If we are adding message strings to require statements, we can make them cheaper by limiting the string length to 32 bytes.

Unpacked variables
The EVM operates on 32 bytes at a time, variables smaller than that get converted. If we are not saving gas by packing the variable, it is cheaper for us to use 32 byte data types such as uint256
### Instances

// Links to github file 
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L33
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairConstants.sol#L47
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L22


```
src/contracts/LinearInterestRate.sol:33:    uint256 private constant MIN_INT = 0; // 0.00% annual rate
src/contracts/FraxlendPairConstants.sol:47:    uint16 internal constant DEFAULT_PROTOCOL_FEE = 0; // 1e5 precision
src/contracts/libraries/SafeERC20.sol:22:            uint8 i = 0;
```

## 3.`ARRAY.LENGTH` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
The overheads outlined below are `PER LOOP`, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use `MLOAD` (3 gas)
calldata arrays use `CALLDATALOAD` (3 gas)
Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset.
### Instances
//Links to gtihubfile
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L270
```
src/contracts/FraxlendPairCore.sol:265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
src/contracts/FraxlendPairCore.sol:270:        for (uint256 i = 0; i < _approvedLenders.length; ++i)
```
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81
```
src/contracts/FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++)
```
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308
```
src/contracts/FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
src/contracts/FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) 
```

## 4. Reduce the size of error messages (Long revert Strings)

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### Instances:

I suggest shortening the revert strings to fit in 32 bytes, or that using custom errors as described next.**

Custom errors from Solidity 0.8.4 are cheaper than revert strings
(cheaper deployment cost and runtime cost when the revert condition is
met)

Source [Custom Errors](https://blog.soliditylang.org/2021/04/21/custom-errors/) in Solidity:

### Instances
//Links to githubfile
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L205
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L228
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L253
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L365
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L399

```
//actual codes used
src/contracts/FraxlendPairDeployer.sol:205:            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
src/contracts/FraxlendPairDeployer.sol:228:            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
src/contracts/FraxlendPairDeployer.sol:253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
src/contracts/FraxlendPairDeployer.sol:365:        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
src/contracts/FraxlendPairDeployer.sol:399:        require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");
```

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L57-#L68
```
src/contracts/LinearInterestRate.sol:57:        require(
src/contracts/LinearInterestRate.sol:61:        require(
src/contracts/LinearInterestRate.sol:65:        require(
```

## 5.++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP
### Instances:
*`FraxlendWhitelist.sol`*
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81
```
src/contracts/FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
```
`SafeERC20.sol`
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L27

```
src/contracts/libraries/SafeERC20.sol:27:            for (i = 0; i < 32 && data[i] != 0; i++) {
```
`FraxlandPair.sol`
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#289
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#308

```
src/contracts/FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
src/contracts/FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
```
