1.
Title: `calldata` instead of `memory` for RO function parameters

Impact:
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

Proof of Concept:
[VaultAccount.sol#L17](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol#L17)
[VaultAccount.sol#L34](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol#L34)

Recommended Mitigation Steps:
Replace `memory` with `calldata`
________________________________________________________________________

2.
Title: Using `+=` or `-=` can save gas

Proof of Concept:
[VaultAccount.sol#L26](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol#L26)
[VaultAccount.sol#L43](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol#L43)

Recommended Mitigation Steps:
Change to:

```
    shares += 1;
```
________________________________________________________________________

3.
Title: Use of `uint8` in for loop increases gas costs

Proof of Concept:
[SafeERC20.sol#L22](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L22)

Recommended Mitigation Steps:
Change `uint8` to `uint256`
________________________________________________________________________

4.
Title: Default value initialization

Impact:
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

Proof of Concept:
[SafeERC20.sol#L22](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L22)
[FraxlendPair.sol#L289](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289)
[FraxlendPair.sol#L308](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308)

Recommended Mitigation Steps:
Remove explicit initialization for default values.
________________________________________________________________________

5.
Title: Using unchecked and prefix increment is more effective for gas saving:

Proof of Concept:
[SafeERC20.sol#L22](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L22)
[FraxlendPair.sol#L289](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289)
[FraxlendPair.sol#L308](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308)
[FraxlendPairCore.sol#L265-L270](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265-L270)

Recommended Mitigation Steps:
Change to:

```
for (i = 0; i < 32 && data[i] != 0;) {
  // ...
unchecked { ++i; }
}
```
________________________________________________________________________

6.
Title: Caching `length` for loop can save gas

Proof of Concept:
[FraxlendPair.sol#L289](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289)
[FraxlendPair.sol#L308](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308)
[FraxlendPairCore.sol#L265-L270](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265-L270)

Recommended Mitigation Steps:
Change to:

```   
	uint256 Length = _lenders.length;
        for (uint256 i = 0; i < _lenders.Length; i++) {
```
________________________________________________________________________

7.
Title: Using `storage` instead of `memory` for struct can save gas

Proof of Concept:
[FraxlendPairCore.sol#L419](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L419)
[FraxlendPairCore.sol#L517](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L517)

Recommended Mitigation Steps:
Replace `memory` with `storage`
________________________________________________________________________

8.
Title: Unused lib

Proof of Concept:
[FraxlendPairDeployer.sol#L45](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L45)

Recommended Mitigation Steps:
remove unused can save gas
________________________________________________________________________

9.
Title: Set as `constant` can save gas

Proof of Concept:
[FraxlendPairDeployer.sol#L49-L51](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L49-L51)

Recommended Mitigation Steps:
Change to `constant` and make it `private` or `internal`
________________________________________________________________________

10.
Title: Reduce the size of error messages (Long revert Strings)

Impact:
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

Proof of Concept:
[FraxlendPairDeployer.sol#L205](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L205)
[FraxlendPairDeployer.sol#L228](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L228)
[FraxlendPairDeployer.sol#L253](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L253)
[FraxlendPairDeployer.sol#L365-L369](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L365-L369)

Recommended Mitigation Steps:
Consider shortening the revert strings to fit in 32 bytes
________________________________________________________________________

11.
Title: Custom errors from Solidity 0.8.4 are cheaper than revert strings

Impact:
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information

Custom errors are defined using the error statement
reference: https://blog.soliditylang.org/2021/04/21/custom-errors/

Proof of Concept:
[FraxlendPairDeployer.sol#L205](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L205)
[FraxlendPairDeployer.sol#L228](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L228)
[FraxlendPairDeployer.sol#L253](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L253)
[FraxlendPairDeployer.sol#L365-L369](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L365-L369)

Recommended Mitigation Steps:
Replace require statements with custom errors.
________________________________________________________________________

12.
Title: Remove or replace unused state variable

Impact:
Saves a storage slot. If the variable is assigned a non-zero value, saves Gsset (20000 gas). If it's assigned a zero value, saves Gsreset (2900 gas). If the variable remains unassigned, there is no gas savings. If the state variable is overriding an interface's public function, mark the variable as constant or immutable so that it does not use a storage slot, and manually add a getter function

Reference: [here](https://github.com/code-423n4/2022-05-alchemix-findings/issues/28)

Proof of Concept:
[FraxlendPairCore.sol#L51](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L51)
________________________________________________________________________

13.
Title: Using multiple `require` instead `&&` can save gas

Proof of Concept:
[LinearInterestRate.sol#L57-L68](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L57-L68)

Recommended Mitigation Steps:
Change to:

```
	require(_minInterest < MAX_INT, "LinearInterestRate: _minInterest < MAX_INT");
	require( _minInterest <= _vertexInterest, "LinearInterestRate: _minInterest <= _vertexInterest");
 	require(_minInterest >= MIN_INT, "LinearInterestRate: _minInterest >= MIN_INT");
```
________________________________________________________________________

14.
Title: Using `!=` is more gas efficient in `require` statement

Proof of Concept:
[LinearInterestRate.sol#L66](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L66)

Recommended Mitigation Steps:
Change from `>0` to `!=`

```
	require(
            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization != 0,
            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization != 0"
        );
```
________________________________________________________________________