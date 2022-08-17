# Summary
| Issue | Instances |
| ------ | :--------: |
| `++i` uses less gas compared to `i++` | 9 |
| Use custom errors instead of `require()` to save gas | 9 |
| Cache array length outside of loop | 7 |
| Return values directly without an intermediate return variable | 1 |
| Unnecesary return  | 1 | 
| Let the default value  be applied to variables initialized to the default value | 11 |
| Use `!= 0`  instead of `> 0`  for a `uint`  | 8 |


# Gas Optimisations

## `++i` uses less gas compared to `i++`   

This is especially relevant for the use of `i++` in `for` loops. This saves 6 gas per loop. 

See: https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g012---use-prefix-increment-instead-of-postfix-increment-if-possible

_There are 9 instances of this issue:_

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L24

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L27

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L130

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L158



## Use custom errors instead of `require()` to save gas
Custom errors are available from solidity version 0.8.4. The instances below match or exceed that version.

_There are 9 instances of this issue:_

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L57-L60

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L61-L64

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L65-L68

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L205

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L228

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L253

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L365

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L366-L369

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L399


## Cache array length outside of loop
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration. To do this, create a variables containing the array length before the loop. 

See: https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g002---cache-array-length-outside-of-loop

_There are 7 instances of this issue:_

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L270

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308



## Return values directly without an intermediate return variable
Initializing a return variable for a function, then assigning a value to it requires more gas compared to simply returning the value, as long as the variable is not being used elsewhere in the function. 

_There is 1 instancesof this issue:_

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L509-L511


## Unnecesary return 
Returning the named return variable of a function is unnecesary. 

_There is 1 instancesof this issue:_

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L233


## Let the default value  be applied to variables initialized to the default value
Letting the default value of `0`, `false` be initialized to variables costs less gas compared to initializing it to these default values. 

See: https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g001---dont-initialize-variables-with-default-value

_There are 11 instances of this issue:_

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L22

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L33

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairConstants.sol#L47

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L270

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L402



## Use `!= 0`  instead of `> 0`  for a `uint` 
This saves 6 gas per instance. 

See: https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g003---use--0-instead-of--0-for-unsigned-integer-comparison

_There are 8 instances of this issue:_

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L66

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L477

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L754

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L835

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1002

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1094

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L379

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L380

