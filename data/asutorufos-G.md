G-1  <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use `MLOAD` (3 gas)
calldata arrays use `CALLDATALOAD` (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

[FraxlendPair.sol L#289](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_lenders.length%3B%20i%2B%2B)%20%7B)
[FraxlendPair.sol L#308](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_borrowers.length%3B%20i%2B%2B)%20%7B)

[FraxlendPairCore.sol L#265](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_approvedBorrowers.length%3B%20%2B%2Bi)%20%7B)

[FraxlendPairCore.sol L#270](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_approvedLenders.length%3B%20%2B%2Bi)%20%7B)
G-2 `++I`/`I++` SHOULD BE `UNCHECKED`{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
[FraxlendPair.sol L#289](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_lenders.length%3B%20i%2B%2B)%20%7B)
[FraxlendPair.sol L#308](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_borrowers.length%3B%20i%2B%2B)%20%7B)
[FraxlendPairCore.sol L#265](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_approvedBorrowers.length%3B%20%2B%2Bi)%20%7B)

[FraxlendPairCore.sol L#270](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_approvedLenders.length%3B%20%2B%2Bi)%20%7B)

G-3 IT COSTS MORE GAS TO INITIALIZE NON-CONSTANT/NON-IMMUTABLE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED

[FraxlendPair.sol L#289](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_lenders.length%3B%20i%2B%2B)%20%7B)
[FraxlendPair.sol L#308](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_borrowers.length%3B%20i%2B%2B)%20%7B)

[FraxlendPairCore.sol L#265](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_approvedBorrowers.length%3B%20%2B%2Bi)%20%7B)

[FraxlendPairCore.sol L#270](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_approvedLenders.length%3B%20%2B%2Bi)%20%7B)
G-4 ++I COSTS LESS GAS THAN I++, ESPECIALLY WHEN IT’S USED IN FOR-LOOPS (--I/I-- TOO)
[FraxlendPair.sol L#289](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_lenders.length%3B%20i%2B%2B)%20%7B)
[FraxlendPair.sol L#308](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_borrowers.length%3B%20i%2B%2B)%20%7B)

[FraxlendPairCore.sol L#265](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_approvedBorrowers.length%3B%20%2B%2Bi)%20%7B)

[FraxlendPairCore.sol L#270](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_approvedLenders.length%3B%20%2B%2Bi)%20%7B)