### Version String can be constant
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L51

```solidity
    string public version = "1.0.0";
```

### TIME_LOCK_ADDRESS can be immutable
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L86-L87

```solidity
    address public TIME_LOCK_ADDRESS;

```

### nameOfContract can be immutable
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L92-L93

```solidity
    string internal nameOfContract;

```

### Use ++i instead of i++
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L129-L131

```solidity
            unchecked {
                i++;
            }
```

### Event with indexed field is cheaper
e.g.
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L200-L201

```solidity
    event SetTimeLock(address _oldAddress, address _newAddress);

```



