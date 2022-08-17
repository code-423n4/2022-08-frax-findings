# Fraxlend Contest Gas Optimization Report

## Summary

The following gas optimization issues were found during the code audit:

1. Use `calldata` instead of `memory` (27 instances)
2. Cache `<array>.length` (10 instances)
3. Use `unchecked{}` to suppress overflow/underflow check (14 instances)
4. Long `require()`/`revert()` string (11 instances)
5. Using `bool`s for storage incurs overhead (21 instances)
6. Use `!= 0` instead of `> 0` when comparing uint (10 instances)
7. Empty blocks should be removed (4 instances)
8. Don't initialize variables with default value (14 instances)
9. Use `++i`/`--i` instead of `i++`/`i--` (12 instances)
10. Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (3 instances)
11. Use uint256/int256 instead of other variations (180 instances)
12. Use `abi.encodePacked()` instead of `abi.encode()` (35 instances)
13. Don't compare boolean expressions to boolean literals (3 instances)
14. Do not use SafeMath for Solidity >= 0.8.0 (1 instances)
15. Use custom errors instead of `revert()`/`require()` strings (18 instances)
16. Use shift right/left instead of division/multiplication if possible (6 instances)
17. Use `storage` instead of `memory` for structs/arrays saves gas (49 instances)

Total 418 instances of 17 issues.

## Use `calldata` instead of `memory` (27 instances)

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for loop to copy each index of the `calldata` to the `memory` index. This overhead can be optimized by using `calldata` directly.

```solidity
2022-08-frax/src/contracts/FraxlendPair.sol::74 => function name() public view override(ERC20, IERC20Metadata) returns (string memory) {

2022-08-frax/src/contracts/FraxlendPair.sol::78 => function symbol() public view override(ERC20, IERC20Metadata) returns (string memory) {

2022-08-frax/src/contracts/FraxlendPairCore.sol::295 => function _totalAssetAvailable(VaultAccount memory _totalAsset, VaultAccount memory _totalBorrow)

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::122 => function getAllPairAddresses() external view returns (address[] memory) {

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::310 => function deploy(bytes memory _configData) external returns (address _pairAddress) {

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::398 => function globalPause(address[] memory _addresses) external returns (address[] memory _updatedAddresses) {

2022-08-frax/src/contracts/FraxlendPairHelper.sol::81 => function getImmutableUint256(address _fraxlendPairAddress) external view returns (ImmutablesUint256 memory) {

2022-08-frax/src/contracts/LinearInterestRate.sol::40 => function name() external pure returns (string memory) {

2022-08-frax/src/contracts/LinearInterestRate.sol::46 => function getConstants() external pure returns (bytes memory _calldata) {

2022-08-frax/src/contracts/VariableInterestRate.sol::46 => function name() external pure returns (string memory) {

2022-08-frax/src/contracts/VariableInterestRate.sol::52 => function getConstants() external pure returns (bytes memory _calldata) {

2022-08-frax/src/contracts/interfaces/IRateCalculator.sol::5 => function name() external pure returns (string memory);

2022-08-frax/src/contracts/interfaces/IRateCalculator.sol::9 => function getConstants() external pure returns (bytes memory _calldata);

2022-08-frax/src/contracts/libraries/SafeERC20.sol::18 => function returnDataToString(bytes memory data) internal pure returns (string memory) {

2022-08-frax/src/contracts/libraries/SafeERC20.sol::39 => function safeSymbol(IERC20 token) internal view returns (string memory) {

2022-08-frax/src/contracts/libraries/SafeERC20.sol::47 => function safeName(IERC20 token) internal view returns (string memory) {

2022-08-frax/src/test/e2e/BasePairTest.sol::125 => function setNetAccountingSnapshot(PairAccounting memory _first, PairAccounting memory _second) internal {

2022-08-frax/src/test/e2e/BasePairTest.sol::134 => function defaultRateInitForLinear() public view returns (bytes memory) {

2022-08-frax/src/test/e2e/BasePairTest.sol::460 => function lendTokenViaDepositWithFaucet(FraxlendPair _pair, LendAction memory _lendAction)

2022-08-frax/src/test/e2e/BasePairTest.sol::499 => function borrowTokenWithFaucet(FraxlendPair _fraxlendPair, BorrowAction memory _borrowAction)

2022-08-frax/src/test/e2e/LinearRateTest.sol::39 => function _testLinearInitData(bytes memory _initData) public {

2022-08-frax/src/test/e2e/LinearRateTest.sol::76 => function _getNewRate(uint256 _utilization, bytes memory _initData) public view returns (uint256 _newRate) {

2022-08-frax/src/test/e2e/Scenarios.sol::33 => function getScenarios() public returns (Scenario[] memory _scenarios) {

2022-08-frax/src/test/interfaces/IUniswapV2Pair.sol::7 => function name() external pure returns (string memory);

2022-08-frax/src/test/interfaces/IUniswapV2Pair.sol::9 => function symbol() external pure returns (string memory);

2022-08-frax/src/test/interfaces/IUniswapV2Router01.sol::151 => function getAmountsOut(uint256 amountIn, address[] calldata path) external view returns (uint256[] memory amounts);

2022-08-frax/src/test/interfaces/IUniswapV2Router01.sol::153 => function getAmountsIn(uint256 amountOut, address[] calldata path) external view returns (uint256[] memory amounts);
```

## Cache `<array>.length` (10 instances)

If `<array>.length` is used as for loop termination condition, then the `.length` method will be called in each iteration. Caching it in a local variable can save gas.

```solidity
2022-08-frax/src/contracts/FraxlendPair.sol::289 => for (uint256 i = 0; i < _lenders.length; i++) {

2022-08-frax/src/contracts/FraxlendPair.sol::308 => for (uint256 i = 0; i < _borrowers.length; i++) {

2022-08-frax/src/contracts/FraxlendPairCore.sol::265 => for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {

2022-08-frax/src/contracts/FraxlendPairCore.sol::270 => for (uint256 i = 0; i < _approvedLenders.length; ++i) {

2022-08-frax/src/contracts/FraxlendWhitelist.sol::51 => for (uint256 i = 0; i < _addresses.length; i++) {

2022-08-frax/src/contracts/FraxlendWhitelist.sol::66 => for (uint256 i = 0; i < _addresses.length; i++) {

2022-08-frax/src/contracts/FraxlendWhitelist.sol::81 => for (uint256 i = 0; i < _addresses.length; i++) {

2022-08-frax/src/test/e2e/BasePairTest.sol::220 => for (uint256 i = 0; i < _oracleAddresses.length; i++) {

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::98 => for (uint256 i; i < path.length - 1; i++) {

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::113 => for (uint256 i = path.length - 1; i > 0; i--) {
```

## Use `unchecked{}` to suppress overflow/underflow check (14 instances)

Starting from version 0.8.0, Solidity does overflow/underflow checks by default. It is a good feature to prevent vulnerabilities but it has a significant overhead, especially when used in for loop. When using uint256/int256, it is extremely hard to trigger overflow, so it makes sense to skip these checks. To suppress the overflow/underflow checks, use `unchecked {}`. For increment situations, just use `unchecked {}` directly; for decrement situations, add a `require()` statement before decrementing to prevent underflow.

```solidity
2022-08-frax/src/contracts/FraxlendPair.sol::289 => for (uint256 i = 0; i < _lenders.length; i++) {

2022-08-frax/src/contracts/FraxlendPair.sol::308 => for (uint256 i = 0; i < _borrowers.length; i++) {

2022-08-frax/src/contracts/FraxlendPairCore.sol::265 => for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {

2022-08-frax/src/contracts/FraxlendPairCore.sol::270 => for (uint256 i = 0; i < _approvedLenders.length; ++i) {

2022-08-frax/src/contracts/FraxlendWhitelist.sol::51 => for (uint256 i = 0; i < _addresses.length; i++) {

2022-08-frax/src/contracts/FraxlendWhitelist.sol::66 => for (uint256 i = 0; i < _addresses.length; i++) {

2022-08-frax/src/contracts/FraxlendWhitelist.sol::81 => for (uint256 i = 0; i < _addresses.length; i++) {

2022-08-frax/src/contracts/libraries/SafeERC20.sol::27 => for (i = 0; i < 32 && data[i] != 0; i++) {

2022-08-frax/src/test/e2e/BasePairTest.sol::220 => for (uint256 i = 0; i < _oracleAddresses.length; i++) {

2022-08-frax/src/test/e2e/BasePairTest.sol::417 => for (uint256 i = 0; i < _length; i++) {

2022-08-frax/src/test/e2e/BasePairTest.sol::536 => for (uint256 i = 0; i < _blocks; i++) {

2022-08-frax/src/test/e2e/LiquidationPairTest.sol::33 => for (uint256 i = 0; i < 1; i++) {

2022-08-frax/src/test/e2e/LiquidationPairTest.sol::59 => for (uint256 i = 0; i < 100; i++) {

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::98 => for (uint256 i; i < path.length - 1; i++) {
```

## Long `require()`/`revert()` string (11 instances)

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

```solidity
2022-08-frax/src/contracts/FraxlendPairDeployer.sol::205 => require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::228 => require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::253 => require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::365 => require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::12 => require(tokenA != tokenB, "UniswapV2Library: IDENTICAL_ADDRESSES");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::57 => require(amountA > 0, "UniswapV2Library: INSUFFICIENT_AMOUNT");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::58 => require(reserveA > 0 && reserveB > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::68 => require(amountIn > 0, "UniswapV2Library: INSUFFICIENT_INPUT_AMOUNT");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::69 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::82 => require(amountOut > 0, "UniswapV2Library: INSUFFICIENT_OUTPUT_AMOUNT");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::83 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```

## Using `bool`s for storage incurs overhead (21 instances)

Use `uint256(1)` and `uint256(2)` for true/false. Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
2022-08-frax/src/contracts/FraxlendPair.sol::52 => bool _isBorrowerWhitelistActive,

2022-08-frax/src/contracts/FraxlendPair.sol::53 => bool _isLenderWhitelistActive

2022-08-frax/src/contracts/FraxlendPairCore.sol::133 => bool public immutable borrowerWhitelistActive;

2022-08-frax/src/contracts/FraxlendPairCore.sol::136 => bool public immutable lenderWhitelistActive;

2022-08-frax/src/contracts/FraxlendPairCore.sol::158 => bool _isBorrowerWhitelistActive,

2022-08-frax/src/contracts/FraxlendPairCore.sol::159 => bool _isLenderWhitelistActive

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::138 => bool _isCustom;

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::199 => bool _isBorrowerWhitelistActive,

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::200 => bool _isLenderWhitelistActive

2022-08-frax/src/contracts/FraxlendPairHelper.sol::33 => bool _borrowerWhitelistActive;

2022-08-frax/src/contracts/FraxlendPairHelper.sol::34 => bool _lenderWhitelistActive;

2022-08-frax/src/contracts/libraries/VaultAccount.sol::19 => bool roundUp

2022-08-frax/src/contracts/libraries/VaultAccount.sol::36 => bool roundUp

2022-08-frax/src/test/e2e/BasePairTest.sol::221 => bool _approval = fraxlendWhitelist.oracleContractWhitelist(_oracleAddresses[i]);

2022-08-frax/src/test/e2e/BasePairTest.sol::333 => bool roundup

2022-08-frax/src/test/e2e/BasePairTest.sol::356 => bool roundup

2022-08-frax/src/test/e2e/BasePairTest.sol::379 => bool roundup

2022-08-frax/src/test/e2e/BasePairTest.sol::402 => bool roundup

2022-08-frax/src/test/interfaces/IUniswapV2Router01.sol::68 => bool approveMax,

2022-08-frax/src/test/interfaces/IUniswapV2Router01.sol::81 => bool approveMax,

2022-08-frax/src/test/interfaces/IUniswapV2Router02.sol::22 => bool approveMax,
```

## Use `!= 0` instead of `> 0` when comparing uint (10 instances)

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```solidity
2022-08-frax/src/contracts/FraxlendPairCore.sol::477 => if (_currentRateInfo.feeToProtocolRate > 0) {

2022-08-frax/src/contracts/FraxlendPairCore.sol::754 => if (_collateralAmount > 0) {

2022-08-frax/src/contracts/FraxlendPairCore.sol::835 => if (userBorrowShares[msg.sender] > 0) {

2022-08-frax/src/contracts/FraxlendPairCore.sol::1002 => if (_leftoverBorrowShares > 0) {

2022-08-frax/src/contracts/FraxlendPairCore.sol::1094 => if (_initialCollateralAmount > 0) {

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::379 => _approvedBorrowers.length > 0,

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::380 => _approvedLenders.length > 0

2022-08-frax/src/contracts/FraxlendPairHelper.sol::235 => if (_feeToProtocolRate > 0) {

2022-08-frax/src/contracts/LinearInterestRate.sol::66 => _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::113 => for (uint256 i = path.length - 1; i > 0; i--) {
```

## Empty blocks should be removed (4 instances)

Empty blocks exist in the code. The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

```solidity
2022-08-frax/src/contracts/FraxlendPair.sol::68 => {}

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::406 => } catch {}

2022-08-frax/src/contracts/FraxlendWhitelist.sol::40 => constructor() Ownable() {}

2022-08-frax/src/contracts/VariableInterestRate.sol::57 => function requireValidInitData(bytes calldata _initData) external pure {}
```

## Don't initialize variables with default value (14 instances)

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```solidity
2022-08-frax/src/contracts/FraxlendPair.sol::289 => for (uint256 i = 0; i < _lenders.length; i++) {

2022-08-frax/src/contracts/FraxlendPair.sol::308 => for (uint256 i = 0; i < _borrowers.length; i++) {

2022-08-frax/src/contracts/FraxlendPairCore.sol::265 => for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {

2022-08-frax/src/contracts/FraxlendPairCore.sol::270 => for (uint256 i = 0; i < _approvedLenders.length; ++i) {

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::402 => for (uint256 i = 0; i < _lengthOfArray; ) {

2022-08-frax/src/contracts/FraxlendWhitelist.sol::51 => for (uint256 i = 0; i < _addresses.length; i++) {

2022-08-frax/src/contracts/FraxlendWhitelist.sol::66 => for (uint256 i = 0; i < _addresses.length; i++) {

2022-08-frax/src/contracts/FraxlendWhitelist.sol::81 => for (uint256 i = 0; i < _addresses.length; i++) {

2022-08-frax/src/contracts/libraries/SafeERC20.sol::22 => uint8 i = 0;

2022-08-frax/src/test/e2e/BasePairTest.sol::220 => for (uint256 i = 0; i < _oracleAddresses.length; i++) {

2022-08-frax/src/test/e2e/BasePairTest.sol::417 => for (uint256 i = 0; i < _length; i++) {

2022-08-frax/src/test/e2e/BasePairTest.sol::536 => for (uint256 i = 0; i < _blocks; i++) {

2022-08-frax/src/test/e2e/LiquidationPairTest.sol::33 => for (uint256 i = 0; i < 1; i++) {

2022-08-frax/src/test/e2e/LiquidationPairTest.sol::59 => for (uint256 i = 0; i < 100; i++) {
```

## Use `++i`/`--i` instead of `i++`/`i--` (12 instances)

Using `++i`/`--i` saves 6 gas per loop.

```solidity
2022-08-frax/src/contracts/FraxlendPair.sol::289 => for (uint256 i = 0; i < _lenders.length; i++) {

2022-08-frax/src/contracts/FraxlendPair.sol::308 => for (uint256 i = 0; i < _borrowers.length; i++) {

2022-08-frax/src/contracts/FraxlendWhitelist.sol::51 => for (uint256 i = 0; i < _addresses.length; i++) {

2022-08-frax/src/contracts/FraxlendWhitelist.sol::66 => for (uint256 i = 0; i < _addresses.length; i++) {

2022-08-frax/src/contracts/FraxlendWhitelist.sol::81 => for (uint256 i = 0; i < _addresses.length; i++) {

2022-08-frax/src/contracts/libraries/SafeERC20.sol::27 => for (i = 0; i < 32 && data[i] != 0; i++) {

2022-08-frax/src/test/e2e/BasePairTest.sol::220 => for (uint256 i = 0; i < _oracleAddresses.length; i++) {

2022-08-frax/src/test/e2e/BasePairTest.sol::417 => for (uint256 i = 0; i < _length; i++) {

2022-08-frax/src/test/e2e/BasePairTest.sol::536 => for (uint256 i = 0; i < _blocks; i++) {

2022-08-frax/src/test/e2e/LiquidationPairTest.sol::33 => for (uint256 i = 0; i < 1; i++) {

2022-08-frax/src/test/e2e/LiquidationPairTest.sol::59 => for (uint256 i = 0; i < 100; i++) {

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::98 => for (uint256 i; i < path.length - 1; i++) {
```

## Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (3 instances)

Instead of using operator && on single require check, using double `require()` checks can save more gas.

```solidity
2022-08-frax/src/test/interfaces/UniswapV2Library.sol::58 => require(reserveA > 0 && reserveB > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::69 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::83 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```

## Use uint256/int256 instead of other variations (180 instances)

Using smaller data types such as uint8/int8 is more expensive than using uint256/int256. The EVM works with 256bit/32byte words. Every operation is based on these base units. If the data is smaller, further operations are needed to downscale from 256 bits to 8 bits, and this is more expensive than using uint256/int256.

```solidity
2022-08-frax/src/contracts/FraxlendPair.sol::84 => function decimals() public pure override(ERC20, IERC20Metadata) returns (uint8) {

2022-08-frax/src/contracts/FraxlendPair.sol::137 => return type(uint128).max;

2022-08-frax/src/contracts/FraxlendPair.sol::141 => return type(uint128).max;

2022-08-frax/src/contracts/FraxlendPair.sol::165 => uint64 _DEFAULT_INT,

2022-08-frax/src/contracts/FraxlendPair.sol::166 => uint16 _DEFAULT_PROTOCOL_FEE,

2022-08-frax/src/contracts/FraxlendPair.sol::211 => event ChangeFee(uint32 _newFee);

2022-08-frax/src/contracts/FraxlendPair.sol::215 => function changeFee(uint32 _newFee) external whenNotPaused {

2022-08-frax/src/contracts/FraxlendPair.sol::228 => event WithdrawFees(uint128 _shares, address _recipient, uint256 _amountToTransfer);

2022-08-frax/src/contracts/FraxlendPair.sol::234 => function withdrawFees(uint128 _shares, address _recipient) external onlyOwner returns (uint256 _amountToTransfer) {

2022-08-frax/src/contracts/FraxlendPair.sol::240 => if (_shares == 0) _shares = uint128(balanceOf(address(this)));

2022-08-frax/src/contracts/FraxlendPair.sol::252 => _totalAsset.amount -= uint128(_amountToTransfer);

2022-08-frax/src/contracts/FraxlendPairConstants.sol::41 => uint64 internal constant DEFAULT_INT = 158247046; // 0.5% annual rate 1e18 precision

2022-08-frax/src/contracts/FraxlendPairConstants.sol::47 => uint16 internal constant DEFAULT_PROTOCOL_FEE = 0; // 1e5 precision

2022-08-frax/src/contracts/FraxlendPairCore.sol::106 => uint64 lastBlock;

2022-08-frax/src/contracts/FraxlendPairCore.sol::107 => uint64 feeToProtocolRate; // Fee amount 1e5 precision

2022-08-frax/src/contracts/FraxlendPairCore.sol::108 => uint64 lastTimestamp;

2022-08-frax/src/contracts/FraxlendPairCore.sol::109 => uint64 ratePerSec;

2022-08-frax/src/contracts/FraxlendPairCore.sol::116 => uint32 lastTimestamp;

2022-08-frax/src/contracts/FraxlendPairCore.sol::400 => uint64 _newRate

2022-08-frax/src/contracts/FraxlendPairCore.sol::415 => uint64 _newRate

2022-08-frax/src/contracts/FraxlendPairCore.sol::434 => _currentRateInfo.lastTimestamp = uint64(block.timestamp);

2022-08-frax/src/contracts/FraxlendPairCore.sol::435 => _currentRateInfo.lastBlock = uint64(block.number);

2022-08-frax/src/contracts/FraxlendPairCore.sol::448 => _newRate = uint64(penaltyRate);

2022-08-frax/src/contracts/FraxlendPairCore.sol::464 => _currentRateInfo.lastTimestamp = uint64(block.timestamp);

2022-08-frax/src/contracts/FraxlendPairCore.sol::465 => _currentRateInfo.lastBlock = uint64(block.number);

2022-08-frax/src/contracts/FraxlendPairCore.sol::472 => _interestEarned + _totalBorrow.amount <= type(uint128).max &&

2022-08-frax/src/contracts/FraxlendPairCore.sol::473 => _interestEarned + _totalAsset.amount <= type(uint128).max

2022-08-frax/src/contracts/FraxlendPairCore.sol::475 => _totalBorrow.amount += uint128(_interestEarned);

2022-08-frax/src/contracts/FraxlendPairCore.sol::476 => _totalAsset.amount += uint128(_interestEarned);

2022-08-frax/src/contracts/FraxlendPairCore.sol::484 => _totalAsset.shares += uint128(_feesShare);

2022-08-frax/src/contracts/FraxlendPairCore.sol::544 => _exchangeRateInfo.lastTimestamp = uint32(block.timestamp);

2022-08-frax/src/contracts/FraxlendPairCore.sol::561 => uint128 _amount,

2022-08-frax/src/contracts/FraxlendPairCore.sol::562 => uint128 _shares,

2022-08-frax/src/contracts/FraxlendPairCore.sol::594 => _deposit(_totalAsset, _amount.toUint128(), _sharesReceived.toUint128(), _receiver);

2022-08-frax/src/contracts/FraxlendPairCore.sol::613 => _deposit(_totalAsset, _amountReceived.toUint128(), _shares.toUint128(), _receiver);

2022-08-frax/src/contracts/FraxlendPairCore.sol::625 => uint128 _amountToReturn,

2022-08-frax/src/contracts/FraxlendPairCore.sol::626 => uint128 _shares,

2022-08-frax/src/contracts/FraxlendPairCore.sol::668 => _redeem(_totalAsset, _amountToReturn.toUint128(), _shares.toUint128(), _receiver, _owner);

2022-08-frax/src/contracts/FraxlendPairCore.sol::684 => _redeem(_totalAsset, _amount.toUint128(), _shares.toUint128(), _receiver, _owner);

2022-08-frax/src/contracts/FraxlendPairCore.sol::707 => function _borrowAsset(uint128 _borrowAmount, address _receiver) internal returns (uint256 _sharesAdded) {

2022-08-frax/src/contracts/FraxlendPairCore.sol::719 => _totalBorrow.shares += uint128(_sharesAdded);

2022-08-frax/src/contracts/FraxlendPairCore.sol::757 => _shares = _borrowAsset(_borrowAmount.toUint128(), _receiver);

2022-08-frax/src/contracts/FraxlendPairCore.sol::857 => uint128 _amountToRepay,

2022-08-frax/src/contracts/FraxlendPairCore.sol::858 => uint128 _shares,

2022-08-frax/src/contracts/FraxlendPairCore.sol::886 => _repayAsset(_totalBorrow, _amountToRepay.toUint128(), _shares.toUint128(), msg.sender, _borrower);

2022-08-frax/src/contracts/FraxlendPairCore.sol::937 => _repayAsset(_totalBorrow, _amountToRepay.toUint128(), _shares.toUint128(), msg.sender, _borrower); // liquidator repays shares on behalf of borrower

2022-08-frax/src/contracts/FraxlendPairCore.sol::951 => uint128 _sharesToLiquidate,

2022-08-frax/src/contracts/FraxlendPairCore.sol::967 => uint128 _borrowerShares = userBorrowShares[_borrower].toUint128();

2022-08-frax/src/contracts/FraxlendPairCore.sol::993 => uint128 _amountLiquidatorToRepay = (_totalBorrow.toAmount(_sharesToLiquidate, true)).toUint128();

2022-08-frax/src/contracts/FraxlendPairCore.sol::997 => uint128 _sharesToAdjust;

2022-08-frax/src/contracts/FraxlendPairCore.sol::998 => uint128 _amountToAdjust;

2022-08-frax/src/contracts/FraxlendPairCore.sol::1001 => uint128 _leftoverBorrowShares = _borrowerShares - _sharesToLiquidate;

2022-08-frax/src/contracts/FraxlendPairCore.sol::1005 => _amountToAdjust = (_totalBorrow.toAmount(_sharesToAdjust, false)).toUint128();

2022-08-frax/src/contracts/FraxlendPairCore.sol::1100 => uint256 _borrowShares = _borrowAsset(_borrowAmount.toUint128(), address(this));

2022-08-frax/src/contracts/FraxlendPairCore.sol::1209 => _repayAsset(_totalBorrow, _amountAssetOut.toUint128(), _sharesToRepay.toUint128(), address(this), msg.sender);

2022-08-frax/src/contracts/FraxlendPairHelper.sol::54 => uint64 lastBlock;

2022-08-frax/src/contracts/FraxlendPairHelper.sol::55 => uint64 feeToProtocolRate; // Fee amount 1e5 precision

2022-08-frax/src/contracts/FraxlendPairHelper.sol::56 => uint64 lastTimestamp;

2022-08-frax/src/contracts/FraxlendPairHelper.sol::57 => uint64 ratePerSec;

2022-08-frax/src/contracts/FraxlendPairHelper.sol::112 => uint128 _totalAssetAmount,

2022-08-frax/src/contracts/FraxlendPairHelper.sol::113 => uint128 _totalAssetShares,

2022-08-frax/src/contracts/FraxlendPairHelper.sol::114 => uint128 _totalBorrowAmount,

2022-08-frax/src/contracts/FraxlendPairHelper.sol::115 => uint128 _totalBorrowShares,

2022-08-frax/src/contracts/FraxlendPairHelper.sol::161 => (, , uint256 _UTIL_PREC, , , uint64 _DEFAULT_INT, , ) = _fraxlendPair.getConstants();

2022-08-frax/src/contracts/FraxlendPairHelper.sol::166 => (uint64 lastBlock, uint64 feeToProtocolRate, uint64 lastTimestamp, uint64 ratePerSec) = _fraxlendPair

2022-08-frax/src/contracts/FraxlendPairHelper.sol::180 => (uint128 _totalAssetAmount, uint128 _totalAssetShares) = _fraxlendPair.totalAsset();

2022-08-frax/src/contracts/FraxlendPairHelper.sol::182 => (uint128 _totalBorrowAmount, uint128 _totalBorrowShares) = _fraxlendPair.totalBorrow();

2022-08-frax/src/contracts/FraxlendPairHelper.sol::198 => _newRate = uint64(_fraxlendPair.penaltyRate());

2022-08-frax/src/contracts/FraxlendPairHelper.sol::232 => (, uint64 _feeToProtocolRate, , ) = _fraxlendPair.currentRateInfo();

2022-08-frax/src/contracts/FraxlendPairHelper.sol::234 => (uint128 _totalAssetAmount, uint128 _totalAssetShares) = _fraxlendPair.totalAsset();

2022-08-frax/src/contracts/FraxlendPairHelper.sol::243 => uint128 _sharesToLiquidate,

2022-08-frax/src/contracts/FraxlendPairHelper.sol::249 => uint128 _amountLiquidatorToRepay,

2022-08-frax/src/contracts/FraxlendPairHelper.sol::251 => uint128 _sharesToSocialize,

2022-08-frax/src/contracts/FraxlendPairHelper.sol::252 => uint128 _amountToSocialize

2022-08-frax/src/contracts/FraxlendPairHelper.sol::259 => (uint128 _totalBorrowAmount, uint128 _totalBorrowShares) = _fraxlendPair.totalBorrow();

2022-08-frax/src/contracts/FraxlendPairHelper.sol::264 => uint128 _borrowerShares;

2022-08-frax/src/contracts/FraxlendPairHelper.sol::267 => _borrowerShares = _fraxlendPair.userBorrowShares(_borrower).toUint128();

2022-08-frax/src/contracts/FraxlendPairHelper.sol::289 => _amountLiquidatorToRepay = (_totalBorrow.toAmount(_sharesToLiquidate, true)).toUint128();

2022-08-frax/src/contracts/FraxlendPairHelper.sol::295 => _amountToSocialize = (_totalBorrow.toAmount(_sharesToSocialize, false)).toUint128();

2022-08-frax/src/contracts/LinearInterestRate.sol::76 => function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {

2022-08-frax/src/contracts/LinearInterestRate.sol::78 => (, , uint256 _utilization, ) = abi.decode(_data, (uint64, uint256, uint256, uint256));

2022-08-frax/src/contracts/LinearInterestRate.sol::85 => _newRatePerSec = uint64(_minInterest + ((_utilization * _slope) / UTIL_PREC));

2022-08-frax/src/contracts/LinearInterestRate.sol::88 => _newRatePerSec = uint64(_vertexInterest + (((_utilization - _vertexUtilization) * _slope) / UTIL_PREC));

2022-08-frax/src/contracts/LinearInterestRate.sol::90 => _newRatePerSec = uint64(_vertexInterest);

2022-08-frax/src/contracts/VariableInterestRate.sol::35 => uint32 private constant MIN_UTIL = 75000; // 75%

2022-08-frax/src/contracts/VariableInterestRate.sol::36 => uint32 private constant MAX_UTIL = 85000; // 85%

2022-08-frax/src/contracts/VariableInterestRate.sol::37 => uint32 private constant UTIL_PREC = 1e5; // 5 decimals

2022-08-frax/src/contracts/VariableInterestRate.sol::40 => uint64 private constant MIN_INT = 79123523; // 0.25% annual rate

2022-08-frax/src/contracts/VariableInterestRate.sol::41 => uint64 private constant MAX_INT = 146248508681; // 10,000% annual rate

2022-08-frax/src/contracts/VariableInterestRate.sol::63 => function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {

2022-08-frax/src/contracts/VariableInterestRate.sol::64 => (uint64 _currentRatePerSec, uint256 _deltaTime, uint256 _utilization, ) = abi.decode(

2022-08-frax/src/contracts/VariableInterestRate.sol::66 => (uint64, uint256, uint256, uint256)

2022-08-frax/src/contracts/VariableInterestRate.sol::71 => _newRatePerSec = uint64((_currentRatePerSec * INT_HALF_LIFE) / _decayGrowth);

2022-08-frax/src/contracts/VariableInterestRate.sol::78 => _newRatePerSec = uint64((_currentRatePerSec * _decayGrowth) / INT_HALF_LIFE);

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::23 => uint64 _newRate

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::50 => function changeFee(uint32 _newFee) external;

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::64 => uint64 lastBlock,

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::65 => uint64 feeToProtocolRate,

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::66 => uint64 lastTimestamp,

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::67 => uint64 ratePerSec

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::70 => function decimals() external pure returns (uint8);

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::78 => function exchangeRateInfo() external view returns (uint32 lastTimestamp, uint224 exchangeRate);

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::89 => uint64 _DEFAULT_INT,

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::90 => uint16 _DEFAULT_PROTOCOL_FEE,

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::114 => uint128 _sharesToLiquidate,

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::196 => function totalAsset() external view returns (uint128 amount, uint128 shares);

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::200 => function totalBorrow() external view returns (uint128 amount, uint128 shares);

2022-08-frax/src/contracts/interfaces/IFraxlendPair.sol::232 => function withdrawFees(uint128 _shares, address _recipient) external returns (uint256 _amountToTransfer);

2022-08-frax/src/contracts/interfaces/IRateCalculator.sol::11 => function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec);

2022-08-frax/src/contracts/libraries/SafeERC20.sol::22 => uint8 i = 0;

2022-08-frax/src/contracts/libraries/SafeERC20.sol::55 => function safeDecimals(IERC20 token) internal view returns (uint8) {

2022-08-frax/src/contracts/libraries/SafeERC20.sol::57 => return success && data.length == 32 ? abi.decode(data, (uint8)) : 18;

2022-08-frax/src/contracts/libraries/VaultAccount.sol::5 => uint128 amount; // Total amount, analogous to market cap

2022-08-frax/src/contracts/libraries/VaultAccount.sol::6 => uint128 shares; // Total shares, analogous to shares outstanding

2022-08-frax/src/test/e2e/BasePairTest.sol::34 => abi.encode(uint80(0), int256((numerator * 10**_oracle.decimals()) / denominator), 0, 0, uint80(0))

2022-08-frax/src/test/e2e/BasePairTest.sol::60 => uint128 totalAssetAmount;

2022-08-frax/src/test/e2e/BasePairTest.sol::61 => uint128 totalAssetShares;

2022-08-frax/src/test/e2e/BasePairTest.sol::62 => uint128 totalBorrowAmount;

2022-08-frax/src/test/e2e/BasePairTest.sol::63 => uint128 totalBorrowShares;

2022-08-frax/src/test/e2e/BasePairTest.sol::97 => uint128 _totalAssetAmount,

2022-08-frax/src/test/e2e/BasePairTest.sol::98 => uint128 _totalAssetShares,

2022-08-frax/src/test/e2e/BasePairTest.sol::99 => uint128 _totalBorrowAmount,

2022-08-frax/src/test/e2e/BasePairTest.sol::100 => uint128 _totalBorrowShares,

2022-08-frax/src/test/e2e/BasePairTest.sol::112 => uint128 _totalAssetAmount,

2022-08-frax/src/test/e2e/BasePairTest.sol::113 => uint128 _totalAssetShares,

2022-08-frax/src/test/e2e/BasePairTest.sol::114 => uint128 _totalBorrowAmount,

2022-08-frax/src/test/e2e/BasePairTest.sol::115 => uint128 _totalBorrowShares,

2022-08-frax/src/test/e2e/BasePairTest.sol::255 => pair.changeFee(uint16((10 * FEE_PRECISION) / 100));

2022-08-frax/src/test/e2e/BasePairTest.sol::308 => pair.changeFee(uint16((10 * FEE_PRECISION) / 100));

2022-08-frax/src/test/e2e/BasePairTest.sol::493 => pair.borrowAsset(uint128(_amountToBorrow), _collateralAmount, _user);

2022-08-frax/src/test/e2e/BasePairTest.sol::557 => uint32 MIN_UTIL,

2022-08-frax/src/test/e2e/BasePairTest.sol::558 => uint32 MAX_UTIL,

2022-08-frax/src/test/e2e/BasePairTest.sol::559 => uint32 UTIL_PREC,

2022-08-frax/src/test/e2e/BasePairTest.sol::560 => uint64 MIN_INT,

2022-08-frax/src/test/e2e/BasePairTest.sol::561 => uint64 MAX_INT,

2022-08-frax/src/test/e2e/BasePairTest.sol::563 => ) = abi.decode(_constants, (uint32, uint32, uint32, uint64, uint64, uint256));

2022-08-frax/src/test/e2e/BasePairTest.sol::589 => function ratePerSec(FraxlendPair _pair) internal view returns (uint64 _ratePerSec) {

2022-08-frax/src/test/e2e/BasePairTest.sol::593 => function feeToProtocolRate(FraxlendPair _pair) internal view returns (uint64 _feeToProtocolRate) {

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::27 => (uint128 _amount, uint128 _shares) = pair.totalBorrow();

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::42 => function testFuzzyMaxLTVBorrowToken(uint64 _maxLTV) public {

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::117 => function testBorrowTokenFuzz(uint128 _amountToBorrow, uint128 _amountInPool) public {

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::120 => vm.assume(_amountInPool < type(uint128).max);

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::153 => uint128 _amountToBorrow = 15e20; // 1.5k

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::154 => uint128 _amountInPool = 15e23; // 1.5m

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::162 => pair.borrowAsset(uint128(_amountToBorrow), _collateralAmount, users[2]);

2022-08-frax/src/test/e2e/FraxlendPairHelper.sol::12 => uint128 _amountToBorrow = 14e23; // 1.4m

2022-08-frax/src/test/e2e/FraxlendPairHelper.sol::13 => uint128 _amountInPool = 15e23; // 1.5m

2022-08-frax/src/test/e2e/FraxlendPairHelper.sol::36 => (uint256 _interestEarned, uint256 _feesAmount, uint256 _feesShare, uint64 _newRate) = pair.addInterest();

2022-08-frax/src/test/e2e/InterestPairTest.sol::23 => borrowToken(uint128(_amountToBorrow), _collateralAmount, users[2]);

2022-08-frax/src/test/e2e/LiquidationPairTest.sol::26 => borrowToken(uint128(_amountToBorrow), _collateralAmount, users[2]);

2022-08-frax/src/test/e2e/LiquidationPairTest.sol::32 => uint128 _shares = pair.userBorrowShares(users[2]).toUint128();

2022-08-frax/src/test/e2e/LiquidationPairTest.sol::57 => borrowToken(uint128(_amountToBorrow), _collateralAmount, users[2]);

2022-08-frax/src/test/e2e/LiquidationPairTest.sol::58 => uint128 _shares = pair.userBorrowShares(users[2]).toUint128();

2022-08-frax/src/test/e2e/VariableRateTest.sol::13 => uint64 _currentRatePerSec,

2022-08-frax/src/test/e2e/VariableRateTest.sol::14 => uint16 _deltaTime,

2022-08-frax/src/test/e2e/VariableRateTest.sol::15 => uint32 _utilization

2022-08-frax/src/test/e2e/VariableRateTest.sol::22 => uint64 _currentRatePerSec,

2022-08-frax/src/test/e2e/VariableRateTest.sol::23 => uint16 _deltaTime,

2022-08-frax/src/test/e2e/VariableRateTest.sol::24 => uint32 _utilization

2022-08-frax/src/test/e2e/VariableRateTest.sol::30 => uint32 MIN_UTIL,

2022-08-frax/src/test/e2e/VariableRateTest.sol::31 => uint32 MAX_UTIL,

2022-08-frax/src/test/e2e/VariableRateTest.sol::32 => uint32 UTIL_PREC,

2022-08-frax/src/test/e2e/VariableRateTest.sol::33 => uint64 MIN_INT,

2022-08-frax/src/test/e2e/VariableRateTest.sol::34 => uint64 MAX_INT,

2022-08-frax/src/test/e2e/VariableRateTest.sol::36 => ) = abi.decode(variableRateContract.getConstants(), (uint32, uint32, uint32, uint64, uint64, uint256));

2022-08-frax/src/test/e2e/VariableRateTest.sol::37 => _currentRatePerSec = uint64((uint256(_currentRatePerSec) + MIN_INT) % MAX_INT);

2022-08-frax/src/test/e2e/VariableRateTest.sol::47 => uint64 _currentRatePerSec,

2022-08-frax/src/test/e2e/VariableRateTest.sol::48 => uint16 _deltaTime,

2022-08-frax/src/test/e2e/VariableRateTest.sol::49 => uint32 _utilization

2022-08-frax/src/test/e2e/WithdrawPairTest.sol::12 => uint128 _amountToBorrow = 15e20; // 1.5k

2022-08-frax/src/test/e2e/WithdrawPairTest.sol::13 => uint128 _amountInPool = 15e23; // 1.5m

2022-08-frax/src/test/e2e/WithdrawPairTest.sol::57 => uint128 _amountToBorrow = 15e20; // 1.5k

2022-08-frax/src/test/e2e/WithdrawPairTest.sol::58 => uint128 _amountInPool = 15e23; // 1.5m

2022-08-frax/src/test/interfaces/IUniswapV2Pair.sol::11 => function decimals() external pure returns (uint8);

2022-08-frax/src/test/interfaces/IUniswapV2Pair.sol::40 => uint8 v,

2022-08-frax/src/test/interfaces/IUniswapV2Pair.sol::71 => uint32 blockTimestampLast

2022-08-frax/src/test/interfaces/IUniswapV2Router01.sol::69 => uint8 v,

2022-08-frax/src/test/interfaces/IUniswapV2Router01.sol::82 => uint8 v,

2022-08-frax/src/test/interfaces/IUniswapV2Router02.sol::23 => uint8 v,

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::25 => uint160(
```

## Use `abi.encodePacked()` instead of `abi.encode()` (35 instances)

`abi.encodePacked()` is more efficient than `abi.encode()`.

```solidity
2022-08-frax/src/contracts/FraxlendPairCore.sol::450 => bytes memory _rateData = abi.encode(

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::213 => abi.encode(

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::331 => abi.encode(CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS, TIME_LOCK_ADDRESS, FRAXLEND_WHITELIST_ADDRESS),

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::374 => abi.encode(CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS, TIME_LOCK_ADDRESS, FRAXLEND_WHITELIST_ADDRESS),

2022-08-frax/src/contracts/FraxlendPairHelper.sol::201 => abi.encode(

2022-08-frax/src/contracts/LinearInterestRate.sol::47 => return abi.encode(MIN_INT, MAX_INT, MAX_VERTEX_UTIL, UTIL_PREC);

2022-08-frax/src/contracts/VariableInterestRate.sol::53 => return abi.encode(MIN_UTIL, MAX_UTIL, UTIL_PREC, MIN_INT, MAX_INT, INT_HALF_LIFE);

2022-08-frax/src/test/e2e/BasePairTest.sol::34 => abi.encode(uint80(0), int256((numerator * 10**_oracle.decimals()) / denominator), 0, 0, uint80(0))

2022-08-frax/src/test/e2e/BasePairTest.sol::143 => return abi.encode(_minInterest, _vertexInterest, _maxInterest, _vertexUtilization);

2022-08-frax/src/test/e2e/BasePairTest.sol::176 => return (address(variableRateContract), abi.encode());

2022-08-frax/src/test/e2e/BasePairTest.sol::180 => abi.encode(

2022-08-frax/src/test/e2e/BasePairTest.sol::238 => abi.encode(

2022-08-frax/src/test/e2e/BasePairTest.sol::264 => _configData = abi.encode(

2022-08-frax/src/test/e2e/BasePairTest.sol::319 => deployFraxlendPublic(1e10, address(variableRateContract), abi.encode());

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::143 => abi.encode(),

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::20 => abi.encode(

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::35 => abi.encode(

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::68 => abi.encode(_minInterest, _vertexInterest, _maxInterest, _vertexUtilization)

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::85 => abi.encode(

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::92 => abi.encode()

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::97 => abi.encode(

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::104 => abi.encode()

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::135 => abi.encode(_minInterest, _vertexInterest, _maxInterest, _vertexUtilization)

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::166 => abi.encode(

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::173 => abi.encode()

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::200 => abi.encode(

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::207 => abi.encode()

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::234 => abi.encode(

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::241 => abi.encode()

2022-08-frax/src/test/e2e/LendPairTest.t.sol::68 => abi.encode(),

2022-08-frax/src/test/e2e/LeveragePairTest.sol::29 => deployFraxlendPublic(1e10, address(variableRateContract), abi.encode());

2022-08-frax/src/test/e2e/LinearRateTest.sol::24 => bytes memory _initData = abi.encode(_minInterest, _vertexInterest, _maxInterest, _vertexUtilization);

2022-08-frax/src/test/e2e/LinearRateTest.sol::35 => bytes memory _initData = abi.encode(_minInterest, _vertexInterest, _maxInterest, _vertexUtilization);

2022-08-frax/src/test/e2e/LinearRateTest.sol::77 => _newRate = linearRateContract.getNewRate(abi.encode(0, 0, _utilization, 0), _initData);

2022-08-frax/src/test/e2e/VariableRateTest.sol::51 => uint256 _newRate = this.getNewRate(abi.encode(_currentRatePerSec, _deltaTime, _utilization, 0), abi.encode());
```

## Don't compare boolean expressions to boolean literals (3 instances)

`if (<x> == true)` can be refactored to `if (<x>)`, `if (<x> == false)` can be refactored to `if (!<x>)`.

```solidity
2022-08-frax/src/contracts/FraxlendPairCore.sol::308 => if (maxLTV == 0) return true;

2022-08-frax/src/contracts/FraxlendPairCore.sol::310 => if (_borrowerAmount == 0) return true;

2022-08-frax/src/contracts/FraxlendPairCore.sol::312 => if (_collateralAmount == 0) return false;
```

## Do not use SafeMath for Solidity >= 0.8.0 (1 instances)

Solidity v0.8.0 introduces built-in overflow checks, so using `SafeMath` is a waste of gas.

```solidity
2022-08-frax/src/test/interfaces/UniswapV2Library.sol::5 => import "./SafeMath.sol";
```

## Use custom errors instead of `revert()`/`require()` strings (18 instances)

Using `require()`/`revert()` strings is expensive. Starting from Soldity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors.

Custom errors decrease both deploy and runtime gas costs. Note that runtime gas cost is only relevant when the revert condition is met.

```solidity
2022-08-frax/src/contracts/FraxlendPairDeployer.sol::205 => require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::228 => require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::253 => require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::365 => require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::399 => require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");

2022-08-frax/src/test/interfaces/SafeMath.sol::7 => require((z = x + y) >= x, "ds-math-add-overflow");

2022-08-frax/src/test/interfaces/SafeMath.sol::11 => require((z = x - y) <= x, "ds-math-sub-underflow");

2022-08-frax/src/test/interfaces/SafeMath.sol::15 => require(y == 0 || (z = x * y) / y == x, "ds-math-mul-overflow");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::12 => require(tokenA != tokenB, "UniswapV2Library: IDENTICAL_ADDRESSES");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::14 => require(token0 != address(0), "UniswapV2Library: ZERO_ADDRESS");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::57 => require(amountA > 0, "UniswapV2Library: INSUFFICIENT_AMOUNT");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::58 => require(reserveA > 0 && reserveB > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::68 => require(amountIn > 0, "UniswapV2Library: INSUFFICIENT_INPUT_AMOUNT");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::69 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::82 => require(amountOut > 0, "UniswapV2Library: INSUFFICIENT_OUTPUT_AMOUNT");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::83 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::95 => require(path.length >= 2, "UniswapV2Library: INVALID_PATH");

2022-08-frax/src/test/interfaces/UniswapV2Library.sol::110 => require(path.length >= 2, "UniswapV2Library: INVALID_PATH");
```

## Use shift right/left instead of division/multiplication if possible (6 instances)

A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left. While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```solidity
2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::55 => (MIN_INT + (MAX_INT / 1000)) / 2,

2022-08-frax/src/test/e2e/LeveragePairTest.sol::67 => uint256 _idealCollateral2 = pair.userCollateralBalance(users[1]) / 2;

2022-08-frax/src/test/e2e/LinearRateTest.sol::59 => _getNewRate(_vertexUtilization / 2, _initData),

2022-08-frax/src/test/e2e/LinearRateTest.sol::60 => _minInterest + ((_vertexInterest - _minInterest) / 2),

2022-08-frax/src/test/e2e/LinearRateTest.sol::69 => _getNewRate(((1e5 - _vertexUtilization) / 2) + _vertexUtilization, _initData),

2022-08-frax/src/test/e2e/LinearRateTest.sol::70 => _vertexInterest + ((_maxInterest - _vertexInterest) / 2),
```

## Use `storage` instead of `memory` for structs/arrays saves gas (49 instances)

When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.

```solidity
2022-08-frax/src/contracts/FraxlendPair.sol::236 => VaultAccount memory _totalAsset = totalAsset;

2022-08-frax/src/contracts/FraxlendPair.sol::237 => VaultAccount memory _totalBorrow = totalBorrow;

2022-08-frax/src/contracts/FraxlendPairCore.sol::419 => CurrentRateInfo memory _currentRateInfo = currentRateInfo;

2022-08-frax/src/contracts/FraxlendPairCore.sol::426 => VaultAccount memory _totalAsset = totalAsset;

2022-08-frax/src/contracts/FraxlendPairCore.sol::427 => VaultAccount memory _totalBorrow = totalBorrow;

2022-08-frax/src/contracts/FraxlendPairCore.sol::450 => bytes memory _rateData = abi.encode(

2022-08-frax/src/contracts/FraxlendPairCore.sol::517 => ExchangeRateInfo memory _exchangeRateInfo = exchangeRateInfo;

2022-08-frax/src/contracts/FraxlendPairCore.sol::592 => VaultAccount memory _totalAsset = totalAsset;

2022-08-frax/src/contracts/FraxlendPairCore.sol::611 => VaultAccount memory _totalAsset = totalAsset;

2022-08-frax/src/contracts/FraxlendPairCore.sol::666 => VaultAccount memory _totalAsset = totalAsset;

2022-08-frax/src/contracts/FraxlendPairCore.sol::682 => VaultAccount memory _totalAsset = totalAsset;

2022-08-frax/src/contracts/FraxlendPairCore.sol::708 => VaultAccount memory _totalBorrow = totalBorrow;

2022-08-frax/src/contracts/FraxlendPairCore.sol::884 => VaultAccount memory _totalBorrow = totalBorrow;

2022-08-frax/src/contracts/FraxlendPairCore.sol::926 => VaultAccount memory _totalBorrow = totalBorrow;

2022-08-frax/src/contracts/FraxlendPairCore.sol::965 => VaultAccount memory _totalBorrow = totalBorrow;

2022-08-frax/src/contracts/FraxlendPairCore.sol::1204 => VaultAccount memory _totalBorrow = totalBorrow;

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::123 => string[] memory _deployedPairsArray = deployedPairsArray;

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::125 => address[] memory _addresses = new address[](_lengthOfArray);

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::171 => bytes memory _firstHalf = BytesLib.slice(_creationCode, 0, 13000);

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::174 => bytes memory _secondHalf = BytesLib.slice(_creationCode, 13000, _creationCode.length - 13000);

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::207 => bytes memory _creationCode = BytesLib.concat(

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::211 => bytes memory bytecode = abi.encodePacked(

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::249 => (, , , , , , bytes memory _rateInitData) = abi.decode(

2022-08-frax/src/contracts/FraxlendPairDeployer.sol::315 => string memory _name = string(

2022-08-frax/src/contracts/libraries/SafeERC20.sol::26 => bytes memory bytesArray = new bytes(i);

2022-08-frax/src/contracts/libraries/SafeERC20.sol::40 => (bool success, bytes memory data) = address(token).staticcall(abi.encodeWithSelector(SIG_SYMBOL));

2022-08-frax/src/contracts/libraries/SafeERC20.sol::48 => (bool success, bytes memory data) = address(token).staticcall(abi.encodeWithSelector(SIG_NAME));

2022-08-frax/src/contracts/libraries/SafeERC20.sol::56 => (bool success, bytes memory data) = address(token).staticcall(abi.encodeWithSelector(SIG_DECIMALS));

2022-08-frax/src/test/e2e/BasePairTest.sol::202 => address[] memory _rateAddresses = new address[](2);

2022-08-frax/src/test/e2e/BasePairTest.sol::208 => address[] memory _oracleAddresses = new address[](8);

2022-08-frax/src/test/e2e/BasePairTest.sol::226 => address[] memory _deployerAddresses = new address[](1);

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::82 => (address _rateContract, bytes memory _rateInitData) = fuzzyRateCalculator(

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::93 => address[] memory _borrowerWhitelist = _maxLTV >= LTV_PRECISION ? users : new address[](0);

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::94 => address[] memory _lenderWhitelist = _maxLTV >= LTV_PRECISION ? users : new address[](0);

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::136 => address[] memory approvedBorrowers = new address[](1);

2022-08-frax/src/test/e2e/BorrowPairTest.t.sol::137 => address[] memory approvedLenders = new address[](1);

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::18 => bytes memory _rateInitData = defaultRateInitForLinear();

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::32 => bytes memory _rateInitData2 = defaultRateInitForLinear();

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::191 => address[] memory _contracts = new address[](2);

2022-08-frax/src/test/e2e/FraxlendPairDeployerTest.sol::228 => address[] memory _deployerAddresses = new address[](1);

2022-08-frax/src/test/e2e/LendPairTest.t.sol::61 => address[] memory approvedBorrowers = new address[](1);

2022-08-frax/src/test/e2e/LendPairTest.t.sol::62 => address[] memory approvedLenders = new address[](1);

2022-08-frax/src/test/e2e/LeveragePairTest.sol::44 => address[] memory _path2 = new address[](2);

2022-08-frax/src/test/e2e/LeveragePairTest.sol::69 => address[] memory _path = new address[](2);

2022-08-frax/src/test/e2e/LinearRateTest.sol::13 => bytes memory _initData = defaultRateInitForLinear();

2022-08-frax/src/test/e2e/LinearRateTest.sol::24 => bytes memory _initData = abi.encode(_minInterest, _vertexInterest, _maxInterest, _vertexUtilization);

2022-08-frax/src/test/e2e/LinearRateTest.sol::35 => bytes memory _initData = abi.encode(_minInterest, _vertexInterest, _maxInterest, _vertexUtilization);

2022-08-frax/src/test/e2e/VariableRateTest.sol::53 => string[] memory _inputs = new string[](5);

2022-08-frax/src/test/e2e/VariableRateTest.sol::59 => bytes memory _ret = vm.ffi(_inputs);
```
