# 1) Too recent Solidity version with enabled optimizer
Using too recent Solidity versions could be potentially dangerous, in addition when the optimizer is enabled. This project introduces both, floating pragma to ^0.8.15 and enabled optimizer with 200 runs.

In past there were discovered several bugs (https://docs.soliditylang.org/en/v0.8.16/bugs.html) and also in the latest releases (https://blog.soliditylang.org/2022/06/15/inline-assembly-memory-side-effects-bug/).

## Recommendation
Use more battle-tested version, at least few months old while preserving wanted features and disable the optimizer if it's possible.

# 2) Potentially unwanted `renounceOwnership` feature
Openzeppelin's Ownable contract introduces the `renounceOwnership` function that allows to renounce the owner of the contract. However, if it is not intended to be used in future, it will be safer to disable this function

These contracts will be more solid without this feature:

- FraxlendWhitelist (it will not be possible to whitelist anymore)
- FraxlendPairCore -> FraxlendPair (it will not be possible to withdraw fees)

These contracts are on consideration:

- FraxlendPairDeployer

## Recommendation
Override the `renounceOwnership` function to disable this feature.

# 3) Two-phase ownership transfer
Openzeppelin's Ownable contract introduces only one-phase ownership that transfers the ownership directly. This could be potentially dangerous when some mistake happens, due to that, it is better to use two-phase transfer of the ownership.

## Recommendation
Implement nomination of the owner that can be later claimed on all places where is expected that the owner will be EOA.

# 4) Missing setters on addresses with escalated privileges

In FraxlendPairDeployer there are several addresses with escalated privileges (like `CIRCUIT_BREAKER_ADDRESS` for the `globalPause` function) that are missing setter functions. Adjustment of their values will need to redeploy the contract.

```
		// Admin contracts
    address public CIRCUIT_BREAKER_ADDRESS;
    address public COMPTROLLER_ADDRESS;
    address public TIME_LOCK_ADDRESS;
```

## Recommendation
Since their values are the only ones assigned in the constructor (except `FRAXLEND_WHITELIST_ADDRESS`) it could be useful to add setters. If not, make them immutable.

# 5) Inconsistent casting of `block.timestamp`
The struct `ExchangeRateInfo` is using `uint32` for the preserving value of the `block.timestamp` while other part of the protocols are using the safer value `uint64`.

```
		struct ExchangeRateInfo {
        uint32 lastTimestamp;
        uint224 exchangeRate; // collateral:asset ratio. i.e. how much collateral to buy 1e18 asset
    }
```

## Recommendation
Consider a lifetime of the project if the gas optimization is worthy, since in 2106 it can overflow and cause the protocol unusable.

# 6) Hardcoded number instead of the precision constant
In the `_addInterest` function on line 468, there is hardcoded number for divison, instead of using proper constant for precision

```
// Calculate interest accrued
_interestEarned = (_deltaTime * _totalBorrow.amount * _currentRateInfo.ratePerSec) / 1e18;
```

Second occurence is in the `FraxlendPair` contract.

```
function assetsPerShare() external view returns (uint256 _assetsPerUnitShare) {
        _assetsPerUnitShare = totalAsset.toAmount(1e18, false);
}
```

There should be probably `decimals()` instead.

## Recommendation
Replace the hardcoded numbers with a proper constant to prevent decimal errors in future updates.

# 7) Data validation during deployment
Although it is claimed that the configuration of deployment is left completely up to deployer and the protocol is aware of it, it would be better to include some basic checks, where is already known that they will lead to a unusable protocol.

More precisely, zero-address checks for `_asset` and `_collateral` in `FraxlendPairCore`.

## Recommendation
Add zero-address checks for `_asset` and `_collateral` in `FraxlendPairCore` constructor.