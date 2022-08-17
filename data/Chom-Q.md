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
