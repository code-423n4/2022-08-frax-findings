## Wrong comment

```
  // NOTE: reverts if _shares > userBorrowShares
  _repayAsset(_totalBorrow, _amountLiquidatorToRepay, _sharesToLiquidate, msg.sender, _borrower); // liquidator repays shares on behalf of borrower
```

`_shares` should be `_sharesToLiquidate`

```
  // NOTE: reverts if _sharesToLiquidate > userBorrowShares
  _repayAsset(_totalBorrow, _amountLiquidatorToRepay, _sharesToLiquidate, msg.sender, _borrower); // liquidator repays shares on behalf of borrower
```

## Typo in comment

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L287

```
/// @param _approval The approcal status
```

Should be

```
/// @param _approval The approval status
```