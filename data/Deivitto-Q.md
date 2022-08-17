# QA
# Low

## approve should be replaced with safeApprove or safeIncreaseAllowance() / safeDecreaseAllowance()
### Summary
`approve` is subject to a known front-running attack.

### Github permalink
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L1103
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L1184

### Mitigation
Consider using `safeApprove` instead.
Keep in mind though that it would be actually better to replace safeApprove() with safeIncreaseAllowance() or safeDecreaseAllowance().
See this discussion: SafeERC20.safeApprove() Has unnecessary and unsecure added behavior:
https://github.com/OpenZeppelin/openzeppelin-contracts/issues/2219


## Missing checks for address(0x0) when assigning values to address state or immutable variables 
### Summary
Zero address should be checked for state variables, immutable variables. A zero address can lead into problems.
### Github Permalinks
immutable variables
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L107

state variables
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L104-L106

params that affect both cases and not checked
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L152-L153

### Mitigation
Check zero address before assigning or using it


## Missing checks for address(0x0) in withdraw functions 
### Summary
Zero address should be checked for some function parameters. For example in functions like mints, withdrawals... 

A zero address can lead into serious problems as locking eth or correct functioning.

### Details
Missing of checks can lead into loss of funds:
- There are no checks of 0 address in withdraw
- There are no checks of 0 address in withdrawFees

### Github Permalinks
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L676-L680
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L234
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L623-L629

### Mitigation
Check zero address before assigning or using it



## Strict equalities are dangerous in manipulable values
###  Summary:
Using == rather than >= or <= is not ideal in some cases where values can be manipulated
###  Details:
Strict equalities using block.timestamp or amounts is not ideal as they can be manipulated so the equality is always avoided.

In this case
block.timestamp is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

###  Github Permalinks: 
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L420
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L518
###  Mitigation:
Avoid the use of strict equalities in values that can be manipulated

## block.timestamp used as time proxy 
###  Summary:
Risk of using block.timestamp for time should be considered. 
###  Details:
block.timestamp is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.

### References
SWC ID: 116

###  Github Permalinks: 
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L321
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L420
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L434
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L441
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L464
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L518
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L544
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L955
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L955

###  Mitigation:
Consider using an oracle for precision
Consider the risk of using block.timestamp as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 

## Front run initializer
### Summary
The initialize function that initializes important contract state can be called by anyone.
### Details
FraxlendPair, what is FraxlendPairCore that is where initialize() is declared, is not IFraxlendPair, so creating in FraxlendPairDeployer an IFraxlendPair doesn't mean to create a FraxlendPair that means a) or an interface is missing or b) is supposed to be declared elsewhere and the initialize can fall vs a front run.

The attacker can initialize the contract before the legitimate deployer, hoping that the victim continues to use the same contract.

In the best case for the victim, they notice it and have to redeploy their contract costing gas.

### Github Permalinks
- initialize() cast
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L258-L259

- initialize() declaration
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L245

- probably missing interface
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L41
### Mitigation
Consider what is the problem:
If it is being missed an interface, add it.
Else
Use the constructor to initialize non-proxied contracts.
For initializing proxy contracts deploy contracts using a factory contract that immediately calls initialize after deployment or make sure to call it immediately after deployment and verify the transaction succeeded.


## Naming convention of state variable non constant
### Summary:
Only constants are suggested to use style CONSTANTS_WITH_UNDERSCORES, other variables are suggested to use camelCase.

If this variables in CONSTANTS_WITH_UNDERSCORES are supposed to be constant, add the `constant` keyword.

Also, guidelines doesn't say to use uppercase on immutables. Also, in case you prefer to use them no matter what, be consistent with the camelCase immutable variables so they are also in caps.
###  Github Permalinks:
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L49-L51

https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L81-L89

https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L57-L62

### Mitigation
Rename to camelCase

## abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccack256()
### Summary
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456) , but abi.encode(0x123,0x456) => 0x0...1230...456 ). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to 
bytes() or bytes32() instead.

### Details
abi.encodePacked will only use the only use the minimal required memory to encode the data. E.g. an address will only use 20 bytes and for dynamic arrays only the elements will be stored without length. For more info see the Solidity docs for packed mode.

For the input of the keccak method it is important that you can ensure that the resulting bytes of the encoding are unique. So if you always encode the same types and arrays always have the same length then there is no problem. But if you switch the parameters that you encode or encode multiple dynamic arrays you might have conflicts.

https://ethereum.stackexchange.com/questions/119583/when-to-use-abi-encode-abi-encodepacked-or-abi-encodewithsignature-in-solidity

### Github permalinks
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L329
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L372

### mitigation
Change abi.encodePacked to abi.encode when data collision may happen

# Informational
##  Use of magic numbers is confusing and risky
###  Summary:
Magic numbers are hardcoded numbers used in the code which are ambiguous to their intended purpose. These should be replaced with constants to make code more readable and maintainable.

###  Details:
Values are hardcoded and would be more readable and maintainable if declared as a constant

In several locations in the code numbers like 18, 90, 1e36, 13000, 32, 1e18, 64 are used. 

###  Github Permalinks:
18
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L85
https://github.com/code-423n4/2022-08-frax/blob/eaf6d1422ff1f64393a9cacde88f42661b750bfe/src/contracts/libraries/SafeERC20.sol#L57

90
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L194

1e36
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L522

13000 -> 3 times
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L171-L174

32
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L226
https://github.com/code-423n4/2022-08-frax/blob/eaf6d1422ff1f64393a9cacde88f42661b750bfe/src/contracts/libraries/SafeERC20.sol#L21
https://github.com/code-423n4/2022-08-frax/blob/eaf6d1422ff1f64393a9cacde88f42661b750bfe/src/contracts/libraries/SafeERC20.sol#L23
https://github.com/code-423n4/2022-08-frax/blob/eaf6d1422ff1f64393a9cacde88f42661b750bfe/src/contracts/libraries/SafeERC20.sol#L27

1e18
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/VariableInterestRate.sol#L69
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/VariableInterestRate.sol#L76

64
https://github.com/code-423n4/2022-08-frax/blob/eaf6d1422ff1f64393a9cacde88f42661b750bfe/src/contracts/libraries/SafeERC20.sol#L19

###  Mitigation:
Replace magic hardcoded numbers with declared constants.
Define constants for the numbers used throughout the code.
Comment what this numbers are intended for

## Missing indexed event parameters
###  Summary:
Events without indexed event parameters make it harder and
inefficient for off-chain tools to analyze them.
###  Details:
Indexed parameters (“topics”) are searchable event parameters.
They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
 
###  Github Permalinks:
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L200
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L211
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L228
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L268
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L376-L382
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L389
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L504

I would add more indexed values because of the big amount of params and some of them being important
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L1143-L1149
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L1045-L1052
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L897-L904

###  Mitigation:
Consider which event parameters could be particularly useful to off-chain tools and should be indexed.




## Use of string.concat() rather than abi.encodePacked(<str>, <str>)
### Summary
string.concat() can be used to join strings
### Details
string.concat() can be used instead of abi.encodePacked(<str>,<str>)
### Github Permalinks
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L316-L325

### Mitigation
Replace to string.concat() where concatenation of strings is the goal.

## Implementation of the comment is confusing
### Summary:
Different comments says something that is confuse
### Details
`  uint16 internal constant DEFAULT_PROTOCOL_FEE = 0; // 1e5 precision`
I don't understand why it says that the precision of 0 is 1e5

```
    uint256 private constant MIN_INT = 0; // 0.00% annual rate
    uint256 private constant MAX_INT = 146248508681; // 10,000% annual rate @audit I don't understand why 146248508681 is a 10,000%, is this the result of a date * rate operation?
```
###  Github Permalinks:
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairConstants.sol#L47
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L34


###  Mitigation:
Consider explaining more, correcting the code or the comments as needed.



## Missing Natspec 
 ###  Summary: 
 Missing Natspec and regular comments affect readability and maintainability of a codebase. 
 ###  Details: 
 Contracts has partial lack of comments
 ###  Github Permalinks: 

#### Natspec @return value
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPair.sol#L183-L192
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairCore.sol#L393
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairCore.sol#L409

#### Natspec in general 
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L98
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPair.sol#L45-L156

https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPair.sol#L317-L338

#### More documentation needed
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/SafeERC20.sol#L18
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/SafeERC20.sol#L60
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/SafeERC20.sol#L68

https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/VariableInterestRate.sol#L57


 ###  mitigation
 Add @param descriptors
 Add @return descriptors
 Add Natspec comments. 
 Add inline comments. 
 Add comments for what the contract does



## Unused code
### Summary:
Code that is not used should be removed
###  Details:
- parameter _initData is declared but not used
###  Github Permalinks:
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/VariableInterestRate.sol#L63
###  Mitigation:
Remove the code that is not used.

## Bad order of code
### Summary
Clearness of the code is important for the readability and maintainability.
As Solidity guidelines says about declaration order:
1.Type declarations
2.State variables
3.Events
4.Modifiers
5.Functions
Also, state variables order affects to gas in the same way as ordering structs for saving storage slots
### Details
Modifiers, struct and events are disordered. They are not expected to be found between functions, this hardly affects what the reader is expecting from the code.
### github permalink
Events and modifiers
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L50-L1149

Events
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L194-L301

Unexpected struct and events
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L49-L139

Events
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L42-L85


### Mitigation
Follow solidity style guidelines https://docs.soliditylang.org/en/v0.8.15/style-guide.html






## Unused named returns
### summary
Using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity 

### github permalinks
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L393-L404
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L409-L423
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L516-L520
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L201
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/LinearInterestRate.sol#L46-L48
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/VariableInterestRate.sol#L52-L54

### mitigation
Remove return or returns when both used
