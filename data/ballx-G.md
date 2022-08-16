# c4udit Report

## Files analyzed
- src/contracts/FraxlendPair.sol
- src/contracts/FraxlendPairConstants.sol
- src/contracts/FraxlendPairCore.sol
- src/contracts/FraxlendPairDeployer.sol
- src/contracts/FraxlendPairHelper.sol
- src/contracts/FraxlendWhitelist.sol
- src/contracts/LinearInterestRate.sol
- src/contracts/VariableInterestRate.sol
- src/contracts/interfaces/IERC4626.sol
- src/contracts/interfaces/IFraxlendPair.sol
- src/contracts/interfaces/IFraxlendWhitelist.sol
- src/contracts/interfaces/IRateCalculator.sol
- src/contracts/interfaces/ISwapper.sol
- src/contracts/libraries/SafeERC20.sol
- src/contracts/libraries/VaultAccount.sol

## Issues found

### Don't Initialize Variables with Default Value

#### Impact
Issue Information: [G001](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g001---dont-initialize-variables-with-default-value)

#### Findings:
```
src/contracts/FraxlendPair.sol::289 => for (uint256 i = 0; i < _lenders.length; i++) {
src/contracts/FraxlendPair.sol::308 => for (uint256 i = 0; i < _borrowers.length; i++) {
src/contracts/FraxlendPairCore.sol::265 => for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
src/contracts/FraxlendPairCore.sol::270 => for (uint256 i = 0; i < _approvedLenders.length; ++i) {
src/contracts/FraxlendPairDeployer.sol::402 => for (uint256 i = 0; i < _lengthOfArray; ) {
src/contracts/FraxlendWhitelist.sol::51 => for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/FraxlendWhitelist.sol::66 => for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/FraxlendWhitelist.sol::81 => for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/libraries/SafeERC20.sol::22 => uint8 i = 0;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Cache Array Length Outside of Loop

#### Impact
Issue Information: [G002](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)

#### Findings:
```
src/contracts/FraxlendPair.sol::80 => // solhint-disable-next-line max-line-length
src/contracts/FraxlendPair.sol::289 => for (uint256 i = 0; i < _lenders.length; i++) {
src/contracts/FraxlendPair.sol::308 => for (uint256 i = 0; i < _borrowers.length; i++) {
src/contracts/FraxlendPairCore.sol::254 => if (bytes(_name).length == 0) {
src/contracts/FraxlendPairCore.sol::257 => if (bytes(nameOfContract).length != 0) {
src/contracts/FraxlendPairCore.sol::265 => for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
src/contracts/FraxlendPairCore.sol::270 => for (uint256 i = 0; i < _approvedLenders.length; ++i) {
src/contracts/FraxlendPairCore.sol::1089 => if (_path[_path.length - 1] != address(_collateralContract)) {
src/contracts/FraxlendPairCore.sol::1090 => revert InvalidPath(address(_collateralContract), _path[_path.length - 1]);
src/contracts/FraxlendPairCore.sol::1175 => if (_path[_path.length - 1] != address(_assetContract)) {
src/contracts/FraxlendPairCore.sol::1176 => revert InvalidPath(address(_assetContract), _path[_path.length - 1]);
src/contracts/FraxlendPairDeployer.sol::114 => /// @notice The ```deployedPairsLength``` function returns the length of the deployedPairsArray
src/contracts/FraxlendPairDeployer.sol::115 => /// @return length of array
src/contracts/FraxlendPairDeployer.sol::117 => return deployedPairsArray.length;
src/contracts/FraxlendPairDeployer.sol::124 => uint256 _lengthOfArray = _deployedPairsArray.length;
src/contracts/FraxlendPairDeployer.sol::125 => address[] memory _addresses = new address[](_lengthOfArray);
src/contracts/FraxlendPairDeployer.sol::127 => for (i = 0; i < _lengthOfArray; ) {
src/contracts/FraxlendPairDeployer.sol::149 => uint256 _lengthOfArray = _addresses.length;
src/contracts/FraxlendPairDeployer.sol::151 => _pairCustomStatuses = new PairCustomStatus[](_lengthOfArray);
src/contracts/FraxlendPairDeployer.sol::152 => for (i = 0; i < _lengthOfArray; ) {
src/contracts/FraxlendPairDeployer.sol::173 => if (_creationCode.length > 13000) {
src/contracts/FraxlendPairDeployer.sol::174 => bytes memory _secondHalf = BytesLib.slice(_creationCode, 13000, _creationCode.length - 13000);
src/contracts/FraxlendPairDeployer.sol::324 => (deployedPairsArray.length + 1).toString()
src/contracts/FraxlendPairDeployer.sol::379 => _approvedBorrowers.length > 0,
src/contracts/FraxlendPairDeployer.sol::380 => _approvedLenders.length > 0
src/contracts/FraxlendPairDeployer.sol::401 => uint256 _lengthOfArray = _addresses.length;
src/contracts/FraxlendPairDeployer.sol::402 => for (uint256 i = 0; i < _lengthOfArray; ) {
src/contracts/FraxlendWhitelist.sol::51 => for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/FraxlendWhitelist.sol::66 => for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/FraxlendWhitelist.sol::81 => for (uint256 i = 0; i < _addresses.length; i++) {
src/contracts/libraries/SafeERC20.sol::8 => // solhint-disable max-line-length
src/contracts/libraries/SafeERC20.sol::19 => if (data.length >= 64) {
src/contracts/libraries/SafeERC20.sol::21 => } else if (data.length == 32) {
src/contracts/libraries/SafeERC20.sol::57 => return success && data.length == 32 ? abi.decode(data, (uint8)) : 18;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: [G003](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g003---use--0-instead-of--0-for-unsigned-integer-comparison)

#### Findings:
```
src/contracts/FraxlendPairCore.sol::440 => // We know totalBorrow.shares > 0
src/contracts/FraxlendPairCore.sol::477 => if (_currentRateInfo.feeToProtocolRate > 0) {
src/contracts/FraxlendPairCore.sol::754 => if (_collateralAmount > 0) {
src/contracts/FraxlendPairCore.sol::835 => if (userBorrowShares[msg.sender] > 0) {
src/contracts/FraxlendPairCore.sol::1002 => if (_leftoverBorrowShares > 0) {
src/contracts/FraxlendPairCore.sol::1094 => if (_initialCollateralAmount > 0) {
src/contracts/FraxlendPairDeployer.sol::379 => _approvedBorrowers.length > 0,
src/contracts/FraxlendPairDeployer.sol::380 => _approvedLenders.length > 0
src/contracts/FraxlendPairHelper.sol::235 => if (_feeToProtocolRate > 0) {
src/contracts/FraxlendPairHelper.sol::292 => if (_leftoverCollateral <= 0 && (_borrowerShares - _sharesToLiquidate) > 0) {
src/contracts/LinearInterestRate.sol::66 => _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
src/contracts/LinearInterestRate.sol::67 => "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: [G006](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g006---use-immutable-for-openzeppelin-accesscontrols-roles-declarations)

#### Findings:
```
src/contracts/FraxlendPairDeployer.sol::204 => bytes32 salt = keccak256(abi.encodePacked(_saltSeed, _configData));
src/contracts/FraxlendPairDeployer.sol::329 => keccak256(abi.encodePacked("public")),
src/contracts/FraxlendPairDeployer.sol::372 => keccak256(abi.encodePacked(_name)),
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Long Revert Strings

#### Impact
Issue Information: [G007](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
src/contracts/FraxlendPair.sol::28 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
src/contracts/FraxlendPair.sol::29 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
src/contracts/FraxlendPair.sol::30 => import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
src/contracts/FraxlendPair.sol::31 => import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";
src/contracts/FraxlendPair.sol::37 => import "./interfaces/IFraxlendWhitelist.sol";
src/contracts/FraxlendPair.sol::81 => return string(abi.encodePacked("FraxlendV1 - ", collateralContract.safeSymbol(), "/", assetContract.safeSymbol()));
src/contracts/FraxlendPairCore.sol::28 => import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
src/contracts/FraxlendPairCore.sol::29 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
src/contracts/FraxlendPairCore.sol::30 => import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
src/contracts/FraxlendPairCore.sol::31 => import "@openzeppelin/contracts/security/Pausable.sol";
src/contracts/FraxlendPairCore.sol::32 => import "@openzeppelin/contracts/access/Ownable.sol";
src/contracts/FraxlendPairCore.sol::33 => import "@openzeppelin/contracts/utils/math/SafeCast.sol";
src/contracts/FraxlendPairCore.sol::34 => import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";
src/contracts/FraxlendPairCore.sol::39 => import "./interfaces/IFraxlendWhitelist.sol";
src/contracts/FraxlendPairDeployer.sol::28 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
src/contracts/FraxlendPairDeployer.sol::29 => import "@openzeppelin/contracts/access/Ownable.sol";
src/contracts/FraxlendPairDeployer.sol::30 => import "@openzeppelin/contracts/utils/Strings.sol";
src/contracts/FraxlendPairDeployer.sol::31 => import "@rari-capital/solmate/src/utils/SSTORE2.sol";
src/contracts/FraxlendPairDeployer.sol::32 => import "solidity-bytes-utils/contracts/BytesLib.sol";
src/contracts/FraxlendPairDeployer.sol::34 => import "./interfaces/IFraxlendWhitelist.sol";
src/contracts/FraxlendPairDeployer.sol::205 => require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
src/contracts/FraxlendPairDeployer.sol::228 => require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
src/contracts/FraxlendPairDeployer.sol::253 => require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
src/contracts/FraxlendPairDeployer.sol::365 => require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
src/contracts/FraxlendPairDeployer.sol::368 => "FraxlendPairDeployer: Only whitelisted addresses"
src/contracts/FraxlendPairHelper.sol::19 => import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";
src/contracts/FraxlendPairHelper.sol::20 => import "@openzeppelin/contracts/utils/math/SafeCast.sol";
src/contracts/FraxlendWhitelist.sol::28 => import "@openzeppelin/contracts/access/Ownable.sol";
src/contracts/LinearInterestRate.sol::59 => "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
src/contracts/LinearInterestRate.sol::63 => "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
src/contracts/LinearInterestRate.sol::67 => "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
src/contracts/VariableInterestRate.sol::47 => return "Variable Time-Weighted Interest Rate";
src/contracts/interfaces/IERC4626.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
src/contracts/interfaces/IERC4626.sol::5 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
src/contracts/libraries/SafeERC20.sol::4 => import "@openzeppelin/contracts/interfaces/IERC20.sol";
src/contracts/libraries/SafeERC20.sol::5 => import { SafeERC20 as OZSafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: [G008](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

#### Findings:
```
src/contracts/VariableInterestRate.sol::36 => uint32 private constant MAX_UTIL = 85000; // 85%
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)


