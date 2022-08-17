## Low Risk Issues

### [L-01] FraxlendPairCore._addInterest() might use wrong `_deltaTime` in the future.
As we can see from below links, `_currentRateInfo.lastTimestamp` saves `uint64(block.timestamp)` but uses raw `block.timestamp` for [_deltaTime](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L441).

- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L441

```
File: 2022-08-frax\src\contracts\FraxlendPairCore.sol
441:             uint256 _deltaTime = block.timestamp - _currentRateInfo.lastTimestamp;
```

- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L464

```
File: 2022-08-frax\src\contracts\FraxlendPairCore.sol
464:             _currentRateInfo.lastTimestamp = uint64(block.timestamp);
```

It will be wrong when `block.timestamp > type(uint64).max` more than 584 * 10^9 years later.

I submit as a low risk because the time is impractical.

We can calculate `_deltaTime` like this.

```
uint256 _deltaTime = uint256(uint64(uint64(block.timestamp) - _currentRateInfo.lastTimestamp));
```

### [L-02] FraxlendPairCore._updateExchangeRate() doesn't check `lastTimestamp` properly.
As we can see from below links, `_exchangeRateInfo.lastTimestamp` saves `uint32(block.timestamp)` but compares with `block.timestamp` for same timestamp.

So after the 2106 year, [this condition](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L518) will be always false and it will update the rate every time.

- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L518

```
File: 2022-08-frax\src\contracts\FraxlendPairCore.sol
518:         if (_exchangeRateInfo.lastTimestamp == block.timestamp) {
519:             return _exchangeRate = _exchangeRateInfo.exchangeRate;
520:         }
```

- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L544

```
File: 2022-08-frax\src\contracts\FraxlendPairCore.sol
544:         _exchangeRateInfo.lastTimestamp = uint32(block.timestamp);
```

We should modify `L518` like below.

```
if (_exchangeRateInfo.lastTimestamp == uint32(block.timestamp)) {
```

### [L-03] Unbounded loops with external calls
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L402


## Non-critical Issues

### [N-01] Nonreentrant modifier should occur before all other modifiers
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L739
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L911
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L954
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1071

### [N-02] Each event should use three indexed fields if there are three or more fields
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L696
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L760
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L846
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L898
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1046
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1046

### [N-03] Immutable addresses should be 0-checked
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L107
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L172-L175
- https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L190-L191
