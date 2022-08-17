# State-accessing function marked as pure

[`FraxlendPair.getConstants()`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L156) is marked as `pure` when it should be marked as `view`, as it accesses state.

# State variable formatted like constant

[`FraxlendPairCore.TIME_LOCK_ADDRESS`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L86) is formatted in ALL-CAPS like a constant, but it is not a constant and change be changed by [`FraxlendPair.setTimelock()`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L204). It should thus be written in camelCase to indicate it is a standard state variable (`timeLockAddress`).