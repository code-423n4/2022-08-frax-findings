## Summary

### Gas Optimizations
| |Issue|Instances|
|-|:-|:-:|
| [G&#x2011;01] | String should only be generated once, and saved | 1 |
| [G&#x2011;02] | Remove or replace unused state variables | 1 |
| [G&#x2011;03] | Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate | 1 |
| [G&#x2011;04] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 11 |
| [G&#x2011;05] | Using `storage` instead of `memory` for structs/arrays saves gas | 16 |
| [G&#x2011;06] | State variables should be cached in stack variables rather than re-reading them from storage | 2 |
| [G&#x2011;07] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables | 2 |
| [G&#x2011;08] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 3 |
| [G&#x2011;09] | `<array>.length` should not be looked up in every loop of a `for`-loop | 7 |
| [G&#x2011;10] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 8 |
| [G&#x2011;11] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 8 |
| [G&#x2011;12] | Optimize names to save gas | 6 |
| [G&#x2011;13] | Using `bool`s for storage incurs overhead | 9 |
| [G&#x2011;14] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 9 |
| [G&#x2011;15] | Splitting `require()` statements that use `&&` saves gas | 3 |
| [G&#x2011;16] | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 13 |
| [G&#x2011;17] | Using `private` rather than `public` for constants, saves gas | 8 |
| [G&#x2011;18] | Use custom errors rather than `revert()`/`require()` strings to save gas | 9 |
| [G&#x2011;19] | Functions guaranteed to revert when called by normal users can be marked `payable` | 7 |

Total: 124 instances over 19 issues



## Gas Optimizations

### [G&#x2011;01]  String should only be generated once, and saved

*There is 1 instance of this issue:*
```solidity
File: /src/contracts/FraxlendPair.sol

81:          return string(abi.encodePacked("FraxlendV1 - ", collateralContract.safeSymbol(), "/", assetContract.safeSymbol()));

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L81

### [G&#x2011;02]  Remove or replace unused state variables
Saves a storage slot. If the variable is assigned a non-zero value, saves Gsset (**20000 gas**). If it's assigned a zero value, saves Gsreset (**2900 gas**). If the variable remains unassigned, there is no gas savings unless the variable is `public`, in which case the compiler-generated non-payable getter deployment cost is saved. If the state variable is overriding an interface's public function, mark the variable as `constant` or `immutable` so that it does not use a storage slot

*There is 1 instance of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

51:       string public version = "1.0.0";

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L51

### [G&#x2011;03]  Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save **~42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There is 1 instance of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

127       mapping(address => uint256) public userCollateralBalance; // amount of collateral each user is backed
128       /// @notice Stores the balance of borrow shares for each user
129:      mapping(address => uint256) public userBorrowShares; // represents the shares held by individuals

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L127-L129

### [G&#x2011;04]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 11 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

/// @audit _configData
/// @audit _immutables
151       constructor(
152           bytes memory _configData,
153           bytes memory _immutables,
154           uint256 _maxLTV,
155           uint256 _liquidationFee,
156           uint256 _maturityDate,
157           uint256 _penaltyRate,
158           bool _isBorrowerWhitelistActive,
159:          bool _isLenderWhitelistActive

/// @audit _path
1062      function leveragedPosition(
1063          address _swapperAddress,
1064          uint256 _borrowAmount,
1065          uint256 _initialCollateralAmount,
1066          uint256 _amountCollateralOutMin,
1067          address[] memory _path
1068      )
1069          external
1070          isNotPastMaturity
1071          nonReentrant
1072          whenNotPaused
1073          approvedBorrower
1074          isSolvent(msg.sender)
1075:         returns (uint256 _totalCollateralBalance)

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L151-L159

```solidity
File: src/contracts/FraxlendPairDeployer.sol

/// @audit _configData
310:      function deploy(bytes memory _configData) external returns (address _pairAddress) {

/// @audit _name
/// @audit _configData
/// @audit _approvedBorrowers
/// @audit _approvedLenders
355       function deployCustom(
356           string memory _name,
357           bytes memory _configData,
358           uint256 _maxLTV,
359           uint256 _liquidationFee,
360           uint256 _maturityDate,
361           uint256 _penaltyRate,
362           address[] memory _approvedBorrowers,
363           address[] memory _approvedLenders
364:      ) external returns (address _pairAddress) {

/// @audit _addresses
398:      function globalPause(address[] memory _addresses) external returns (address[] memory _updatedAddresses) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L310

```solidity
File: src/contracts/FraxlendPair.sol

/// @audit _configData
/// @audit _immutables
45        constructor(
46            bytes memory _configData,
47            bytes memory _immutables,
48            uint256 _maxLTV,
49            uint256 _liquidationFee,
50            uint256 _maturityDate,
51            uint256 _penaltyRate,
52            bool _isBorrowerWhitelistActive,
53            bool _isLenderWhitelistActive
54        )
55            FraxlendPairCore(
56                _configData,
57                _immutables,
58                _maxLTV,
59                _liquidationFee,
60                _maturityDate,
61                _penaltyRate,
62                _isBorrowerWhitelistActive,
63                _isLenderWhitelistActive
64            )
65            ERC20("", "")
66            Ownable()
67:           Pausable()

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L45-L67

### [G&#x2011;05]  Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

*There are 16 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

419:          CurrentRateInfo memory _currentRateInfo = currentRateInfo;

426:          VaultAccount memory _totalAsset = totalAsset;

427:          VaultAccount memory _totalBorrow = totalBorrow;

517:          ExchangeRateInfo memory _exchangeRateInfo = exchangeRateInfo;

592:          VaultAccount memory _totalAsset = totalAsset;

611:          VaultAccount memory _totalAsset = totalAsset;

666:          VaultAccount memory _totalAsset = totalAsset;

682:          VaultAccount memory _totalAsset = totalAsset;

708:          VaultAccount memory _totalBorrow = totalBorrow;

884:          VaultAccount memory _totalBorrow = totalBorrow;

926:          VaultAccount memory _totalBorrow = totalBorrow;

965:          VaultAccount memory _totalBorrow = totalBorrow;

1204:         VaultAccount memory _totalBorrow = totalBorrow;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L419

```solidity
File: src/contracts/FraxlendPairDeployer.sol

123:          string[] memory _deployedPairsArray = deployedPairsArray;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L123

```solidity
File: src/contracts/FraxlendPair.sol

236:          VaultAccount memory _totalAsset = totalAsset;

237:          VaultAccount memory _totalBorrow = totalBorrow;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L236

### [G&#x2011;06]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 2 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairDeployer.sol

/// @audit DEFAULT_MAX_LTV on line 332
/// @audit DEFAULT_LIQ_FEE on line 333
342:          _logDeploy(_name, _pairAddress, _configData, DEFAULT_MAX_LTV, DEFAULT_LIQ_FEE, 0);

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L342

### [G&#x2011;07]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

*There are 2 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

773:          totalCollateral += _collateralAmount;

815:          totalCollateral -= _collateralAmount;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L773

### [G&#x2011;08]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There are 3 instances of this issue:*
```solidity
File: src/contracts/LinearInterestRate.sol

/// @audit if-condition on line 86
88:               _newRatePerSec = uint64(_vertexInterest + (((_utilization - _vertexUtilization) * _slope) / UTIL_PREC));

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L88

```solidity
File: src/contracts/VariableInterestRate.sol

/// @audit if-condition on line 68
69:               uint256 _deltaUtilization = ((MIN_UTIL - _utilization) * 1e18) / MIN_UTIL;

/// @audit if-condition on line 75
76:               uint256 _deltaUtilization = ((_utilization - MAX_UTIL) * 1e18) / (UTIL_PREC - MAX_UTIL);

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L69

### [G&#x2011;09]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There are 7 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

265:          for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {

270:          for (uint256 i = 0; i < _approvedLenders.length; ++i) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265

```solidity
File: src/contracts/FraxlendPair.sol

289:          for (uint256 i = 0; i < _lenders.length; i++) {

308:          for (uint256 i = 0; i < _borrowers.length; i++) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289

```solidity
File: src/contracts/FraxlendWhitelist.sol

51:           for (uint256 i = 0; i < _addresses.length; i++) {

66:           for (uint256 i = 0; i < _addresses.length; i++) {

81:           for (uint256 i = 0; i < _addresses.length; i++) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51

### [G&#x2011;10]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 8 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

265:          for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {

270:          for (uint256 i = 0; i < _approvedLenders.length; ++i) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265

```solidity
File: src/contracts/FraxlendPair.sol

289:          for (uint256 i = 0; i < _lenders.length; i++) {

308:          for (uint256 i = 0; i < _borrowers.length; i++) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289

```solidity
File: src/contracts/FraxlendWhitelist.sol

51:           for (uint256 i = 0; i < _addresses.length; i++) {

66:           for (uint256 i = 0; i < _addresses.length; i++) {

81:           for (uint256 i = 0; i < _addresses.length; i++) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51

```solidity
File: src/contracts/libraries/SafeERC20.sol

27:               for (i = 0; i < 32 && data[i] != 0; i++) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L27

### [G&#x2011;11]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There are 8 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairDeployer.sol

205:              require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");

228:              require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");

253:          require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");

365:          require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");

366           require(
367               IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
368               "FraxlendPairDeployer: Only whitelisted addresses"
369:          );

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L205

```solidity
File: src/contracts/LinearInterestRate.sol

57            require(
58                _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
59                "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
60:           );

61            require(
62                _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
63                "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
64:           );

65            require(
66                _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
67                "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
68:           );

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L60

### [G&#x2011;12]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 6 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

/// @audit initialize(), addInterest(), updateExchangeRate(), borrowAsset(), addCollateral(), removeCollateral(), repayAsset(), liquidate(), liquidateClean(), leveragedPosition(), repayAssetWithCollateral()
46:   abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ownable, Pausable, ReentrancyGuard {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L46

```solidity
File: src/contracts/FraxlendPairDeployer.sol

/// @audit deployedPairsLength(), getAllPairAddresses(), getCustomStatuses(), setCreationCode(), deploy(), deployCustom(), globalPause()
44:   contract FraxlendPairDeployer is Ownable {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L44

```solidity
File: src/contracts/FraxlendPair.sol

/// @audit assetsPerShare(), assetsOf(), getConstants(), toBorrowShares(), toBorrowAmount(), setTimeLock(), changeFee(), withdrawFees(), setSwapper(), setApprovedLenders(), setApprovedBorrowers(), pause(), unpause()
41:   contract FraxlendPair is FraxlendPairCore {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L41

```solidity
File: src/contracts/FraxlendWhitelist.sol

/// @audit setOracleContractWhitelist(), setRateContractWhitelist(), setFraxlendDeployerWhitelist()
30:   contract FraxlendWhitelist is Ownable {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L30

```solidity
File: src/contracts/LinearInterestRate.sol

/// @audit getConstants(), requireValidInitData(), getNewRate()
32:   contract LinearInterestRate is IRateCalculator {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L32

```solidity
File: src/contracts/VariableInterestRate.sol

/// @audit getConstants(), requireValidInitData(), getNewRate()
33:   contract VariableInterestRate is IRateCalculator {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L33

### [G&#x2011;13]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There are 9 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

78:       mapping(address => bool) public swappers; // approved swapper addresses

133:      bool public immutable borrowerWhitelistActive;

134:      mapping(address => bool) public approvedBorrowers;

136:      bool public immutable lenderWhitelistActive;

137:      mapping(address => bool) public approvedLenders;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L78

```solidity
File: src/contracts/FraxlendPairDeployer.sol

94:       mapping(address => bool) public deployedPairCustomStatusByAddress;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L94

```solidity
File: src/contracts/FraxlendWhitelist.sol

32:       mapping(address => bool) public oracleContractWhitelist;

35:       mapping(address => bool) public rateContractWhitelist;

38:       mapping(address => bool) public fraxlendDeployerWhitelist;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L32

### [G&#x2011;14]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There are 9 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairDeployer.sol

130:                  i++;

158:                  i++;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L130

```solidity
File: src/contracts/FraxlendPair.sol

289:          for (uint256 i = 0; i < _lenders.length; i++) {

308:          for (uint256 i = 0; i < _borrowers.length; i++) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289

```solidity
File: src/contracts/FraxlendWhitelist.sol

51:           for (uint256 i = 0; i < _addresses.length; i++) {

66:           for (uint256 i = 0; i < _addresses.length; i++) {

81:           for (uint256 i = 0; i < _addresses.length; i++) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51

```solidity
File: src/contracts/libraries/SafeERC20.sol

24:                   i++;

27:               for (i = 0; i < 32 && data[i] != 0; i++) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L24

### [G&#x2011;15]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There are 3 instances of this issue:*
```solidity
File: src/contracts/LinearInterestRate.sol

57            require(
58                _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
59                "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
60:           );

61            require(
62                _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
63                "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
64:           );

65            require(
66                _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
67                "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
68:           );

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L60

### [G&#x2011;16]  Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra [**22-28 gas**](https://gist.github.com/IllIllI000/9388d20c70f9a4632eb3ca7836f54977) (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

*There are 13 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

/// @audit uint64 _newRate
421:              _newRate = _currentRateInfo.ratePerSec;

/// @audit uint64 _newRate
448:                  _newRate = uint64(penaltyRate);

/// @audit uint64 _newRate
456:                  _newRate = IRateCalculator(rateContract).getNewRate(_rateData, rateInitCallData);

/// @audit uint128 _amountToAdjust
1005:                     _amountToAdjust = (_totalBorrow.toAmount(_sharesToAdjust, false)).toUint128();

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L421

```solidity
File: src/contracts/FraxlendPair.sol

/// @audit uint64 _DEFAULT_INT
175:          _DEFAULT_INT = DEFAULT_INT;

/// @audit uint16 _DEFAULT_PROTOCOL_FEE
176:          _DEFAULT_PROTOCOL_FEE = DEFAULT_PROTOCOL_FEE;

/// @audit uint128 _shares
240:          if (_shares == 0) _shares = uint128(balanceOf(address(this)));

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L175

```solidity
File: src/contracts/libraries/SafeERC20.sol

/// @audit uint8 i
27:               for (i = 0; i < 32 && data[i] != 0; i++) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L27

```solidity
File: src/contracts/LinearInterestRate.sol

/// @audit uint64 _newRatePerSec
85:               _newRatePerSec = uint64(_minInterest + ((_utilization * _slope) / UTIL_PREC));

/// @audit uint64 _newRatePerSec
88:               _newRatePerSec = uint64(_vertexInterest + (((_utilization - _vertexUtilization) * _slope) / UTIL_PREC));

/// @audit uint64 _newRatePerSec
90:               _newRatePerSec = uint64(_vertexInterest);

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L85

```solidity
File: src/contracts/VariableInterestRate.sol

/// @audit uint64 _newRatePerSec
71:               _newRatePerSec = uint64((_currentRatePerSec * INT_HALF_LIFE) / _decayGrowth);

/// @audit uint64 _newRatePerSec
78:               _newRatePerSec = uint64((_currentRatePerSec * _decayGrowth) / INT_HALF_LIFE);

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L71

### [G&#x2011;17]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 8 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

64:       uint256 public immutable oracleNormalization;

67:       uint256 public immutable maxLTV;

70:       uint256 public immutable cleanLiquidationFee;

71:       uint256 public immutable dirtyLiquidationFee;

95:       uint256 public immutable maturityDate;

96:       uint256 public immutable penaltyRate;

133:      bool public immutable borrowerWhitelistActive;

136:      bool public immutable lenderWhitelistActive;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L64

### [G&#x2011;18]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 9 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairDeployer.sol

205:              require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");

228:              require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");

253:          require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");

365:          require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");

366           require(
367               IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
368               "FraxlendPairDeployer: Only whitelisted addresses"
369:          );

399:          require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L205

```solidity
File: src/contracts/LinearInterestRate.sol

57            require(
58                _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
59                "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
60:           );

61            require(
62                _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
63                "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
64:           );

65            require(
66                _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
67                "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
68:           );

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L60

### [G&#x2011;19]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 7 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairDeployer.sol

170:      function setCreationCode(bytes calldata _creationCode) external onlyOwner {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L170

```solidity
File: src/contracts/FraxlendPair.sol

204:      function setTimeLock(address _newAddress) external onlyOwner {

234:      function withdrawFees(uint128 _shares, address _recipient) external onlyOwner returns (uint256 _amountToTransfer) {

274:      function setSwapper(address _swapper, bool _approval) external onlyOwner {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L204

```solidity
File: src/contracts/FraxlendWhitelist.sol

50:       function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {

65:       function setRateContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {

80:       function setFraxlendDeployerWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L50

