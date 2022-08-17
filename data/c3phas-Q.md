## QA

### Missing checks for address(0x0) when assigning values to address state variables
File: FraxlendPairDeployer.sol [Line 104](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L104)
```solidity
        CIRCUIT_BREAKER_ADDRESS = _circuitBreaker;
```

File: FraxlendPairDeployer.sol [Line 105](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L105)
```solidity
        COMPTROLLER_ADDRESS = _comptroller;
```

File: FraxlendPairDeployer.sol [Line 106](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L106)
```solidity
        TIME_LOCK_ADDRESS = _timelock;
```

File: FraxlendPairDeployer.sol [Line 107](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L107)
```solidity
        FRAXLEND_WHITELIST_ADDRESS = _fraxlendWhitelist;
```

### constants should be defined rather than using magic numbers
There are several occurrences of literal values with unexplained meaning .Literal values in the codebase without an explained meaning make the code harder to read, understand and maintain, thus hindering the experience of developers, auditors and external contributors alike.

Developers should define a constant variable for every magic value used , giving it a clear and self-explanatory name.

File: VariableInterestRate.sol [Line 69](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L69)
**1e18**
```solidity
            uint256 _deltaUtilization = ((MIN_UTIL - _utilization) * 1e18) / MIN_UTIL;
```

File: VariableInterestRate.sol [Line 76](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L76)
**1e18**
```solidity
            uint256 _deltaUtilization = ((_utilization - MAX_UTIL) * 1e18) / (UTIL_PREC - MAX_UTIL);
```

File: FraxlendPairCore.sol [Line 194](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L194)
**9000**
```solidity
            dirtyLiquidationFee = (_liquidationFee * 9000) / LIQ_PRECISION; // 90% of clean fee
```

File: FraxlendPairCore.sol [Line 468](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L468)
**1e18**
```solidity
            _interestEarned = (_deltaTime * _totalBorrow.amount * _currentRateInfo.ratePerSec) / 1e18;
```

File: FraxlendPairCore.sol [Line 522](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L522)
**1e36**
```solidity
        uint256 _price = uint256(1e36);
```

File: FraxlendPair.sol [Line 105](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L105)
```solidity
        _assetsPerUnitShare = totalAsset.toAmount(1e18, false);
```

### Lock pragmas to specific compiler version
Contracts should be deployed with the same compiler version and flags that they have been tested the most with. Locking the pragma helps ensure that contracts do not accidentally get deployed using, for example, the latest compiler which may have higher risks of undiscovered bugs. Contracts may also be deployed by others and the pragma indicates the compiler version intended by the original authors.

File: LinearInterestRate.sol [Line 2](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L2)
```solidity
pragma solidity ^0.8.15;
```
**Other instances**
File: FraxlendWhitelist.sol [Line 2](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L2)

File: FraxlendPairConstants.sol [Line 2](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairConstants.sol#L2)

File: FraxlendPairCore.sol [Line 2](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L2)

File: FraxlendPair.sol [Line 2](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L2)

File: FraxlendPairDeployer.sol [Line 2](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L2)


### Lack of event emission after critical initialize() functions

File: FraxlendPairCore.sol [Line 245-285](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L245-L285)
*Function has been truncated*
```solidity
    function initialize(
        string calldata _name,
        address[] calldata _approvedBorrowers,
        address[] calldata _approvedLenders,
        bytes calldata _rateInitCallData
    ) external {
```

### Upper case variable names should be reserved for constants

File: FraxlendPairDeployer.sol [Line 57](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L57)
```solidity
    address public CIRCUIT_BREAKER_ADDRESS;
```

File: FraxlendPairDeployer.sol [Line 58](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L58)
```solidity
    address public COMPTROLLER_ADDRESS;
```

File: FraxlendPairDeployer.sol [Line 59](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L59)
```solidity
    address public TIME_LOCK_ADDRESS;
```

