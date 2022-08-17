## 1. SPLILTTING `require()` STATEMENT THAT USE && COULD SAVE GAS
Instead of using the && operator in a single require statement to check multiple conditions, i suggest using multiple require statement with 1 condition per require statement (save 3 gas per &&)

There are 3 instance for this issue:

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L57-L68

## 2. REDUCE THE SIZE OF ERROR MESSAGES (LONG REVERT STRINGS)
Shortening revert string to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

There are 3 instances for this issue:

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L57-L68


## 3. FOR LOOPS CAN BE OPTIMIZED IN SEVERAL WAYS
To optimize the for loop and make it consume less gas, i suggest to:

1. If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas. 

2. Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack. Caching the array length in the stack saves around 3 gas per iteration. I suggest storing the array’s length in a variable before the for-loop.

3. For the i++ or ++i should be unchecked{i++} or unchecked{++i} when it is not possible for them to overflow, as is the case when used in `for` loops.

There are several instances for this issues:

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L270
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308


## 4. USING CALLDATA INSTEAD OF MEMORY FOR READ-ONLY ARGUMENTS IN EXTERNAL FUNCTION COULD SAVES GAS
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it’s still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

There are 2 instances for this issues:
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L295
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L560


## 5. <X> += <Y> COSTS MORE GAS THAN <x> = <X> + <Y> FOR STATE VARIABLES
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L484
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L566-L567
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L643-L644
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L718-L719
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L724
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L813-L815
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L863-L867
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1008-L1012
