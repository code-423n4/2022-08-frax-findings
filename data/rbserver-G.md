## [G-01] block.number - \_currentRateInfo.lastBlock OF \_rateData IS NOT USED IN getNewRate FUNCTION AND CAN BE REMOVED
`block.number - \_currentRateInfo.lastBlock` of `_rateData` in the following code is never used in the `getNewRate` functions of the `LinearInterestRate` and `VariableInterestRate` contract. Hence, it can be removed from `_rateData`, and the `getNewRate` functions can be adjusted accordingly to save deployment and run-time gas.

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L450-L456
```
	bytes memory _rateData = abi.encode(
		_currentRateInfo.ratePerSec,
		_deltaTime,
		_utilizationRate,
		block.number - _currentRateInfo.lastBlock
	);
	_newRate = IRateCalculator(rateContract).getNewRate(_rateData, rateInitCallData);
```

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L76-L92
```
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

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L63-L85
```
    function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {
        (uint64 _currentRatePerSec, uint256 _deltaTime, uint256 _utilization, ) = abi.decode(
            _data,
            (uint64, uint256, uint256, uint256)
        );
        if (_utilization < MIN_UTIL) {
            uint256 _deltaUtilization = ((MIN_UTIL - _utilization) * 1e18) / MIN_UTIL;
            uint256 _decayGrowth = INT_HALF_LIFE + (_deltaUtilization * _deltaUtilization * _deltaTime);
            _newRatePerSec = uint64((_currentRatePerSec * INT_HALF_LIFE) / _decayGrowth);
            if (_newRatePerSec < MIN_INT) {
                _newRatePerSec = MIN_INT;
            }
        } else if (_utilization > MAX_UTIL) {
            uint256 _deltaUtilization = ((_utilization - MAX_UTIL) * 1e18) / (UTIL_PREC - MAX_UTIL);
            uint256 _decayGrowth = INT_HALF_LIFE + (_deltaUtilization * _deltaUtilization * _deltaTime);
            _newRatePerSec = uint64((_currentRatePerSec * _decayGrowth) / INT_HALF_LIFE);
            if (_newRatePerSec > MAX_INT) {
                _newRatePerSec = MAX_INT;
            }
        } else {
            _newRatePerSec = _currentRatePerSec;
        }
    }
```

## [G-02] REDUNDANT TYPE CONVERSION CAN BE AVOIDED
Converting a variable type when not needed costs more unnecessary gas.
`1e36` does not need to be explicitly casted for the following code.

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L522
```
uint256 _price = uint256(1e36);
```

## [G-03] STATE VARIABLES WITH VALUES THAT DO NOT CHANGE CAN BE DECLARED AS CONSTANTS
Reading constants save more gas than reading state variables. Please consider declaring the following state variables as constants since their values do not change.
```
contracts\FraxlendPairDeployer.sol
  49: uint256 public DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision   
  50: uint256 public GLOBAL_MAX_LTV = 1e8; // 1000x (100,000%) with 1e5 precision, protects from rounding errors in LTV calc
  51: uint256 public DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision
```

## [G-04] STATE VARIABLE WHOSE VALUE IS ONLY ASSIGNED IN CONSTRUCTOR CAN BE IMMUTABLE
Providing an `immutable` keyword to the state variable whose value is only assigned in the constructor can help save gas. Please consider declaring the following state variables as immutables.
```
contracts\FraxlendPairDeployer.sol
  57: address public CIRCUIT_BREAKER_ADDRESS;
  58: address public COMPTROLLER_ADDRESS;
  59: address public TIME_LOCK_ADDRESS;
```

## [G-05] VARIABLE DOES NOT NEED TO BE INITIALIZED TO ITS DEFAULT VALUE
Explicitly initializing a variable with its default value costs more gas than uninitializing it. For example, `uint256 i` can be used instead of `uint256 i = 0` in the following code.
```
contracts\FraxlendPair.sol
  289: for (uint256 i = 0; i < _lenders.length; i++) {
  308: for (uint256 i = 0; i < _borrowers.length; i++) {

contracts\FraxlendPairCore.sol
  265: for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
  270: for (uint256 i = 0; i < _approvedLenders.length; ++i) {

contracts\FraxlendPairDeployer.sol
  127: for (i = 0; i < _lengthOfArray; ) {
  152: for (i = 0; i < _lengthOfArray; ) {
  402: for (uint256 i = 0; i < _lengthOfArray; ) {

contracts\FraxlendWhitelist.sol
  51: for (uint256 i = 0; i < _addresses.length; i++) {
  66: for (uint256 i = 0; i < _addresses.length; i++) {
  81: for (uint256 i = 0; i < _addresses.length; i++) {

contracts\libraries\SafeERC20.sol
  27: for (i = 0; i < 32 && data[i] != 0; i++) {
```

## [G-06] ARRAY LENGTH CAN BE CACHED OUTSIDE OF LOOP
Caching the array length outside of the loop and using the cached length in the loop costs less gas than reading the array length for each iteration. For example, `_lenders.length` in the following code can be cached outside of the loop like `uint256 _lendersLength = _lenders.length`, and `i < _lendersLength` can be used for each iteration.
```
contracts\FraxlendPair.sol
  289: for (uint256 i = 0; i < _lenders.length; i++) {
  308: for (uint256 i = 0; i < _borrowers.length; i++) {

contracts\FraxlendPairCore.sol
  265: for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
  270: for (uint256 i = 0; i < _approvedLenders.length; ++i) {

contracts\FraxlendWhitelist.sol
  51: for (uint256 i = 0; i < _addresses.length; i++) {
  66: for (uint256 i = 0; i < _addresses.length; i++) {
  81: for (uint256 i = 0; i < _addresses.length; i++) {
```

## [G-07] ++VARIABLE CAN BE USED INSTEAD OF VARIABLE++
++variable costs less gas than variable++. For example, `i++` can be changed to `++i` in the following code.
```
contracts\FraxlendPair.sol
  289: for (uint256 i = 0; i < _lenders.length; i++) {
  308: for (uint256 i = 0; i < _borrowers.length; i++) {

contracts\FraxlendWhitelist.sol
  51: for (uint256 i = 0; i < _addresses.length; i++) {
  66: for (uint256 i = 0; i < _addresses.length; i++) {
  81: for (uint256 i = 0; i < _addresses.length; i++) {
```

## [G-08] ARITHMETIC OPERATIONS THAT DO NOT OVERFLOW CAN BE UNCHECKED
Explicitly unchecking arithmetic operations that do not overflow by wrapping these in `unchecked {}` costs less gas than implicitly checking these.

For the following loops, if increasing the counter variable is very unlikely to overflow, then `unchecked {++i}` at the end of the loop block can be used, where `i` is the counter variable.
```
contracts\FraxlendPair.sol
  289: for (uint256 i = 0; i < _lenders.length; i++) {
  308: for (uint256 i = 0; i < _borrowers.length; i++) {

contracts\FraxlendPairCore.sol
  265: for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
  270: for (uint256 i = 0; i < _approvedLenders.length; ++i) {

contracts\FraxlendWhitelist.sol
  51: for (uint256 i = 0; i < _addresses.length; i++) {
  66: for (uint256 i = 0; i < _addresses.length; i++) {
  81: for (uint256 i = 0; i < _addresses.length; i++) {

contracts\libraries\SafeERC20.sol
  27: for (i = 0; i < 32 && data[i] != 0; i++) {
```

## [G-09] REVERT REASON STRINGS CAN BE SHORTENED TO FIT IN 32 BYTES
Revert reason strings that are longer than 32 bytes need more memory space and more mstore operation(s) than these that are shorter than 32 bytes when reverting. Please consider shortening the following revert reason strings.
```
contracts\FraxlendPairDeployer.sol
  205: require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");  
  228: require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");  
  253: require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");  

contracts\LinearInterestRate.sol
  59: "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
  63: "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
  67: "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
```

## [G-10] REVERT WITH CUSTOM ERROR CAN BE USED INSTEAD OF REQUIRE() WITH REASON STRING
`revert` with custom error can cost less gas than `require()` with reason string. Please consider using `revert` with custom error to replace the following `require()`.
```
contracts\FraxlendPairDeployer.sol
  205: require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
  228: require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
  253: require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
  365: require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
  366: require(
  399: require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");

contracts\LinearInterestRate.sol
  57: require(
  61: require(
  65: require(
```