# Gas Optimizations
# [G-01] Use prefix not postfix in loops 
Using a prefix increment (++i) instead of a postfix increment (i++) saves gas for each loop cycle and so can have a big gas impact when the loop executes on a large number of elements.


There are 8 occurrences

**FraxlendPair.sol**
[L289](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289) `        for (uint256 i = 0; i < _lenders.length; i++) {`
[L308](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308) `        for (uint256 i = 0; i < _borrowers.length; i++) {`

**FraxlendPairDeployer.sol**
[L127](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L127) `        for (i = 0; i < _lengthOfArray; ) {`
[L152](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L152) `        for (i = 0; i < _lengthOfArray; ) {`
[L402](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L402) `        for (uint256 i = 0; i < _lengthOfArray; ) {`

**FraxlendWhitelist.sol**
[L51](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51) `        for (uint256 i = 0; i < _addresses.length; i++) {`
[L66](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66) `        for (uint256 i = 0; i < _addresses.length; i++) {`
[L81](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81) `        for (uint256 i = 0; i < _addresses.length; i++) {`


#### Recommended Mitigation Steps
Use prefix not postfix to increment in a loop


# [G-02] Short require strings save gas 
Strings in solidity are handled in 32 byte chunks. A require string longer than 32 bytes uses more gas. Shortening these strings will save gas.


There are 6 occurrences

**FraxlendPairDeployer.sol**
[L253](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L253) `        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");`
[L365](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L365) `        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");`
[L366](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L366) `        require(`

**LinearInterestRate.sol**
[L57](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L57) `        require(`
[L61](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L61) `        require(`
[L65](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L65) `        require(`


#### Recommended Mitigation Steps
Shorten all require strings to less than 32 characters


# [G-03] Redundant zero initialization 
Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas.


There are 9 occurrences

**FraxlendPair.sol**
[L289](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289) `        for (uint256 i = 0; i < _lenders.length; i++) {`
[L308](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308) `        for (uint256 i = 0; i < _borrowers.length; i++) {`

**FraxlendPairCore.sol**
[L265](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265) `        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {`
[L270](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L270) `        for (uint256 i = 0; i < _approvedLenders.length; ++i) {`

**FraxlendPairDeployer.sol**
[L402](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L402) `        for (uint256 i = 0; i < _lengthOfArray; ) {`

**FraxlendWhitelist.sol**
[L51](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51) `        for (uint256 i = 0; i < _addresses.length; i++) {`
[L66](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66) `        for (uint256 i = 0; i < _addresses.length; i++) {`
[L81](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81) `        for (uint256 i = 0; i < _addresses.length; i++) {`

**LinearInterestRate.sol**
[L33](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L33) `    uint256 private constant MIN_INT = 0; // 0.00% annual rate`


#### Recommended Mitigation Steps
Remove the redundant zero initialization]
      uint256 amount


# [G-04] Use Custom Errors instead of revert()/require() 
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)


There are 7 occurrences

**FraxlendPairDeployer.sol**
[L253](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L253) `        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");`
[L365](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L365) `        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");`
[L366](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L366) `        require(`
[L399](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L399) `        require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");`

**LinearInterestRate.sol**
[L57](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L57) `        require(`
[L61](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L61) `        require(`
[L65](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L65) `        require(`


#### Recommended Mitigation Steps
Recommended to replace revert strings with custom errors.


