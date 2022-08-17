### Low Risk Findings Overview
|        | Finding                                                                                                                     |  Instances  |
|:-------|:----------------------------------------------------------------------------------------------------------------------------|:-----------:|
| [L-01] | Floating Pragma                                                                                                             |      1      |
| [L-02] | `approve` has been deprecated                                                                                               |      4      |
| [L-03] | `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` |      1      |
| [L-04] | Missing zero address check might lead to redeployment                                                                       |      1      |
### Non-critical Findings Overview
|        | Finding                                     |  Instances  |
|:-------|:--------------------------------------------|:-----------:|
| [N-01] | The use of magic numbers is not recommended |      3      |
| [N-02] | Unindexed parameters in events              |      4      |
### QA overview per contract
| Contract                                                                                                                                                    |  Total Instances  |  Total Findings  |  Low Findings  |  Low Instances  |  NC Findings  |  NC Instances  |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------:|:----------------:|:--------------:|:---------------:|:-------------:|:--------------:|
| [FraxlendPairCore.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol)         |         9         |        4         |       3        |        6        |       1       |       3        |
| [FraxlendPairDeployer.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol) |         4         |        2         |       1        |        1        |       1       |       3        |
| [FraxlendWhitelist.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol)       |         1         |        1         |       0        |        0        |       1       |       1        |
## Low Risk Findings
### [L-01] Floating Pragma
A floating pragma might result in contract being tested/deployed with different compiler versions possibly leading to unexpected behaviour.
*1 instance of this issue has been found:*
###### [L-01] [FraxlendPairCore.sol#L2-L3](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L2-L3)
```solidity

pragma solidity ^0.8.15;

```
### [L-02] `approve` has been deprecated
Please use `safeIncreaseAllowance` and `safeDecreaseAllowance` instead
*4 instances of this issue have been found:*
###### [L-02] [FraxlendPairCore.sol#L1184-L1185](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1184-L1185)
```solidity

        _collateralContract.approve(_swapperAddress, _collateralToSwap);

```
###### [L-02b] [FraxlendPairCore.sol#L1103-L1104](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1103-L1104)
```solidity

        _assetContract.approve(_swapperAddress, _borrowAmount);

```
###### [L-02c] [FraxlendPairCore.sol#L1184-L1185](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1184-L1185)
```solidity

        _collateralContract.approve(_swapperAddress, _collateralToSwap);

```
###### [L-02d] [FraxlendPairCore.sol#L633-L634](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L633-L634)
```solidity

            if (allowed != type(uint256).max) _approve(_owner, msg.sender, allowed - _shares);

```
### [L-03] `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456)`. If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` instead.
*1 instance of this issue has been found:*
###### [L-03] [FraxlendPairDeployer.sol#L204-L205](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L204-L205)
```solidity

            bytes32 salt = keccak256(abi.encodePacked(_saltSeed, _configData));

```
### [L-04] Missing zero address check might lead to redeployment
If variable is accidentally set to zero the contract will have to be redeployed.
*1 instance of this issue has been found:*
###### [L-04] [FraxlendPairCore.sol#L171-L176](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L171-L176)
```solidity

            DEPLOYER_ADDRESS = msg.sender;
            CIRCUIT_BREAKER_ADDRESS = _circuitBreaker;
            COMPTROLLER_ADDRESS = _comptrollerAddress;
            TIME_LOCK_ADDRESS = _timeLockAddress;
            FRAXLEND_WHITELIST_ADDRESS = _fraxlendWhitelistAddress;
        }
```

## Non-critical Findings
### [N-01] The use of magic numbers is not recommended
Consider setting constant numbers as a `constant` variable for better readability and clarity.
*3 instances of this issue have been found:*
###### [N-01] [FraxlendPairDeployer.sol#L171-L172](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L171-L172)
```solidity

        bytes memory _firstHalf = BytesLib.slice(_creationCode, 0, 13000);

```
###### [N-01b] [FraxlendPairDeployer.sol#L173-L174](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L173-L174)
```solidity

        if (_creationCode.length > 13000) {

```
###### [N-01c] [FraxlendPairDeployer.sol#L174-L175](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L174-L175)
```solidity

            bytes memory _secondHalf = BytesLib.slice(_creationCode, 13000, _creationCode.length - 13000);

```
### [N-02] Unindexed parameters in events 
Events should index all first three parameters.
*4 instances of this issue have been found:*
###### [N-02] [FraxlendPairCore.sol#L1143-L1149](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1143-L1149)
```solidity

    event RepayAssetWithCollateral(
        address indexed _borrower,
        address _swapperAddress,
        uint256 _collateralToSwap,
        uint256 _amountAssetOut,
        uint256 _sharesRepaid
    );
```
###### [N-02b] [FraxlendPairCore.sol#L1045-L1052](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1045-L1052)
```solidity

    event LeveragedPosition(
        address indexed _borrower,
        address _swapperAddress,
        uint256 _borrowAmount,
        uint256 _borrowShares,
        uint256 _initialCollateralAmount,
        uint256 _amountCollateralOut
    );
```
###### [N-02c] [FraxlendWhitelist.sol#L45-L46](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L45-L46)
```solidity

    event SetOracleWhitelist(address indexed _address, bool _bool);

```
###### [N-02d] [FraxlendPairCore.sol#L897-L904](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L897-L904)
```solidity

    event Liquidate(
        address indexed _borrower,
        uint256 _collateralForLiquidator,
        uint256 _sharesToLiquidate,
        uint256 _amountLiquidatorToRepay,
        uint256 _sharesToAdjust,
        uint256 _amountToAdjust
    );
```

