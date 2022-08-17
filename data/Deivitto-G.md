# GAS

## Public function visibility can be made external
 Functions should have the strictest visibility possible. Public functions may lead to more gas usage by forcing the copy of their parameters to memory from calldata.
### details
 If a function is never called from the contract it should be marked as external. This will save gas.
### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L74-L76
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L78
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L84
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L89
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L100

### mitigation
 Consider changing visibility from public to external.

## use of custom errors rather than revert() / require() error message
### summary
Custom errors reduce 38 gas if the condition is met and 22 gas otherwise.
Also reduces contract size and deployment costs.
### details
Since version 0.8.4 the use of custom errors rather than revert() / require() saves gas as noticed in
https://blog.soliditylang.org/2021/04/21/custom-errors/
https://github.com/code-423n4/2022-04-pooltogether-findings/issues/13

### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L205
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L228
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L253
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L365
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L366-L369
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L399
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L57-L60
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L61-L64
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L65-L68

### mitigation
replace each error message in a require by a custom error

## use != rather than >0 for unsigned integers in require() statements
### Summary
When the optimizer is enabled, gas is wasted by doing a greater-than operation, rather than a not-equals operation inside require() statements. When Using != , the optimizer is able to avoid the EQ, ISZERO, and associated operations, by relying on the JUMPI that comes afterwards, which itself checks for zero.
### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L66
### mitigation
Use != 0 rather than > 0 for unsigned integers in require()  statements.

## splitting require() statements that use && saves gas
### Summary
When the optimizer is enabled, gas is wasted by doing a greater-than operation, rather than a not-equals operation inside require() statements. When Using != , the optimizer is able to avoid the EQ, ISZERO, and associated operations, by relying on the JUMPI that comes afterwards, which itself checks for zero.
### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L57-L60
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L61-L64
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L65-L68

### mitigation
Instead of using the && operator in a single require statement to check multiple conditions. Consider using multiple require statements with 1 condition per require statement (saving 3 gas per 
& ):


## Reduce the size of error messages (Long revert Strings)
### summary
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L205
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L228
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L253
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L365
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L366-L369
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L57-L60
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L61-L64
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L65-L68

### mitigation
Consider shortening the revert strings to fit in 32 bytes


## Using bools for storage incurs overhead { 
### summary
Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

### details
Here is one example of OpenZeppelin about this optimization
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### github permalinks
Over the next files there are multiple instances of bools
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L78
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L94
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L32-L38
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L52-L53
https://github.com/code-423n4/2022-08-frax/blob/eaf6d1422ff1f64393a9cacde88f42661b750bfe/src/contracts/libraries/VaultAccount.sol#L18
### mitigation
Consider using uint256 with values 0 and 1 rather than bool




## Pack state variables tightly
### summary
State variables are expected to be ordered by data type, this helps readability
and also gas optimization by tightly packing the variables.
### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L51-L137
### mitigation
Order in a proper way the state variables to improve readability and to reduce gas usage.


## Store using Struct over multiple mappings
### summary
All these variables could be combine in a Struct in order to reduce the gas cost. 
### details
As noticed in: 
https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2
When multiple mappings that access the same addresses, uints, etc, all of them can be mixed into an struct and then that data accessed like:
mapping(datatype => newStructCreated) newStructMap;
Also, you have this post where it explains the benefits of using Structs over mappings 
https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L127-L130
- - - 
I'm not sure if this mapping is also all_users(address) -> value, if it is, then it can be combined  in the same way
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L132-L137
- - -
Same doubt here, if the address array is expect to be the same array for every mapping, then the values can be combined into a struct
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L32-L38


### mitigation
Consider mixing different mappings into an struct when able in order to save gas.


## Make constant state variables that do not change
### summary
State variables which value isn't changed by any function in the contract, can be declared as a constant state variable to save some gas during deployment.

### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L51
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/contracts/FraxlendPairDeployer.sol#L49
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/contracts/FraxlendPairDeployer.sol#L50
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/contracts/FraxlendPairDeployer.sol#L51


### mitigation
- Add constant to state variables that do not change


## Make immutable state variables that do not change but assigned in the constructor
### summary
State variables which value isn't changed by any function in the contract but constructor, can be declared as a immutable state variable to save some gas during deployment.

### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L57-L59
### mitigation
- Add immutable to state variables that do not change but which value is assigned in constructor

## Explicit initialization
### summary
It is not needed to initialize variables to the default value. Explicitly initializing it with its default value is an anti-pattern and wastes gas.
### details
If a variable is not set/initialized, it is assumed to have the default value ( 0 for uint, false for bool, address(0) for address…). 

### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairConstants.sol#L47
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L33
https://github.com/code-423n4/2022-08-frax/blob/eaf6d1422ff1f64393a9cacde88f42661b750bfe/src/contracts/libraries/SafeERC20.sol#L22

### mitigation
Don't initialize variables to default value





## Index initialized in for loop
### summary
In for loops is not needed to initialize indexes to 0 as it is the default uint/int value. This saves gas.

### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L289-295
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L308-L314
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L265-L267
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L270-L272
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L402-L410
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L51-L54
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L66-L69
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L81-L84

### mitigation
Don't initialize variables to default value


## use of i++ in loop rather than ++i
### summary
++i costs less gas than i++, especially when it's used in for loops
### details
using ++i doesn't affect the flow of regular for loops and improves gas cost

### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L289-295
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L308-L314
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L127-L132
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L152-L160
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L51-L54
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L66-L69
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L81-L84
https://github.com/code-423n4/2022-08-frax/blob/eaf6d1422ff1f64393a9cacde88f42661b750bfe/src/contracts/libraries/SafeERC20.sol#L27-L29

In while loops also applies
https://github.com/code-423n4/2022-08-frax/blob/eaf6d1422ff1f64393a9cacde88f42661b750bfe/src/contracts/libraries/SafeERC20.sol#L24

Also for consistency over the code i = i + 1; to ++i;
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L402-L410

### mitigation
Substitute to ++i 







## increments can be unchecked in loops
### summary
Unchecked operations as the ++i on for loops are cheaper than checked one.

### details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:

    for (uint256 i; i < numIterations; i++) {
    // ...
    }

    to

    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.

### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L289-295
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L308-L314
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L265-L267
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L270-L272
https://github.com/code-423n4/2022-08-frax/blob/eaf6d1422ff1f64393a9cacde88f42661b750bfe/src/contracts/libraries/SafeERC20.sol#L27-L29
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L51-L54
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L66-L69
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L81-L84
### mitigation
Add unchecked ++i at the end of all the for loop where it's not expected to overflow and remove them from the for header


## <array>.length should no be looked up in every loop of a for-loop
### summary
In loops not assigning the length to a variable so memory accessed a lot (caching local variables)

### details
The overheads outlined below are PER LOOP, excluding the first loop
storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)

### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L289-295
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L308-L314
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L265-L267
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L270-L272
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L51-L54
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L66-L69
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L81-L84

### mitigation
Assign the length of the array.length to a local variable in loops for gas savings

## Variables should be cached when used several times
### summary
Variables read more than once improves gas usage when cached into local variable
### details
In loops or state variables, this is even more gas saving

### github permalinks
_lenders[i] 
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L289-295

_borrowers[i] 
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L308-L314

_addresses[i]
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L127-L132
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L152-L160
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L402-L410
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L51-L54
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L66-L69
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L81-L84

data[i] cached and used within a break for the condition to be in the loop
https://github.com/code-423n4/2022-08-frax/blob/eaf6d1422ff1f64393a9cacde88f42661b750bfe/src/contracts/libraries/SafeERC20.sol#L27-L29



### mitigation
Cache variables used more than one into a local variable.




## abi.encode() is less gas efficient than abi.encodePacked()
### Summary
In general, abi.encodePacked is more gas-efficient.

### Details
Changing the abi.encode function to abi.encodePacked can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, abi.encodePacked is more gas-efficient.
### Github permalinks
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L450-L455
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L331
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L374
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L47
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/VariableInterestRate.sol#L53
### mitigation
Change abi.encode to abi.encodePacked



## Functions guaranteed to revert when called by normal users can be marked payable
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github permalinks
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L204-L207
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L234-L263
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L274-L277
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L170-L177
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L50-L55
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L65-L70
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L80-L85

### Mitigation
It's suggested to add payable to functions guaranteed to revert when called by normal users to improve gas costs

## >= cheaper than >
### Summary
Strict inequalities ( > ) are more expensive than non-strict ones ( >= ). This is due to some supplementary checks (ISZERO, 3 gas)

### Github permalinks
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L477
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L754
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L835
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L1002
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L1094
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L379-L380
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L66

### mitigation
Consider using >= 1 instead of > 0 to avoid some opcodes

## <X> += <Y> costs more gas than <X> = <X> + <Y> for state variables
### Summary
x+=y costs more gas than x=x+y for state variables

### Github permalinks
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L724
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L772
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L773

### Mitigation
Don't use += for state variables as it cost more gas.



## Use calldata instead of memory for function parameters
### Summary
It is generally cheaper to load variables directly from calldata, rather than copying them to memory. 
### Details
Only use memory if the variable needs to be modified
### Github Permalinks
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L1067
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L310
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L356-L363
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L398


### Mitigation
Use calldata rather than memory in external functions where the parameter is not modified but only read




## Extra declaration in named returns that don't use them
### summary
Using both named returns and a return statement isn’t necessary, this consumes extra gas as one more variable that is not used is declared .

### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L393-L404
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L409-L423
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L516-L520
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L201
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L46-L48
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/VariableInterestRate.sol#L52-L54

### mitigation
Remove return or returns as needed.