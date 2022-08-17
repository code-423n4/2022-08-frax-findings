## Fraxlend QA Reports

Risk | title
---|---
L00 | `dirtyLiquidationFee` mismatches comments
L01 | risky zero `maxLTV`

# Low-00: `dirtyLiquidationFee` mismatches comments

The `dirtyLiquidationFee` is currently 9% of clean fee, which does not match the comment of 90% of clean fee.
The fee information is used in the `FraxlendPairCore::liquidateClean` function (line 990)


```solidity
// FraxlendPairCore.sol
 193             cleanLiquidationFee = _liquidationFee;
 194             dirtyLiquidationFee = (_liquidationFee * 9000) / LIQ_PRECISION; // 90% of clean fee

 // FraxlendPairConstants.sol
  35     uint256 internal constant LIQ_PRECISION = 1e5;
```

# Low-01: risky zero `maxLTV`

When `maxLTV` is zero, the function `_isSolvent` always returns true, meaning everybody is solvent regardless to the borrow or collateral amount.
When undercollateralized loan is possible, the borrow white list is forced to be active. But the white list is not enforced in the case is zero `maxLTV`. Given zero `maxLTV` is riskier because everybody is considered solvent regardless to the collateral and borrow amount, there should be safe guard against it.


```solidity
// FraxlendPairCore.sol
// line 308: when maxLTV is zero, everybody is solvent

 307     function _isSolvent(address _borrower, uint256 _exchangeRate) internal view returns (bool) {
 308         if (maxLTV == 0) return true;
 309         uint256 _borrowerAmount = totalBorrow.toAmount(userBorrowShares[_borrower], true);
 310         if (_borrowerAmount == 0) return true;
 311         uint256 _collateralAmount = userCollateralBalance[_borrower];
 312         if (_collateralAmount == 0) return false;
 313
 314         uint256 _ltv = (((_borrowerAmount * _exchangeRate) / EXCHANGE_PRECISION) * LTV_PRECISION) / _collateralAmount;
 315         return _ltv <= maxLTV;
 316     }

// FraxlendPairCore constructor
// when undercollaterized loan is possible, the borrow whitelist is forced to be active
// but not when the maxLTV is zero

 196             if (_maxLTV >= LTV_PRECISION && !_isBorrowerWhitelistActive) revert BorrowerWhitelistRequired();
 197             maxLTV = _maxLTV;
```

<!-- zzzitron QA -->