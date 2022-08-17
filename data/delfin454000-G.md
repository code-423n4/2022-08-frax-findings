### `Require` revert string is too long
The revert strings below should be shortened to 32 characters or fewer to save gas, or else consider using custom error codes (which could save even more gas)
___
[LinearInterestRate.sol: L57-60](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L57-L60)
```solidity
        require(
            _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
            "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
```
___
[LinearInterestRate.sol: L61-64](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L61-L64)
```solidity
        require(
            _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
            "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
        );
```
___
[LinearInterestRate.sol: L65-68](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L65-L68)
```solidity
        require(
            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
        );
```
___
[FraxlendPairDeployer.sol: L205](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L205)
```solidity
            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
```
___
[FraxlendPairDeployer.sol: L228](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L228)
```solidity
            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
```
___
[FraxlendPairDeployer.sol: L365](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L365)
```solidity
        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
```
___
[FraxlendPairDeployer.sol: L366-369](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L366-L369)
```solidity
        require(
            IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
            "FraxlendPairDeployer: Only whitelisted addresses"
        );
```
___
___

### Avoid use of '&&' within a `require` function
Splitting into separate `require()` statements saves gas
___
[LinearInterestRate.sol: L57-60](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L57-L60)
```solidity
        require(
            _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
            "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
```
Recommendation:
```solidity
        require(_minInterest < MAX_INT, "Error message");
        require(_minInterest <= _vertexInterest, "Error message");
        require(_minInterest >= MIN_INT, "Error message");
```
Similarly for the additional instances of `&&` within `require` functions referenced below:

[LinearInterestRate.sol: L61-64](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L61-L64)

[LinearInterestRate.sol: L65-68](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L65-L68)
___
___

### Array length should not be looked up in every iteration of a `for` loop
Since calculating the array length costs gas, it's best to read the length of the array from memory before executing the loop (as in fact occurs in some of the `for` loops in `Fraxlend`)  
___
[FraxlendWhitelist.sol: L51-54](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L51-L54)
```solidity
        for (uint256 i = 0; i < _addresses.length; i++) {
            oracleContractWhitelist[_addresses[i]] = _bool;
            emit SetOracleWhitelist(_addresses[i], _bool);
        }
```
Suggestion:
```solidity
        uint256 totalAddressesLength = _addresses.length; 
        for (uint256 i = 0; i < totalAddressesLength; i++) {
            oracleContractWhitelist[_addresses[i]] = _bool;
            emit SetOracleWhitelist(_addresses[i], _bool);
        }
```
___
Similarly for the six additional `for` loops referenced below:

[FraxlendWhitelist.sol: L66-69](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L66-L69)

[FraxlendWhitelist.sol: L81-84](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L81-L84)

[FraxlendPairCore.sol: L265-267](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L265-L267)

[FraxlendPairCore.sol: L270-272](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L270-L272)

[FraxlendPair.sol: L289-295](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L289-L295)

[FraxlendPair.sol: L308-314](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L308-L314)
___
___

### Use `++i` instead of `i++` to increase count in a `for` loop
Since use of  `i++` (or equivalent counter) costs more gas, it is better to use `++i`
___
[SafeERC20.sol: L27-29](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/libraries/SafeERC20.sol#L27-L29)
```solidity
            for (i = 0; i < 32 && data[i] != 0; i++) {
                bytesArray[i] = data[i];
            }
```
Suggestion:
```solidity
            for (i = 0; i < 32 && data[i] != 0; ++i) {
                bytesArray[i] = data[i];
            }
```
___
Similarly for the seven `for` loops referenced below:

[FraxlendWhitelist.sol: L51-54](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L51-L54)

[FraxlendWhitelist.sol: L66-69](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L66-L69)

[FraxlendWhitelist.sol: L81-84](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L81-L84)

[FraxlendPair.sol: L289-295](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L289-L295)

[FraxlendPair.sol: L308-314](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L308-L314)

[FraxlendPairDeployer.sol: L127-132](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L127-L132)

[FraxlendPairDeployer.sol: L152-160](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L152-L160)
___
___

### Avoid use of default "checked" behavior in a `for` loop
Underflow/overflow checks are made every time `++i` (or `i++` or equivalent counter) is called. Such checks are unnecessary since `i` is already limited. Therefore, use `unchecked {++i}`/`unchecked {i++}` instead
___
[FraxlendWhitelist.sol: L51-54](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L51-L54)
```solidity
        for (uint256 i = 0; i < _addresses.length; i++) {
            oracleContractWhitelist[_addresses[i]] = _bool;
            emit SetOracleWhitelist(_addresses[i], _bool);
        }
```
Recommendation
```solidity
        uint256 totalAddressesLength = _addresses.length; 
        for (uint256 i = 0; i < totalAddressesLength;) {
            oracleContractWhitelist[_addresses[i]] = _bool;
            emit SetOracleWhitelist(_addresses[i], _bool);

            unchecked {
              ++i;
          }
        }
```

Similarly for the seven following `for` loops:

[FraxlendWhitelist.sol: L51-54](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L51-L54)

[FraxlendWhitelist.sol: L66-69](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L66-L69)

[FraxlendWhitelist.sol: L81-84](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L81-L84)

[FraxlendPairCore.sol: L265-267](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L265-L267)

[FraxlendPairCore.sol: L270-272](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L270-L272)

[FraxlendPair.sol: L289-295](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L289-L295)

[FraxlendPair.sol: L308-314](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L308-L314)
___
Note that while, for presentation purposes, I have been addressing the `for` loop-related gas savings opportunities separately, they should be combined. I've included the other two types of gas saving that were described in previous sections in the recommendation above. Also, the `for` loops in the contest are inconsistent, so that the lists of loops for each gas saving type presented above are not identical.
___
___
