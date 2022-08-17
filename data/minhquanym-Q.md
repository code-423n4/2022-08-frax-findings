# Summary

| Id | Title |
| -- | ----- |
| 1 | Division before multiplication can lead to precision errors |
| 2 | When `exchangeRate` become too large - not fit in uint224, borrowers cannot repay their loans |

## 1. Division before multiplication can lead to precision errors

Since we are working with integer, if we divide before multiply, it can lead to precision errors. 

In this case, it can lead to error in LTV calculation in `_isSolvent()` function, prevent liquidator from liquidate insolvent borrower.

```solidity
uint256 _ltv = (((_borrowerAmount * _exchangeRate) / EXCHANGE_PRECISION) * LTV_PRECISION) / _collateralAmount;
```

### Affected Codes

- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L314

### Recommended Mitigation Steps

- Do multipication before division

## 2. When `exchangeRate` become too large - not fit in uint224, borrowers cannot repay their loans

In `FraxlendPairCore._updateExchangeRate()` function, if exchange rate becomes too large, it will be reverted. And this function is called first every time borrowers want to repay / borrow
```solidity
if (_exchangeRate > type(uint224).max) revert PriceTooLarge();
```

But in case this rate not fit in `uint224` anymore, some extreme cases like LUNA crash for example, it basically caused DOS in borrowing and repaying.

And since borrowers cannot repay their debt, lenders will not able to redeem their assets too.

### Affected Codes
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L542

### Recommended Mitigation Steps

Add a mechanism to allow admin/operator to help borrowers repay in extreme cases.



