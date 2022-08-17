### 1. dirtyLiquidationFee uses hard coded multiplier

`9000` is hardcoded for `dirtyLiquidationFee`. If LIQ_PRECISION be changed the calculation becomes incorrect by magnitudes:

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L193-L194

```solidity
            cleanLiquidationFee = _liquidationFee;
            dirtyLiquidationFee = (_liquidationFee * 9000) / LIQ_PRECISION; // 90% of clean fee
```

## Recommended Mitigation Steps

Consider introducing `DIRTY_LIQ_FEE` to project constants and using it:

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L193-L194

```solidity
            cleanLiquidationFee = _liquidationFee;
            dirtyLiquidationFee = (_liquidationFee * DIRTY_LIQ_FEE) / LIQ_PRECISION; // 90% of clean fee
```

### 2. Contructor allows for zero addresses in core configuration

FraxlendPairDeployer and FraxlendPairCore constructors do not check core configuration variables:

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L98-L108

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

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L172-L175

```solidity
            CIRCUIT_BREAKER_ADDRESS = _circuitBreaker;
            COMPTROLLER_ADDRESS = _comptrollerAddress;
            TIME_LOCK_ADDRESS = _timeLockAddress;
            FRAXLEND_WHITELIST_ADDRESS = _fraxlendWhitelistAddress;
```

When these variables set incorrectly the lending pair functionality become mostly unusable

## Recommended Mitigation Steps

Consider adding validity checks (non-zero addresses and variables range)

### 3. Constructor allows for both multiply and divide oracles to be zero 

It is not checked that at least one oracle is functional:

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L206-L217

```solidity
                if (_oracleMultiply != address(0) && !_fraxlendWhitelist.oracleContractWhitelist(_oracleMultiply)) {
                    revert NotOnWhitelist(_oracleMultiply);
                }

                if (_oracleDivide != address(0) && !_fraxlendWhitelist.oracleContractWhitelist(_oracleDivide)) {
                    revert NotOnWhitelist(_oracleDivide);
                }

                // Write oracleData to storage
                oracleMultiply = _oracleMultiply;
                oracleDivide = _oracleDivide;
                oracleNormalization = _oracleNormalization;
```

Setting both oracles to zero will imply that everything is always solvent, `_isSolvent == true`, as LTV = 0:

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L307-L316

```solidity
    function _isSolvent(address _borrower, uint256 _exchangeRate) internal view returns (bool) {
        if (maxLTV == 0) return true;
        uint256 _borrowerAmount = totalBorrow.toAmount(userBorrowShares[_borrower], true);
        if (_borrowerAmount == 0) return true;
        uint256 _collateralAmount = userCollateralBalance[_borrower];
        if (_collateralAmount == 0) return false;

        uint256 _ltv = (((_borrowerAmount * _exchangeRate) / EXCHANGE_PRECISION) * LTV_PRECISION) / _collateralAmount;
        return _ltv <= maxLTV;
    }
```

## Recommended Mitigation Steps

Consider requiring in constructor that at least one oracle is a valid address.

### 4. _updateExchangeRate can use stale prices

Utilizing stale prices in lending protocols yields liquidations of healthy positions and vice versa. Price feed validation is important part of any collateralization management logic.

_updateExchangeRate skips the timestamp/round data returned by `AggregatorV3Interface.latestRoundData` and only uses the price:

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L522-L539

```solidity
        uint256 _price = uint256(1e36);
        if (oracleMultiply != address(0)) {
            (, int256 _answer, , , ) = AggregatorV3Interface(oracleMultiply).latestRoundData();
            if (_answer <= 0) {
                revert OracleLTEZero(oracleMultiply);
            }
            _price = _price * uint256(_answer);
        }

        if (oracleDivide != address(0)) {
            (, int256 _answer, , , ) = AggregatorV3Interface(oracleDivide).latestRoundData();
            if (_answer <= 0) {
                revert OracleLTEZero(oracleDivide);
            }
            _price = _price / uint256(_answer);
        }

        _exchangeRate = _price / oracleNormalization;
```

## Recommended Mitigation Steps

Consider adding the checks for positive timestamp and introducing the stale price threshold:

https://docs.chain.link/docs/feed-registry/#latestrounddata

```
(, int256 price, , int256 updatedAt,) = AggregatorV3Interface(oracle).latestRoundData();

require(updatedAt > 0 && updatedAt > PRICE_UPDATE_THRESHOLD, "Stale price");

```

### 5. Business logic relied on low level overflow check

_redeem() do not checks the allowance, attempting to remove it instead:

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L630-L634

```solidity
        if (msg.sender != _owner) {
            uint256 allowed = allowance(_owner, msg.sender);
            // NOTE: This will revert on underflow ensuring that allowance > shares
            if (allowed != type(uint256).max) _approve(_owner, msg.sender, allowed - _shares);
        }
```

## Recommended Mitigation Steps

Consider introducing proper error and perform a require to check that balance is enough


### 6. Doubled liquidation collateral removal comment

Consider removal of the line:

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1020

```solidity
        // NOTE: reverts if _collateralForLiquidator > userCollateralBalance
```