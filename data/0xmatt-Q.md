## Low/Non-Critical QA Report

### L-01 Missing Zero-Address Checks

Zero-address checks are a common best practice for input validation of critical address parameters, particularly in constructors and setters. Accidental use of zero-addresses may result in unexpected exceptions, failures or require the redeployment of contracts.

To implement a zero-address check, use a pattern like the following:

```
error ZeroAddress(); // This probably belongs in FraxlendPairConstants
require(DEPLOYER_ADDRESS != address(0), ZeroAddress()); // inside function where value defined
```

The following instances were identified where zero-address checks should be added.

In FraxlendPairCore.sol:

* [Immutables Configuration](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L161-L176) - DEPLOYER_ADDRESS, CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS, TIME_LOCK_ADDRESS. FRAXLEND_WHITELIST_ADDRESS should be checked if whitelists are active.
* [Pair Settings](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L178-L188) - _asset, _collateral, _oracleMultiply, _oracleDivide, and _rateContract.

In FraxlendPairDeployer.sol:

* [Constructor](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L98-L108) - CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS, TIME_LOCK_ADDRESS. FRAXLEND_WHITELIST_ADDRESS should be checked if whitelists are active.
* [_deploySecond](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L244) - _pairAddress should be checked here instead of in deploy and deployCustom().

In FraxlendWhitelist.sol:

* [setOracleContractWhitelist](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L50-L55) - addresses[i] in the for loop.
* [setRateContractWhitelist](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L65-L70) - addresses[i] in for loop.
* [setFraxlendDeployerWhitelist](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L80-L85) - addresses[i] in for loop.

### L-02 Unchecked ERC20.approve() Values

Not all ERC20 tokens revert on approval failure. Some (such as USDT) will return false instead. This project ignores the return value of ERC20.approve() on several occasions:

[leveragedPosition()](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1103-L1104) - If this approve fails without a revert the code will still call the swapper. Not all swappers act the same (Uniswap v2 returns uint[] memory amounts for example, other swappers may act differently).
[repayAssetWithCollateral()](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1184-L1194) - If this approve fails without a revert the contract will still call the swapper.

I've marked this as low as it's a last-day finding and I don't have time to test it, but it's worth the devs testing a mock swapper that doesn't revert if not approved to see the impact on the rest of these two functions. Remember that just because an approves can fail when allowances are already set. It's possible that the contract may enter an unexpected state with regards to positions and collateral, but I lacked the time to test.

### L-03 Use Of .approve() Might Fail For Some Tokens

Some ERC20 Tokens (most notably USDT) won't permit the use of .approve() to update an approval from one non-zero value to another. This can trigger situations such as outlined in my L-02 finding where an approve fails because one already exists and the swapper may act unexpectedly.

There are several ways to handle approves. The first (and not recommended) is to approve the token for 0, then for the amount. This is discouraged as this is a workaround for non-standard ERC tokens, where the expectation is they will already behave in a non-standard manner.

A preferred option - given the welcome decision to use SafeERC20 would be to use SafeERC20's increaseAllowance() and decreaseAllowance() functions instead of approve().

The instances where using SafeERC20 functions may be better are:

* [leveragedPosition()](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1103-L1104)
* [repayAssetWithCollateral()](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1184-L1194)
* [_redeem()](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L633)
