## 1. Multiplication before division

Solidity integer division might truncate. As a result, performing multiplication before division can sometimes avoid loss of precision.

### Proof of Concept

[`_isSolvent`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L314)

### Recommended Mitigation Steps

You can first multiply `LTV_PRECISION` with the result of `(_borrowerAmount * _exchangeRate)` and then divide the result by `EXCHANGE_PRECISION`. 