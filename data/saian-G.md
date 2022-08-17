
## Array length can be cached to save gas in for-loops

Array length can be cached and used in the loop condition instead of reading it in every iteration and save gas

### Proof of concept

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51

```
        for (uint256 i = 0; i < _addresses.length; i++) 
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66

```
        for (uint256 i = 0; i < _addresses.length; i++) { 
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81

```
        for (uint256 i = 0; i < _addresses.length; i++) { 
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289

```
        for (uint256 i = 0; i < _lenders.length; i++)
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308

```
        for (uint256 i = 0; i < _borrowers.length; i++)
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265

```
        for (uint256 i = 0; i < _approvedLenders.length; ++i)
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270

```
        for (uint256 i = 0; i < _approvedBorrowers.length; ++i)
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51

```
        for (uint256 i = 0; i < _addresses.length; i++)
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66

```
        for (uint256 i = 0; i < _addresses.length; i++)
```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81

```
        for (uint256 i = 0; i < _addresses.length; i++)
```

## Use bytes32 instead of string

String is a dynamic data structure and therefore consumes more gas, it can be replaced with bytes32

### Proof of concept

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L51

```
    string public version = "1.0.0"; 
```

## Variables can be constant

Variables that are known at compile time and not updated later can be converted to constant to avoid storage reads and save gas

### Proof of concept

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L49

```
    uint256 public DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision
    uint256 public GLOBAL_MAX_LTV = 1e8; // 1000x (100,000%) with 1e5 precision, protects from rounding errors in LTV calc
    uint256 public DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision
```

## Variables can be immutable

Variables that are initialized in constructor and not updated later can be converted to immutable to save gas on storage read

### Proof of concept

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L56

```
    // Admin contracts
    address public CIRCUIT_BREAKER_ADDRESS;
    address public COMPTROLLER_ADDRESS;
    address public TIME_LOCK_ADDRESS;
```

## Pre-increment can be converted to post-increment

Pre-increment saves a small amount of gas than postfix increment because it doesnt have to store the previous value. This can be more significant in loops where this operation is done multiple times.

### Proof of concept

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51

```
        for (uint256 i = 0; i < _addresses.length; i++) 
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66

```
        for (uint256 i = 0; i < _addresses.length; i++) { 
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81

```
        for (uint256 i = 0; i < _addresses.length; i++) { 
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L130

```
        i++;
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L158

```
        i++;
```

## Long revert strings

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met. Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### Proof of concept

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L205

```
        require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L228

```
        require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");

```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L253

```
        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");    
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L365

```
        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");

```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L366

```
        require(
            IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
            "FraxlendPairDeployer: Only whitelisted addresses"
        );
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57

```
        require(
            _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
            "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
        );  
        require(
            _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
            "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
        ); 
        require(
            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
        );
```

## Use custom errors instead of revert strings

Custom errors from solidity 0.8.4 are more efficient than revert strings with cheaper deployment and runtime time costs when revert condition is met

Refer: https://blog.soliditylang.org/2021/04/21/custom-errors/](https://blog.soliditylang.org/2021/04/21/custom-errors/

## Perform early check to save gas on revert

Deployed pair address name check can be performed in `deploy()` function instead of `_deploySecond()` to save gas on revert

### Proof of concept

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L253

```
        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");    
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L315

```
        string memory _name = string(
            abi.encodePacked(
                "FraxlendV1 - ",
                IERC20(_collateral).safeName(),
                "/",
                IERC20(_asset).safeName(),
                " - ",
                IRateCalculator(_rateContract).name(),
                " - ",
                (deployedPairsArray.length + 1).toString()
            )
        );
```