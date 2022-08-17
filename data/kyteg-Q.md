# Summary

- ### Low Risk Issues
	- 1.  Nonspecific Compiler Version Pragma

- ### Non-critical Issues
	- 1. Constants should be defined rather than using magic numbers


# Low Risk

## 1. Nonspecific Compiler Version Pragma
Avoid floating pragmas for non-library contracts. 

See: https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma

Recommended mitigation: 
- Use a specific compiler version


_There are 5 instances of this issue:_

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L2

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L2

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L2

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L2

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L2



# Non-critical

## 1. Constants should be defined rather than using magic numbers

_There are 4 instances of this issue:_

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L85

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L171

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L173

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L174


