 # Low Risk Issues

## Summary Of Findings:

  | Issue 
-- | -- 
1 | Zero-check is not performed for address
2 | Use `<=` instead of `<` when checking if Vault has enough funds to transfer

## Details on Findings:

### 1. <ins>Zero-check is not performed for address</ins>

In the `FraxlendPair.sol`, address is not checked for a zero value before assigning it to the `TIME_LOCK_ADDRESS` in the [setTimeLock](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L204) function.

Mitigation would be to add a `require` statement in the function as shown below:

```solidity
    function setTimeLock(address _newAddress) external onlyOwner {
        emit SetTimeLock(TIME_LOCK_ADDRESS, _newAddress);
        require(_newAddress != address(0), "Zero Address");
        TIME_LOCK_ADDRESS = _newAddress;  // @audit zero check
    }
```

Similarly in the `FraxlendPairDeployer.sol`, the [constructor](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L98) doesn't perform any zero checks for important addresses.
```solidity
    constructor(  
        address _circuitBreaker,
        address _comptroller,
        address _timelock,
        address _fraxlendWhitelist
    ) Ownable() {
        CIRCUIT_BREAKER_ADDRESS = _circuitBreaker;
        COMPTROLLER_ADDRESS = _comptroller;
        TIME_LOCK_ADDRESS = _timelock;
        FRAXLEND_WHITELIST_ADDRESS = _fraxlendWhitelist;
    }
```

Again, in the `FraxlendPairCore.sol` file, the [constructor](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L162) doesn't check for zero addresses before assigning important contract addresses.

```solidity
Line 162: {
            (
                address _circuitBreaker,
                address _comptrollerAddress,
                address _timeLockAddress,
                address _fraxlendWhitelistAddress
            ) = abi.decode(_immutables, (address, address, address, address));

            // Deployer contract
            DEPLOYER_ADDRESS = msg.sender;
            CIRCUIT_BREAKER_ADDRESS = _circuitBreaker;
            COMPTROLLER_ADDRESS = _comptrollerAddress;
            TIME_LOCK_ADDRESS = _timeLockAddress;
            FRAXLEND_WHITELIST_ADDRESS = _fraxlendWhitelistAddress;
        }

Line 190:   assetContract = IERC20(_asset);
Line 191:   collateralContract = IERC20(_collateral);
```

### 2. <ins>Use `<=` instead of `<` when checking if Vault has enough funds to transfer</ins>
Though it is unlikely that this will cause a problem, for the readability of logic, it is better to use `<=`. If the fund in the vault is exactly equal to the fund requested then the function shouldn't revert.  

There are 3 instances of this:

 1. [_redeem](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L638) function
 2. [_borrowAsset](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L712) function
 3. [repayAssetWithCollateral](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1200) function
```solidity
Line  638:    if (_assetsAvailable < _amountToReturn) {
Line  712:    if (_assetsAvailable < _borrowAmount) {
Line 1200:    if (_amountAssetOut < _amountAssetOutMin) {
```


 # Non-Critical Issues

## Summary Of Findings:

  | Issue 
-- | -- 
1 | Non-library/interface files should use fixed compiler versions, not floating ones
2 | Wrong Natspec comments
3 | Misleading function names
4 | Not emitting any event for unnatural exceptions

## Details on Findings: 

### 1. <ins>Non-library/interface files should use fixed compiler versions, not floating ones</ins>

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)

All the files under scope uses floating pragma like the one below:
```solidity
pragma solidity ^0.8.15;
```

Mitigation would be to change it to this:
```solidity
pragma solidity 0.8.15;
```

### 2. <ins>Wrong Natspec comments</ins>

See the following instances:

 1. [line 284](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L284)
```solidity
@notice The ```setApprovedLenders``` function sets a given set of addresses to the whitelist
```
while the function can add the addresses to the blacklist as well (and not only whitelist)

 2. [line 303](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L303)
```solidity
@notice The ```setApprovedBorrowers``` function sets a given array of addresses to the whitelist
```
As the function can add the addresses to the blacklist as well (and not only whitelist)

### 3. <ins>Misleading function names</ins>

In the `FraxlendWhitelist` file, the 3 functions, [setOracleContractWhitelist](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L50), [setRateContractWhitelist](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L65) and  [setFraxlendDeployerWhitelist](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L80) are called whitelist but they can be also used to blacklist an address.

### 4. <ins>Not emitting any event for unnatural exceptions</ins>
In the `FraxlendPairCore.sol` file, the `if statement` [here](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L471) should have an `else` which should emit an event. As overflow in the `totalBorrow.amount` or `totalAsset.amount` is a situation worth getting notified.
```solidity
            if (
                _interestEarned + _totalBorrow.amount <= type(uint128).max &&
                _interestEarned + _totalAsset.amount <= type(uint128).max
            )
```



