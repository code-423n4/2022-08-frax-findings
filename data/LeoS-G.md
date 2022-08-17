## [G-01] Index increment can be left uncheck in for loop
For solidity ^0.8.0 there is an overflow check on each increment operation. This check is not needed in those `for` or `while` loop, since it can't overflow.

9 instances:

 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L270
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L24
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L27

Consider removing `i++` or `++i` and replacing it by `unchecked { ++i; }` at the end of the loop. Transforming `i++` to `++i` is also cheaper.

## [G-02] Expression like `x = x + y` are cheaper than `x += y` for states variables.

22 instances:

 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L475-L476
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L484
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L566-L567
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L718-L719
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L724
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L772-L773
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L252-L253
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L643-L644
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L813
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L815
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L863-L864
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L867
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1008
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1011-L1012

Consider replacing `+=` and `-=`.

## [G-03] Unnecessary initialization of variables.
`int`, `uint`, `bool` and `address` are initialized by default with `0`, `0`, `false` and `address(0)`. It is not necessary to initialize these values again.

    uint256 i =  0 ; -> uint256 i;

11 instances:

 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L22
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L270
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L402
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308

Consider shortening these initializations.

*save 3 gas each*

## [G-04] `.length` should not be called in every loop.
A cached length is more expensive to store, but cheaper to create. So if the length is called a lot of time, it is a good practice to cache it.

7 instances:

 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L270
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L208

Consider caching the length before the loop.

*Cost 100 gas to store the length, but save 3 gas each loop by not calling it.*

## [G-05] Multiple access of an array should use a local variable cache.

Accessing a value in an array costs a lot of gas, if the same index is called multiple times, it's a good practice to cache it.

2 instances:
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L291-L293
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L310-L312

Consider caching these values.

*Save 42 gas each call*

## [G-06] `external` function for the owner can be marked as `payable`.
If a function is guaranteed to revert when called by a normal user, this function can be marked as `payable` to avoid the check to know if a payment is provided.

7 instances:

 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L50
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L65
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L80
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L204
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L234
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L274
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L170

Consider adding `payable` keyword.

*Save 21 gas cost each*

## [G-07] Short reverted strings can save gas.
Reverted strings which are longer than 32 bytes require at least one additional `mstore` and so consume more gas than a shorter.

8 instances:

 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L57-L68
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L205
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L228
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L253
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L365-L369

  
Consider shortening the revert strings to fit within 32 bytes, or using custom errors.

*Save deployment cost or runtime cost when the condition is met.*

## [G-08] Usage of `uint/int` smaller than 32 bytes can cause overhead.

To optimize gas, it's a good practice to use only 32 bytes `uint/int`. The EVM operates on 32 bytes, if an element is smaller than that, the EVM needs to transform it to 32 bytes, which costs gas. This cost reduction usually outweighs the gain of a properly sized element.

 45 instances:

 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L22

- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L55-L58
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol#L5-L6
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L85-L90
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L35-L41
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L64-L67
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairConstants.sol#L41
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairConstants.sol#L47
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L105-L110
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L116
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L400
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L415
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L434-L435
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L464-L465
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L475-L476
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L484
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L542-L544
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L561-L562
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L594
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L613
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L625-L626
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L668
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L684
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L707
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L719
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L757
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L857-L858
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L886
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L937
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L951
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L967
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L993
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L997-L998
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1001
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1005
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1100
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1209
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L84
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L165-166
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L211
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L215
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L228
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L234
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L240
- https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L252

Consider changing those data types.