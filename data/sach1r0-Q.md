# Non-Library/Interface files should use fixed compiler versions, not floating ones

## Details
Contracts should be deployed with the same compiler version/flags where they have been tested with. Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version that might introduce bugs that affect the contract system negatively.
see reference: https://github.com/code-423n4/2021-11-unlock-findings/issues/15, https://code4rena.com/reports/2022-03-paladin/ and https://swcregistry.io/docs/SWC-103

## Mitigation
I suggest removing `^` in `pragma solidity ^0.8.15` and change it to `pragma solidity 0.8.15` to be consistent with the rest of the contracts.

## Line of code
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L2
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L2
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L2
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairConstants.sol#L2
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L2
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L2
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L2
