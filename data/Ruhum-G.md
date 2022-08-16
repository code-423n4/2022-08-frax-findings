# Gas Report

- [G-01: use `constant`](#g-01-use-constant)
- [G-02: use `immutable`](#g-02-use-immutable)
- [G-03: use `unchecked` when incrementing a loop's iterator](#g-03-use-unchecked-when-incrementing-a-loops-iterator)
- [G-04: use `++i`](#g-04-use-i)

## G-01: use `constant`

- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L49-L51

## G-02: use `immutable`

- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L57-L59

## G-03: use `unchecked` when incrementing a loop's iterator

Realistically the iterator can't overflow because of the loop's condition. Incrementing it within an `unchecked` block will save gas.

```sol
for (uint i; i < length;) {
    unchecked {
        ++i;
    }
}
```

```
./src/contracts/FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
./src/contracts/FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
./src/contracts/libraries/SafeERC20.sol:27:            for (i = 0; i < 32 && data[i] != 0; i++) {
./src/contracts/FraxlendWhitelist.sol:53:        for (uint256 i = 0; i < _addresses.length; i++) {
./src/contracts/FraxlendWhitelist.sol:68:        for (uint256 i = 0; i < _addresses.length; i++) {
./src/contracts/FraxlendWhitelist.sol:83:        for (uint256 i = 0; i < _addresses.length; i++) {
./src/contracts/FraxlendPairCore.sol:265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
./src/contracts/FraxlendPairCore.sol:270:        for (uint256 i = 0; i < _approvedLenders.length; ++i) {
```

## G-04: use `++i`

`++i` is cheaper than `i++` when incrementing a loop's iterator.

```
./src/contracts/FraxlendPairDeployer.sol:131:                i++;
./src/contracts/FraxlendPairDeployer.sol:159:                i++;
./src/contracts/FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
./src/contracts/FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
./src/contracts/libraries/SafeERC20.sol:24:                i++;
./src/contracts/libraries/SafeERC20.sol:27:            for (i = 0; i < 32 && data[i] != 0; i++) {
./src/contracts/FraxlendWhitelist.sol:53:        for (uint256 i = 0; i < _addresses.length; i++) {
./src/contracts/FraxlendWhitelist.sol:68:        for (uint256 i = 0; i < _addresses.length; i++) {
```