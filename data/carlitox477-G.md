# FraxlendPair
## setApprovedLenders: iterator asignment and checks
To avoid double memory access, just change ```i++``` for ```++i```. To avoid unnecessary checks, move this assignment inside an unchecked block

```solidity
for (uint256 i = 0; i < _lenders.length; ) {
    // Do not set when _approval == false and _lender == msg.sender
    if (_approval || _lenders[i] != msg.sender) {
        approvedLenders[_lenders[i]] = _approval;
        emit SetApprovedLender(_lenders[i], _approval);
        }
    unchecked{
        ++i
    }
}
```

## setApprovedBorrowers: iterator asignment and checks
To avoid double memory access, just change ```i++``` for ```++i```. To avoid unnecessary checks, move this assignment inside an unchecked block

```solidity
function setApprovedBorrowers(address[] calldata _borrowers, bool _approval) external approvedBorrower {
    for (uint256 i = 0; i < _borrowers.length; ) {
        // Do not set when _approval == false and _borrower == msg.sender
        if (_approval || _borrowers[i] != msg.sender) {
            approvedBorrowers[_borrowers[i]] = _approval;
            emit SetApprovedBorrower(_borrowers[i], _approval);
        }
    }
    unchecked{
        ++i
    }
}
```


# FraxlendPairCore
## _isSolvent double access to storage value
maxLTV is accessed twice, when it can be accessed once.
Add line ```uint256 _maxLTV=maxLTV``` at function start and replace ecery ocurrency of **maxLTV** in the function for **_maxLTV**

# FraxlendPairDeployer
## Constant variables not set to constant
According to comment: **DEFAULT_MAX_LTV**, **GLOBAL_MAX_LTV** and **DEFAULT_LIQ_FEE** should be constant. This can save gas during deployment and in every function execution which use these values.

## CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS and TIME_LOCK_ADDRESS should be set to immutable
This will save gas in every function execution which use these values.

## getAllPairAddresses: iterator asignment
To avoid double memory access, just change ```i++``` for ```++i```

## getCustomStatuses: iterator asignment
To avoid double memory access, just change ```i++``` for ```++i```

## globalPause: iterator asignment
To avoid double memory access, just change ```i = i + 1``` for ```++i```

# FraxlendWhitelist
## setOracleContractWhitelist: iterator asignment and checks
To avoid double memory access, just change ```i++``` for ```++i```. To avoid unnecessary checks, move this assignment inside an unchecked block

```solidity
function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
    for (uint256 i = 0; i < _addresses.length; ) {
        oracleContractWhitelist[_addresses[i]] = _bool;
        emit SetOracleWhitelist(_addresses[i], _bool);
    }
    unchecked{
        ++i
    }
}
```

## setRateContractWhitelist: iterator asignment and checks
To avoid double memory access, just change ```i++``` for ```++i```. To avoid unnecessary checks, move this assignment inside an unchecked block

```solidity
  function setRateContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
    for (uint256 i = 0; i < _addresses.length; ) {
        rateContractWhitelist[_addresses[i]] = _bool;
        emit SetRateContractWhitelist(_addresses[i], _bool);
    }
    unchecked{
        ++i
    }
}
```

## setFraxlendDeployerWhitelist: iterator asignment and checks
To avoid double memory access, just change ```i++``` for ```++i```. To avoid unnecessary checks, move this assignment inside an unchecked block

```solidity
function setFraxlendDeployerWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
    for (uint256 i = 0; i < _addresses.length; ) {
        fraxlendDeployerWhitelist[_addresses[i]] = _bool;
        emit SetFraxlendDeployerWhitelist(_addresses[i], _bool);
    }
    unchecked{
        ++i
    }
}
```