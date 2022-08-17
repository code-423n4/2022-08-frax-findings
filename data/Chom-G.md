## Double abi.decode on LinearInterestRate

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L53-L56
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L79-L82

```
    /// @notice The ```requireValidInitData``` function reverts if initialization data fails to be validated
    /// @param _initData abi.encode(uint256 _minInterest, uint256 _vertexInterest, uint256 _maxInterest, uint256 _vertexUtilization)
    function requireValidInitData(bytes calldata _initData) public pure {
        (uint256 _minInterest, uint256 _vertexInterest, uint256 _maxInterest, uint256 _vertexUtilization) = abi.decode(
            _initData,
            (uint256, uint256, uint256, uint256)
        );
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
    }

    /// @notice Calculates interest rates using two linear functions f(utilization)
    /// @dev We use calldata to remain unopinionated about future implementations
    /// @param _data abi.encode(uint64 _currentRatePerSec, uint256 _deltaTime, uint256 _utilization, uint256 _deltaBlocks)
    /// @param _initData abi.encode(uint256 _minInterest, uint256 _vertexInterest, uint256 _maxInterest, uint256 _vertexUtilization)
    /// @return _newRatePerSec The new interest rate per second, 1e18 precision
    function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {
        requireValidInitData(_initData);
        (, , uint256 _utilization, ) = abi.decode(_data, (uint64, uint256, uint256, uint256));
        (uint256 _minInterest, uint256 _vertexInterest, uint256 _maxInterest, uint256 _vertexUtilization) = abi.decode(
            _initData,
            (uint256, uint256, uint256, uint256)
        );
        if (_utilization < _vertexUtilization) {
            uint256 _slope = ((_vertexInterest - _minInterest) * UTIL_PREC) / _vertexUtilization;
            _newRatePerSec = uint64(_minInterest + ((_utilization * _slope) / UTIL_PREC));
        } else if (_utilization > _vertexUtilization) {
            uint256 _slope = (((_maxInterest - _vertexInterest) * UTIL_PREC) / (UTIL_PREC - _vertexUtilization));
            _newRatePerSec = uint64(_vertexInterest + (((_utilization - _vertexUtilization) * _slope) / UTIL_PREC));
        } else {
            _newRatePerSec = uint64(_vertexInterest);
        }
    }
```

Notice that this code block run 2 times

```
        (uint256 _minInterest, uint256 _vertexInterest, uint256 _maxInterest, uint256 _vertexUtilization) = abi.decode(
            _initData,
            (uint256, uint256, uint256, uint256)
        );
```

You can split requireValidInitData function to avoid redundant abi.decode

```
    function requireValidInitData(uint256 _minInterest, uint256 _vertexInterest, uint256 _maxInterest, uint256 _vertexUtilization) public pure {
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
    }

    /// @notice The ```requireValidInitData``` function reverts if initialization data fails to be validated
    /// @param _initData abi.encode(uint256 _minInterest, uint256 _vertexInterest, uint256 _maxInterest, uint256 _vertexUtilization)
    function requireValidInitData(bytes calldata _initData) public pure {
        (uint256 _minInterest, uint256 _vertexInterest, uint256 _maxInterest, uint256 _vertexUtilization) = abi.decode(
            _initData,
            (uint256, uint256, uint256, uint256)
        );
        requireValidInitData(_minInterest, _vertexInterest, _maxInterest, _vertexUtilization);
    }

    /// @notice Calculates interest rates using two linear functions f(utilization)
    /// @dev We use calldata to remain unopinionated about future implementations
    /// @param _data abi.encode(uint64 _currentRatePerSec, uint256 _deltaTime, uint256 _utilization, uint256 _deltaBlocks)
    /// @param _initData abi.encode(uint256 _minInterest, uint256 _vertexInterest, uint256 _maxInterest, uint256 _vertexUtilization)
    /// @return _newRatePerSec The new interest rate per second, 1e18 precision
    function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {
        (uint256 _minInterest, uint256 _vertexInterest, uint256 _maxInterest, uint256 _vertexUtilization) = abi.decode(
            _initData,
            (uint256, uint256, uint256, uint256)
        );
        requireValidInitData(_minInterest, _vertexInterest, _maxInterest, _vertexUtilization);

        (, , uint256 _utilization, ) = abi.decode(_data, (uint64, uint256, uint256, uint256));

        if (_utilization < _vertexUtilization) {
            uint256 _slope = ((_vertexInterest - _minInterest) * UTIL_PREC) / _vertexUtilization;
            _newRatePerSec = uint64(_minInterest + ((_utilization * _slope) / UTIL_PREC));
        } else if (_utilization > _vertexUtilization) {
            uint256 _slope = (((_maxInterest - _vertexInterest) * UTIL_PREC) / (UTIL_PREC - _vertexUtilization));
            _newRatePerSec = uint64(_vertexInterest + (((_utilization - _vertexUtilization) * _slope) / UTIL_PREC));
        } else {
            _newRatePerSec = uint64(_vertexInterest);
        }
    }
```

## Can use assembly to decode _utilization from _data

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L78

```
(, , uint256 _utilization, ) = abi.decode(_data, (uint64, uint256, uint256, uint256));
```

Can be optimized by using assembly technique to skip unnecessary decoding of first two arguments.