# [1] Unnecessary CALLDATALOADs in for-each loops
There are many for loops that follows this for-each pattern:
```
for (uint256 i = 0; i < array.length; i++) {
    // do something with `array[i]`
}
```
In such for loops, the `array.length` is read on every iteration, instead of caching it once in a local variable and read it from there. Calldata reads are a bit more expensive than reading local variables.

### Recommendation
Read these values from calldata once, cache them in local variables and then read them again from the local variables. For example:
```
uint256 length = array.length;
for (uint256 i = 0; i < length; i++) {
    // do something with `array[i]`
}
```

### Affected Contracts
- `FraxlendPair.sol`
- `FraxlendPairCore.sol`
- `FraxlendWhitelist.sol`

# [2] Default value initialization
If a variable is not set/initialized, it is assumed to have the default value (`0`, `false`, `0x0` etc. depending on the data type).
Explicitly initializing it with its default value is an anti-pattern and wastes gas. For example:
```
uint256 x = 0;
```
can be optimized to:
```
uint256 x;
```

### Affected Contracts
- `SafeERC20.sol`
- `FraxlendPair.sol`
- `FraxlendPairCore.sol`
- `FraxlendPairDeployer.sol`
- `FraxlendWhitelist.sol`

# [3] `++` is cheaper than `x = x + 1`
Use prefix increments (`++x`) instead of increments by 1 (`x = x + 1`).

### Recommendation
Change all increments by 1 to prefix increments.

### Affected Contracts
- `VaultAcount.sol`
- `FraxlendPairDeployer.sol`

# [4] Unnecessary array boundaries check when loading an array element twice
Some functions loads the same array element more than once. In such cases, only one array boundaries check should take place, and the rest are unnecessary. Therefore, this array element should be cached in a local variable and then be loaded again using that local variable, skipping the redundant second array boundaries check and saving gas.

### Recommendation
Load the array elements once, cache them in local variables and then read them again using the local variables. For example:
```
uint256 item = array[i];
// do something with `item`
// do some other thing with `item`
```

### Affected Contracts
- `FraxlendPair.sol`
- `FraxlendPairDeployer.sol`
- `FraxlendWhitelist.sol`

# [5] Prefix increments are cheaper than postfix increments
Use prefix increments (`++x`) instead of postfix increments (`x++`).

### Recommendation
Change all postfix increments to prefix increments.

### Affected Contracts
- `SafeERC20.sol`
- `FraxlendPair.sol`
- `FraxlendPairDeployer.sol`
- `FraxlendWhitelist.sol`

# [6] Unnecessary checked arithmetic in for loops
There is no risk of overflow caused by increments to the iteration index in for loops (the `i++` in `for (uint256 i = 0; i < numIterations; i++)`). Increments perform overflow checks that are not necessary in this case.

### Recommendation
Surround the increment expressions with an `unchecked { ... }` block to avoid the default overflow checks. For example, change the loop
```
for (uint256 i = 0; i < numIterations; i++) {
  // ...
}
```
to
```
for (uint256 i = 0; i < numIterations;) {
  // ...
  unchecked { i++; }
}
```
It is a little less readable but it saves a significant amount of gas.

### Affected Contracts
- `SafeERC20.sol`
- `FraxlendPair.sol`
- `FraxlendPairCore.sol`
- `FraxlendWhitelist.sol`

# [7] Non-zero tests for unsigned integers
Testing `!= 0` is cheaper than testing `> 0` for unsigned integers.

### Recommendation
Use `!= 0` instead of `> 0` when testing unsigned integers.

### Affected Contracts
- `FraxlendPairCore.sol`
- `FraxlendPairDeployer.sol`
- `LinearInterestRate.sol`

# [8] Long `require()` strings
`require()` strings longer than 32 bytes cost extra gas.

### Recommendation
Use `require()` strings no longer than 32 bytes.

### Affected Contracts
- `FraxlendPairDeployer.sol`
- `LinearInterestRate.sol`