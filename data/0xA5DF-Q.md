## Nominate admin to manage whitelists
Lines: https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPair.sol#L276-L312


Currently the lenders and borrowers whitelists are managed by themselves - approved borrower can add/remove any borrower from the whitelist and the same goes for approved lender.
This isn't the best practice, since it's mixing the role of managing the whitelists and being an approved lender/borrower. 
This can lead to an approved borrower/lender (might be one added by mistake, a friend of a friend of a friend of a member of the original whitelist or a compromised wallet) taking over and removing all other members from the list.

### Mitigation
Consider creating a different role of an admin that would manage the whitelists.


## Missing zero approval before approval
USDT requires the approved amount to be zero before approving a non-zero amount.
Even though the approved amount is expected to always be zero at the beginning of tx (since the swapper always uses all of the approved amount), it's better to approve zero to avoid any kind of errors (even if there's 1 wei of USDT left approved due to some error or rounding with the swapper, it'll make the functions that use approval unusable).

Code:
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1184
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1103

## multiple instances of public pairs with same config can be created, due to `_configData` length not being checked
The deployer tried to prevent creation of multiple pairs with same config data by hashing the config data and checking if a pair with a similar hash has been created.
However, this can by bypassed by padding the `_configData` with random data. Since `abi.decode()` doesn't check the input bytes size, this will still pass as a valid config and create pair with same config as without the padding (while the hash would be different due to the padding).


### PoC
`testCannotDeployTwicePublic()` would fail with `Call did not revert as expected` when applying following diff:

```diff
diff --git a/src/test/e2e/FraxlendPairDeployerTest.sol b/src/test/e2e/FraxlendPairDeployerTest.sol
index 18eeb1e..e1c836c 100644
--- a/src/test/e2e/FraxlendPairDeployerTest.sol
+++ b/src/test/e2e/FraxlendPairDeployerTest.sol
@@ -39,7 +39,8 @@ contract FraxlendPairDeployerTest is BasePairTest {
                 address(oracleDivide),
                 1e10,
                 address(linearRateContract),
-                _rateInitData2
+                _rateInitData2,
+                address(oracleDivide)
             )
         );
     }
```

### Mitigation
Check that the config data is the expected length (it contains encoded bytes of `_rateInitData2`, but that should have a fixed length too).


## Admin can bypass timelock by replacing it
The ability to change the timelock address without any delay defeats the whole purpose of the timelock address.
The idea of the timelock is to delay admin actions and prevent any surprises to users, however if the admin can replace the contract without any delay, he can simply replace it with a non-timelock address and execute actions immediately.


### Mitigation
Make the `setTimeLock` a 2 step actions, with a delay (the same delay as the timelock contract) between.

## Move division to after second multiplication, to get a more precise results
Code:
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L929-L932
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L970-L991
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L314

In order to loose less to rounding it's better to move any division to after multiplication:

Mitigation diff:
```diff
diff --git a/src/contracts/FraxlendPairCore.sol b/src/contracts/FraxlendPairCore.sol
index a712d46..58cba44 100644
--- a/src/contracts/FraxlendPairCore.sol
+++ b/src/contracts/FraxlendPairCore.sol
@@ -311,7 +311,7 @@ abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ow
         uint256 _collateralAmount = userCollateralBalance[_borrower];
         if (_collateralAmount == 0) return false;
 
-        uint256 _ltv = (((_borrowerAmount * _exchangeRate) / EXCHANGE_PRECISION) * LTV_PRECISION) / _collateralAmount;
+        uint256 _ltv = ((_borrowerAmount * _exchangeRate) * LTV_PRECISION) / (_collateralAmount * EXCHANGE_PRECISION);
         return _ltv <= maxLTV;
     }
 
@@ -927,9 +927,9 @@ abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ow
 
         // Determine how much of the borrow and collateral will be repaid
         _collateralForLiquidator =
-            (((_totalBorrow.toAmount(_shares, false) * _exchangeRate) / EXCHANGE_PRECISION) *
+            ((_totalBorrow.toAmount(_shares, false) * _exchangeRate) *
                 (LIQ_PRECISION + cleanLiquidationFee)) /
-            LIQ_PRECISION;
+            (LIQ_PRECISION * EXCHANGE_PRECISION);
 
         // Effects & Interactions
         // NOTE: reverts if _shares > userBorrowShares
@@ -972,12 +972,12 @@ abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ow
             // Checks & Calculations
             // Determine the liquidation amount in collateral units (i.e. how much debt is liquidator going to repay)
             uint256 _liquidationAmountInCollateralUnits = ((_totalBorrow.toAmount(_sharesToLiquidate, false) *
-                _exchangeRate) / EXCHANGE_PRECISION);
+                _exchangeRate) );
 
             // We first optimistically calculate the amount of collateral to give the liquidator based on the higher clean liquidation fee
             // This fee only applies if the liquidator does a full liquidation
             uint256 _optimisticCollateralForLiquidator = (_liquidationAmountInCollateralUnits *
-                (LIQ_PRECISION + cleanLiquidationFee)) / LIQ_PRECISION;
+                (LIQ_PRECISION + cleanLiquidationFee)) / (LIQ_PRECISION * EXCHANGE_PRECISION);
 
             // Because interest accrues every block, _liquidationAmountInCollateralUnits from a few lines up is an ever increasing value
             // This means that leftoverCollateral can occasionally go negative by a few hundred wei (cleanLiqFee premium covers this for liquidator)
@@ -987,7 +987,7 @@ abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ow
             // This will only be true when there liquidator is cleaning out the position
             _collateralForLiquidator = _leftoverCollateral <= 0
                 ? _userCollateralBalance
-                : (_liquidationAmountInCollateralUnits * (LIQ_PRECISION + dirtyLiquidationFee)) / LIQ_PRECISION;
+                : (_liquidationAmountInCollateralUnits * (LIQ_PRECISION + dirtyLiquidationFee)) / (LIQ_PRECISION * EXCHANGE_PRECISION);
         }
         // Calced here for use during repayment, grouped with other calcs before effects start
         uint128 _amountLiquidatorToRepay = (_totalBorrow.toAmount(_sharesToLiquidate, true)).toUint128();

```


