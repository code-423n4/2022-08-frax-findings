## QA Report

### Use of unbound array for `deployedPairsArray`

**Severity**: Medium

Context: [`FraxlendPairDeployer.sol#L96`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L96)

`deployedPairsArray` is a string[] that contains the names of each deployed pair. However, there is no maximium length enforced on this array. Additionally, there is no maximium length enforced on the length of the name. Furthermore, there is no means to remove items from this list.

**Impact**: 

In `getAllPairAddresses()` this array is copied to memory then iterated over to create another in-memory array of the same length. The longer the array, and the longer the string elements of the array are, the more gas will be consumed by an external contract that calls this method within a transaction. Furthermore, even when calling this method off-chain, a large amount of data could make the returnvalue of this function difficult or impossible to deal with.


```
[PASS] testGetAllPairAddressesMulti()
Logs:
  gas used with 1 pair with 32 byte name: 3702
  gas used with 100 pairs with 100 byte name: 383162
  gas used with 1000 pairs with 100 byte name: 3818626
  gas used with 10000 pairs with 100 byte name: 38156200

```

**Recommendation**: 

1. **Implement removeDeployedPair** - Implement a new method that removes a pair from the deployedPairsArray. The main goal of this would be to limit the length of this array. If needed, the removed pair names could be stored in a separate array and/or removed from the deployedPairsByName mapping.
```diff
index f8d959e..8f5077a 100644
--- a/src/contracts/FraxlendPairDeployer.sol
+++ b/src/contracts/FraxlendPairDeployer.sol
@@ -111,6 +111,22 @@ contract FraxlendPairDeployer is Ownable {
     // Functions: View Functions
     // ============================================================================================

+    function removeDeployedPair(string calldata name) public ownerOnly {
+        string[] memory _deployedPairsArray = deployedPairsArray;
+        uint256 _lengthOfArray = _deployedPairsArray.length;
+        for (uint i; i < _lengthOfArray; ) {
+            if (keccak256(bytes(_deployedPairsArray[i])) == keccak256(bytes(name))) {
+                deployedPairsArray[i] = deployedPairsArray[_lengthOfArray - 1];
+                deployedPairsArray.pop();
+                return;
+            }
+            unchecked {
+                i++;
+            }
+        }
+    }
+
+
```


-----------------------

### ERC-4626 functions `previewDeposit` and `previewMint` don't revert when they should

**Severity**: Low

The Fraxlend previewDeposit and previewMint functions are ERC-4626 complatibility functions. The [ERC-4626 spec](https://eips.ethereum.org/EIPS/eip-4626#definitions) intends these functions to effectively "simulate" a deposit or mint, and to revert if the corresponding deposit or mint would revert. 

In its current implementation, previewDeposit and previewMint do not revert when the vault is paused or past maturity; however, the [deposit](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPairCore.sol#L583-L586) and [mint](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPairCore.sol#L602-L606) functions _do_ revert when the vault is paused or past maturity. 

**Impact**
The vault/pair will not be fully ERC-4626 compliant. Strict ERC-4626 clients may not be able to interact with the protocol.

**Recommendation**: 
This can be rememdiated by adding the `isNotPastMaturity` and `whenNotPaused` modifiers to the following functions:

- [previewDeposit](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L120)
- [previewMint](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L124)


### ERC-4626 functions `maxDeposit` and `maxMint` return incorrect results

**Severity**: Low

The Fraxlend [maxDeposit](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L136) and [maxMint](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L140) functions are ERC-4626 complatibility functions. The [ERC-4626 spec](https://eips.ethereum.org/EIPS/eip-4626#definitions) intends these functions to be used to indicate global & user-specific deposit limitations.

In its current implementation, maxDeposit and maxMint always return `type(uint128).max`, regardless of whether the vault is paused or if the address corresponds to an approved lender.

**Impact**
The vault/pair will not be fully ERC-4626 compliant. Strict ERC-4626 clients may not be able to interact with the protocol.

**Recommendation**: 
To remediate this, the maxDeposit and maxMint functions must:

1. Check _isPastMaturity(). If true, return 0.
2. Check paused(). If true, return 0.
3. Check whether the provided address is an approved lender using [this logic](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPairCore.sol#L352). If the address is not an approved lender, return 0.
4. If it gets this far, return `type(uint128).max`


-----------------------

### `previewRateInterest()` does not handle edge case
**Severity**: Low

Context: [`FraxlendPairHelper.sol#L155`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L432)

Not in scope, but just wanted to point out that `previewRateInterest` does not handle the edge case when the rate had been updated in the same block.  So the result will be different than if the new rate generated by `_addInterest`.  Specific logic missing:

```
        CurrentRateInfo memory _currentRateInfo = currentRateInfo;
        if (_currentRateInfo.lastTimestamp == block.timestamp) {
            _newRate = _currentRateInfo.ratePerSec;
            return (_interestEarned, _feesAmount, _feesShare, _newRate);
        }

```
**Impact**: 

May cause some problems with those trying to integrate with the protocol and expecting an accurate preview.

**Recommendation**: 

Add missing edge case logic shown above.


-----------------------

### `addInterest()` missing event

**Severity**: Low

Context: [`FraxlendPairCore.sol#L432`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L432)

**Impact**: 

May cause some problems with those trying to integrate with the protocol or with the front end.

**Recommendation**: 

Emit event `UpdateRate` event.


-----------------------

### FraxlendPairCore Constructor missing @param natspec

**Severity**: Informational

Context: [`FraxlendPairCore.sol#L153`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L153)

`_immutables` param does not have a natspec

-----------------------

### FraxlendPairCore `_addInterest()` incorrect comment

**Severity**: Informational

Context: [`FraxlendPairCore.sol#L429`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L429)

Comment says:
```
// If there are no borrows or contract is paused, no interest accrues and we reset interest rate
```
but actually the rate only resets if the shares == 0 but its not paused.

-----------------------

### FraxlendPairCore `updateExchangeRate()` unnecessary assignment

**Severity**: Informational / Gas

Context: [`FraxlendPairCore.sol#L519`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L519)


It's unneccesary to assign a value to the named return var if you're returning it. 

```
    function _updateExchangeRate() internal returns (uint256 _exchangeRate) {
        ExchangeRateInfo memory _exchangeRateInfo = exchangeRateInfo;
        if (_exchangeRateInfo.lastTimestamp == block.timestamp) {
            return _exchangeRate = _exchangeRateInfo.exchangeRate;
```

Just return it `return _exchangeRateInfo.exchangeRate;`

-----------------------

### FraxlendPairDeployer deprecated import location

**Severity**: Informational

Context: [`FraxlendPairDeployer.sol#L31`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L31)

`Solmate` is now located in Transmissions11's repo.

https://github.com/transmissions11/solmate
