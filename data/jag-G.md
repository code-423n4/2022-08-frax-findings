https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol

1. No need to initialize  variable to 0.
2. Use unchecked in loops since end condition is within range.
3. Use pre increment (++i instead of i++)

```git diff
diff --git a/src/contracts/libraries/SafeERC20.sol b/src/contracts/libraries/SafeERC20.sol
index 0682d78..800b9e8 100644
--- a/src/contracts/libraries/SafeERC20.sol
+++ b/src/contracts/libraries/SafeERC20.sol
@@ -19,13 +19,18 @@ library SafeERC20 {
         if (data.length >= 64) {
             return abi.decode(data, (string));
         } else if (data.length == 32) {
-            uint8 i = 0;
+            uint8 i;
             while (i < 32 && data[i] != 0) {
-                i++;
+                unchecked {
+                    ++i;
+                }
             }
             bytes memory bytesArray = new bytes(i);
-            for (i = 0; i < 32 && data[i] != 0; i++) {
+            for (i; i < 32 && data[i] != 0;) {
                 bytesArray[i] = data[i];
+                unchecked {
+                    ++i;
+                }
             }
             return string(bytesArray);
         } else {
```
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol
1. Use pre increment (++i instead of i++) 

``` git diff
diff --git a/src/contracts/libraries/VaultAccount.sol b/src/contracts/libraries/VaultAccount.sol
index af33161..efb0711 100644
--- a/src/contracts/libraries/VaultAccount.sol
+++ b/src/contracts/libraries/VaultAccount.sol
@@ -23,7 +23,7 @@ library VaultAccountingLibrary {
         } else {
             shares = (amount * total.shares) / total.amount;
             if (roundUp && (shares * total.amount) / total.shares < amount) {
-                shares = shares + 1;
+                ++shares;
             }
         }
     }
@@ -40,7 +40,7 @@ library VaultAccountingLibrary {
         } else {
             amount = (shares * total.amount) / total.shares;
             if (roundUp && (amount * total.shares) / total.amount < shares) {
-                amount = amount + 1;
+                ++amount;
             }
         }
     }
```
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol
1. No need to initialize  variable to 0.
2. Use unchecked in loops since end condition is within range.
3. Use pre increment (++i instead of i++)

``` git diff
diff --git a/src/contracts/FraxlendWhitelist.sol b/src/contracts/FraxlendWhitelist.sol
index da34880..0e85bdf 100644
--- a/src/contracts/FraxlendWhitelist.sol
+++ b/src/contracts/FraxlendWhitelist.sol
@@ -48,9 +48,12 @@ contract FraxlendWhitelist is Ownable {
     /// @param _addresses addresses to set status for
     /// @param _bool status of approval
     function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
-        for (uint256 i = 0; i < _addresses.length; i++) {
+        for (uint256 i; i < _addresses.length;) {
             oracleContractWhitelist[_addresses[i]] = _bool;
             emit SetOracleWhitelist(_addresses[i], _bool);
+            unchecked {
+                ++i;
+            }
         }
     }
 
@@ -63,9 +66,12 @@ contract FraxlendWhitelist is Ownable {
     /// @param _addresses addresses to set status for
     /// @param _bool status of approval
     function setRateContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
-        for (uint256 i = 0; i < _addresses.length; i++) {
+        for (uint256 i; i < _addresses.length;) {
             rateContractWhitelist[_addresses[i]] = _bool;
             emit SetRateContractWhitelist(_addresses[i], _bool);
+            unchecked {
+                ++i;
+            }
         }
     }
 
@@ -78,9 +84,12 @@ contract FraxlendWhitelist is Ownable {
     /// @param _addresses addresses to set status for
     /// @param _bool status of approval
     function setFraxlendDeployerWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
-        for (uint256 i = 0; i < _addresses.length; i++) {
+        for (uint256 i; i < _addresses.length;) {
             fraxlendDeployerWhitelist[_addresses[i]] = _bool;
             emit SetFraxlendDeployerWhitelist(_addresses[i], _bool);
+            unchecked {
+                ++i;
+            }
         }
     }
 }
```
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol
1. No need to initialize  variable to 0.
2. Use unchecked in loops since end condition is within range.
3. Use pre increment (++i instead of i++)
4.  Use local variable to refer storage variable
``` git diff
diff --git a/src/contracts/FraxlendPairCore.sol b/src/contracts/FraxlendPairCore.sol
index a712d46..5782b72 100644
--- a/src/contracts/FraxlendPairCore.sol
+++ b/src/contracts/FraxlendPairCore.sol
@@ -262,13 +262,19 @@ abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ow
         nameOfContract = _name;
 
         // Set approved borrowers
-        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
+        for (uint256 i; i < _approvedBorrowers.length;) {
             approvedBorrowers[_approvedBorrowers[i]] = true;
+            unchecked {
+                ++i;
+            }
         }
 
         // Set approved lenders
-        for (uint256 i = 0; i < _approvedLenders.length; ++i) {
+        for (uint256 i; i < _approvedLenders.length;) {
             approvedLenders[_approvedLenders[i]] = true;
+            unchecked {
+                ++i;
+            }
         }
 
         // Reverts if init data is not valid
@@ -305,14 +311,16 @@ abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ow
     /// @param _exchangeRate The exchange rate, i.e. the amount of collateral to buy 1e18 asset
     /// @return Whether borrower is solvent
     function _isSolvent(address _borrower, uint256 _exchangeRate) internal view returns (bool) {
-        if (maxLTV == 0) return true;
+        //Use local variable to refer storage variable
+        uint256 _maxLTV = maxLTV;
+        if (_maxLTV == 0) return true;
         uint256 _borrowerAmount = totalBorrow.toAmount(userBorrowShares[_borrower], true);
         if (_borrowerAmount == 0) return true;
         uint256 _collateralAmount = userCollateralBalance[_borrower];
         if (_collateralAmount == 0) return false;
 
         uint256 _ltv = (((_borrowerAmount * _exchangeRate) / EXCHANGE_PRECISION) * LTV_PRECISION) / _collateralAmount;
-        return _ltv <= maxLTV;
+        return _ltv <= _maxLTV;
     }
 
     /// @notice The ```_isPastMaturity``` function determines if the current block timestamp is past the maturityDate date
```
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol
1. No need to initialize  variable to 0.
2. Use unchecked in loops since end condition is within range.
3. Use pre increment (++i instead of i++)
4.  Use local variable to refer calldata
``` git diff
diff --git a/src/contracts/FraxlendPair.sol b/src/contracts/FraxlendPair.sol
index d54b8f5..71c08c2 100644
--- a/src/contracts/FraxlendPair.sol
+++ b/src/contracts/FraxlendPair.sol
@@ -286,12 +286,16 @@ contract FraxlendPair is FraxlendPairCore {
     /// @param _lenders The addresses whos status will be set
     /// @param _approval The approcal status
     function setApprovedLenders(address[] calldata _lenders, bool _approval) external approvedLender(msg.sender) {
-        for (uint256 i = 0; i < _lenders.length; i++) {
+        uint256 _len = _lenders.length;
+        for (uint256 i; i < _len;) {
             // Do not set when _approval == false and _lender == msg.sender
             if (_approval || _lenders[i] != msg.sender) {
                 approvedLenders[_lenders[i]] = _approval;
                 emit SetApprovedLender(_lenders[i], _approval);
             }
+            unchecked {
+                ++i;
+            }
         }
     }
 
@@ -305,12 +309,16 @@ contract FraxlendPair is FraxlendPairCore {
     /// @param _borrowers The addresses whos status will be set
     /// @param _approval The approcal status
     function setApprovedBorrowers(address[] calldata _borrowers, bool _approval) external approvedBorrower {
-        for (uint256 i = 0; i < _borrowers.length; i++) {
+        uint256 _len = _borrowers.length;
+        for (uint256 i ; i < _len;) {
             // Do not set when _approval == false and _borrower == msg.sender
             if (_approval || _borrowers[i] != msg.sender) {
                 approvedBorrowers[_borrowers[i]] = _approval;
                 emit SetApprovedBorrower(_borrowers[i], _approval);
             }
+            unchecked {
+                ++i;
+            }
         }
     }
```
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol
1. No need to initialize  variable to 0.
2. Use pre increment (++i instead of i++)
``` git diff
diff --git a/src/contracts/FraxlendPairDeployer.sol b/src/contracts/FraxlendPairDeployer.sol
index f8d959e..16f28c1 100644
--- a/src/contracts/FraxlendPairDeployer.sol
+++ b/src/contracts/FraxlendPairDeployer.sol
@@ -124,10 +124,10 @@ contract FraxlendPairDeployer is Ownable {
         uint256 _lengthOfArray = _deployedPairsArray.length;
         address[] memory _addresses = new address[](_lengthOfArray);
         uint256 i;
-        for (i = 0; i < _lengthOfArray; ) {
+        for (; i < _lengthOfArray; ) {
             _addresses[i] = deployedPairsByName[_deployedPairsArray[i]];
             unchecked {
-                i++;
+                ++i;
             }
         }
         return _addresses;
@@ -149,13 +149,13 @@ contract FraxlendPairDeployer is Ownable {
         uint256 _lengthOfArray = _addresses.length;
         uint256 i;
         _pairCustomStatuses = new PairCustomStatus[](_lengthOfArray);
-        for (i = 0; i < _lengthOfArray; ) {
+        for (; i < _lengthOfArray; ) {
             _pairCustomStatuses[i] = PairCustomStatus({
                 _address: _addresses[i],
                 _isCustom: deployedPairCustomStatusByAddress[_addresses[i]]
             });
             unchecked {
-                i++;
+                ++i;
             }
         }
     }
@@ -399,13 +399,13 @@ contract FraxlendPairDeployer is Ownable {
         require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");
         address _pairAddress;
         uint256 _lengthOfArray = _addresses.length;
-        for (uint256 i = 0; i < _lengthOfArray; ) {
+        for (uint256 i; i < _lengthOfArray; ) {
             _pairAddress = _addresses[i];
             try IFraxlendPair(_pairAddress).pause() {
                 _updatedAddresses[i] = _addresses[i];
             } catch {}
             unchecked {
-                i = i + 1;
+                ++i;
             }
         }
     }
```