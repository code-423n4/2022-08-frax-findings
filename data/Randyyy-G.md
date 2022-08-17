1. Chached array length

#Impact
Loading length for storage arrays cost 100 gas and for memory arrays it costs 3 gas. When arr.length is defined as the condition of for loop, at the start of every iteration the length is loaded from memory. If the length doesn't change during the loop, loading the length of arrays repeatedly can be avoided by saving the length to the stack.

#POC
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L289
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L308

2. i++ -->> ++i

#Impact
++i cost less gas than i++, especially when its used in for-loops.

#Poc
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L289
https://github.com/code-423n4/2022-08-frax/blob/d968f256462469239ec7394171409ed76dab03e1/src/contracts/FraxlendPair.sol#L308

3. x > 0 --> x != 0

#Impact
A small gas optimization is possible by replacing
x > 0
with
x != 0
provide x is an unsigned integer.

#Poc
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L754