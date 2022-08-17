# QA Report

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Setters should check the input value  |  1 |
| 2      | Immutable addresses lack ZERO-ADDRESS check  |  3 |
| 3      | Non-library/interface files should use fixed compiler versions, not floating ones     |  7  |


## Findings

### 1- Setters should check the input value :

Setters should check the input value - ie make revert if it is the zero address or zero

#### Risk
Low 

#### Proof of Concept
Instances include:

FraxlendPairCore.sol

[function setTimeLock(address _newAddress)](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L204)

#### Mitigation
Add non-zero checks - address - to the setters aforementioned.

### 2- Immutable addresses lack ZERO-ADDRESS check :

Constructors should check the address written in an immutable address variable is not the zero address

#### Risk
Low 

#### Proof of Concept
Instances include:

FraxlendPairCore.sol

[CIRCUIT_BREAKER_ADDRESS = _circuitBreaker;](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L172)

[COMPTROLLER_ADDRESS = _comptrollerAddress;](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L173)

[FRAXLEND_WHITELIST_ADDRESS = _fraxlendWhitelistAddress;](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L175)

[assetContract = IERC20(_asset);](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L190)

[collateralContract = IERC20(_collateral);](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L191)

#### Mitigation
Add zero address checks inside the constructor for the instances mentioned above.

### 3- Non-library/interface files should use fixed compiler versions, not floating ones :

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

check this source : https://swcregistry.io/docs/SWC-103

#### Risk
Low 

#### Proof of Concept
All contracts use a floating solidity version 

```
pragma solidity ^0.8.15;
```

#### Mitigation
Lock the pragma version to the same version as used in the other contracts and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile it locally.
