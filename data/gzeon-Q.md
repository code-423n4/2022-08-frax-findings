## Low
### `_totalAssetAvailable` may revert due to underflow
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L295-L301

```solidity
    function _totalAssetAvailable(VaultAccount memory _totalAsset, VaultAccount memory _totalBorrow)
        internal
        pure
        returns (uint256)
    {
        return _totalAsset.amount - _totalBorrow.amount;
    }
```

## Non Critical
### Unused param
`_initData` is unused, consider to remove the name from function declaration.
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L63-L64

```solidity
    function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {

```