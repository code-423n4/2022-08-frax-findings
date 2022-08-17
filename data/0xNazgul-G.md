## [NAZ-G1] Use `++index` instead of `index++` to increment a loop counter
**Context**: [`SafeERC20.sol#L27`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L27), [`FraxlendWhitelist.sol#L51`](frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51), [`FraxlendWhitelist.sol#L66`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66), [`FraxlendWhitelist.sol#L81`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81), [`FraxlendPair.sol#L289`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289), [`FraxlendPair.sol#L308`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308)

**Description**:
Due to reduced stack operations, using `++index` saves 5 gas per iteration.

**Recommendation**: 
Use `++index `to increment a loop counter.


## [NAZ-G2] The Increment In For Loop Post Condition Can Be Made Unchecked
**Context**: [`SafeERC20.sol#L27`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L27), [`FraxlendWhitelist.sol#L51`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51), [`FraxlendWhitelist.sol#L66`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66), [`FraxlendWhitelist.sol#L81`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81), [`FraxlendPairCore.sol#L265`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265), [`FraxlendPairCore.sol#L270`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L270), [`FraxlendPair.sol#L289`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289), [`FraxlendPair.sol#L308`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308)

**Description**:
(This is only relevant if you are using the default solidity checked arithmetic). `i++` involves checked arithmetic, which is not required. This is because the value of `i` is always strictly less than length <= 2**256 - 1. Therefore, the theoretical maximum value of `i` to enter the for-loop body is `2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. One can manually do this by:
```js
for (uint i = 0; i < length; ) {
    // do something that doesn't change the value of i
    unchecked {
        ++i;
    }
}
```
Note that it’s important that the call to `unchecked_inc` is inlined. This is only possible for solidity versions starting from `0.8.2`.

**Recommendation**: 
Consider doing the increment in the for loop post condition in an unchecked block.


## [NAZ-G3] Catching The Array Length Prior To Loop
**Context**: [`FraxlendWhitelist.sol#L51`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51), [`FraxlendWhitelist.sol#L66`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66), [`FraxlendWhitelist.sol#L81`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81), [`FraxlendPairCore.sol#L265`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265), [`FraxlendPairCore.sol#L270`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L270), [`FraxlendPair.sol#L289`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289), [`FraxlendPair.sol#L308`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308)

**Description**:
One can save gas by caching the array length (in stack) and using that set variable in the loop. Replace state variable reads and writes within loops with local variable reads and writes. This is done by assigning state variable values to new local variables, reading and/or writing the local variables in a loop, then after the loop assigning any changed local variables to their equivalent state variables.

**Recommendation**: 
Simply do something like so before the for loop: ```uint length =  variable.length```. Then add ```length``` in place of ``` variable.length``` in the for loop. 


## [NAZ-G4] Setting The Constructor To Payable
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts)

**Description**:
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of `msg.value == 0` and saves 21 gas on deployment with no security risks.

**Recommendation**: 
Set the constructor to payable.


## [NAZ-G5] Use of Custom Errors Instead of String
**Context**: [`LinearInterestRate.sol`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol), [`FraxlendPairDeployer.sol`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol)

**Description**:
To save some gas the use of custom errors leads to cheaper deploy time cost and run time cost. The run time cost is only relevant when the revert condition is met.

**Recommendation**: 
Use Custom Errors instead of strings.


## [NAZ-G6] Function Ordering via Method ID
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts)

**Description**:
Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. One could use [`This tool`](https://emn178.github.io/solidity-optimize-name/) to help find alternative function names with lower Method IDs while keeping the original name intact.

**Recommendation**: 
Find a lower method ID name for the most called functions for example ```mostCalled()``` vs. ```mostCalled_41q()``` is cheaper by 44 gas.