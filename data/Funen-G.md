#GAS OPT

1.  Using unchecked ++i can save more gas

Files :

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L157-L159

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L129-L131

2. Set value as constant 


https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L49-L51

```
    uint256 public DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision
    uint256 public GLOBAL_MAX_LTV = 1e8; // 1000x (100,000%) with 1e5 precision, protects from rounding errors in LTV calc
    uint256 public DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision
```

3. Short reason string can be used for saving more gas

Every reason string takes at least 32 bytes. Use short reason strings that fits in 32 bytes or it will become more expensive.

File :

```


/main/src/contracts/FraxlendPairDeployer.sol#L253 "FraxlendPairDeployer: Pair name must be unique"
/main/src/contracts/FraxlendPairDeployer.sol#L365  "FraxlendPairDeployer: _maxLTV is too large"
/main/src/contracts/FraxlendPairDeployer.sol#L368 "FraxlendPairDeployer: Only whitelisted addresses
```

4. Custom Error

Custom errors can be used from Solidity 0.8.4 are cheaper than revert strings. Its cheaper deployment cost and runtime cost when the revert condition is met.

Files :

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L253

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L365-L369

