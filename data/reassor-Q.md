# List
## Low Risk
1. Missing zero address checks
2. Critical address change

## Non-Critical Risk
3. Missing indexed events
4. Usage of not well-tested solidity version might contain undiscovered vulnerabilities
5. Contracts are using unlocked pragma
6. Missing/incomplete natspec comments


# 1. Missing zero address checks
## Risk
Low

## Impact
Multiple contracts do not check for zero addresses which might lead to loss of funds, failed transactions and can break the protocol functionality.

## Proof of Concept
`FraxlendWhitelist.sol`:
* Missing `_addresses` zero address checks - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L50 
* Missing `_addresses` zero address checks - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L65
* Missing `_addresses` zero address checks - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L80

`FraxlendPairDeployer.sol`:
* Missing `_circuitBreaker`, `_compotroller`, `_timelock`, `_fraxlendWhitelist` zero addresses checks - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L99-L102  

`FraxlendPairCore.sol`:
* Misssing `_circuitBreaker`, `_comptrollerAddress`, `_timeLockAddress`, `_fraxlendWhitelitAddress` zero addresss checks - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L164-L168
* Missing `_receiver` zero address check - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L583
* Missing `_receiver` zero address check - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L602
* Missing `_receiver` and `_owner` zero addresses checks - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L662-L663
* Missing `_receiver` and `_owner` zero addresses checks - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L678-L679
* Missing `_receiver` zero address check - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L742
* Missing `_borrower` zero address check - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L786
* Missing `_receiver` zero address check - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L828
* Missing `_borrower` zero address check - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L882

`FraxlendPair.sol`:
* Missing `_newAddress` zero address check - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L204
* Missing `_recipient` zero address check - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L234
* Missing `_swapper` zero address check - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L274
* Missing `_lenders` zero addresses checks - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L288
* Missing `_borrowers` zero addresses checks - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L307

## Tools Used
Manual Review / VSCode

## Recommended Mitigation Steps
It is recommended to add zero address checks for listed parameters.


# 2. Critical address change
## Risk
Low

## Impact
Changing critical addresses such as ownership should be a two-step process where the first transaction (from the old/current address) registers the new address (i.e. grants ownership) and the second transaction (from the new address) replaces the old address with the new one. This gives an opportunity to recover from incorrect addresses mistakenly used in the first step. If not, contract functionality might become inaccessible.

## Proof of Concept
`FraxlendWhitelist.sol`:
* Changing owner via `Ownable` - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L30

`FraxlendPairDeployer.sol`:
* Changing owner via `Ownable` - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L44

`FraxlendPairCore.sol`:
* One step process for changing owner via `Ownable` from openzeppelin - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L46

`FraxlendPair.sol`:
* One step process for changing owner via `Ownable` from openzeppelin (FraxlendPairCore) - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L41

## Tools Used
Manual Review / VSCode

## Recommended Mitigation Steps
It is recommended to implement two-step process for changing ownership.


# 3. Missing indexed events
## Risk
Non-Critical

## Impact
Protocol implements multiple events but does not properly index parameters for all of them. Lack of indexed parameters for events makes it difficult for off-chain applications to monitor the protocol.

## Proof of Concept
```
FraxlendPair.sol:200:    event SetTimeLock(address _oldAddress, address _newAddress);
FraxlendPair.sol:228:    event WithdrawFees(uint128 _shares, address _recipient, uint256 _amountToTransfer);
```

## Tools Used
Manual Review / VSCode

## Recommended Mitigation Steps
It is recommended to add `indexed` keyword to listed events parameters.


# 4. Usage of not well-tested solidity version might contain undiscovered vulnerabilities
## Risk
Non-Critical

## Impact
Using the latest versions might make contracts susceptible to undiscovered compiler bugs.

## Proof of Concept
```
FraxlendPair.sol:2:pragma solidity ^0.8.15;
FraxlendPairConstants.sol:2:pragma solidity ^0.8.15;
FraxlendPairCore.sol:2:pragma solidity ^0.8.15;
FraxlendPairDeployer.sol:2:pragma solidity ^0.8.15;
FraxlendWhitelist.sol:2:pragma solidity ^0.8.15;
LinearInterestRate.sol:2:pragma solidity ^0.8.15;
VariableInterestRate.sol:2:pragma solidity ^0.8.15;
libraries/VaultAccount.sol:2:pragma solidity ^0.8.15;
libraries/SafeERC20.sol:2:pragma solidity ^0.8.15;
```

## Tools Used
Manual Review / VSCode

## Recommended Mitigation Steps
It is recommended to use more stable and tested solidity version such as `0.8.10`.


# 5. Contracts are using unlocked pragma
## Risk
Non-Critical

## Impact
As different compiler versions have critical behavior specifics if the contract gets accidentally deployed using another compiler version compared to one they tested with, various types of undesired behavior can be introduced.

## Proof of Concept
```
FraxlendPair.sol:2:pragma solidity ^0.8.15;
FraxlendPairConstants.sol:2:pragma solidity ^0.8.15;
FraxlendPairCore.sol:2:pragma solidity ^0.8.15;
FraxlendPairDeployer.sol:2:pragma solidity ^0.8.15;
FraxlendWhitelist.sol:2:pragma solidity ^0.8.15;
LinearInterestRate.sol:2:pragma solidity ^0.8.15;
VariableInterestRate.sol:2:pragma solidity ^0.8.15;
libraries/VaultAccount.sol:2:pragma solidity ^0.8.15;
libraries/SafeERC20.sol:2:pragma solidity ^0.8.15;
```

## Tools Used
Manual Review / VSCode

## Recommended Mitigation Steps
Consider locking compiler version, for example pragma solidity `0.8.15`.


# 6. Missing/incomplete natspec comments
## Risk
Non-Critical

## Impact
Multiple contracts are missing natspec comments which makes code more difficult to read and prone to errors.

## Proof of Concept
`libraries/SafeERC20.sol`:
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L18
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L60
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L68

`libraries/VaultAccount.sol`:
* Missing `@param total`, `@param amount`, `@param roundUp` and `@return` natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/VaultAccount.sol#L14-L16
* Missing `@param total`, `@param shares`, `@param roundUp` and `@return` natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/VaultAccount.sol#L31-L33

`FraxlendPairDeployer.sol`:
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L98-L102

`FraxlendPairCore.sol`:
* Missing `@return` for `_feesAmount`, `_feesShares`, `-newRate` - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L392
* Missing `@return` for `_feesAmount`, `_feesShares`, `-newRate` - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L408

`FraxlendPair.sol`:
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L45
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L74
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L78
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L84
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L89
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L96
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L100
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L104
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L108
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L112
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L116
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L120
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L124
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L128
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L132
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L136
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L140
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L144
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L148
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L156
* Missing `@return` natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L183
* Missing `@return` natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L190
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L317
* Missing natspec - https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L330

## Tools Used
Manual Review / VSCode

## Recommended Mitigation Steps
It is recommended to add missing natspec comments.