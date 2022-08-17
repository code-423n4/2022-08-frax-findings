## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Allow-list not required if `maxLTV` is zero | 1 |
| [L&#x2011;02] | Exchange data not fetched once per block once timestamp passes `type(uint32).max` | 1 |
| [L&#x2011;03] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 5 |
| [L&#x2011;04] | `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | 3 |

Total: 10 instances over 4 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Inconsistent constant naming pattern | 1 |
| [N&#x2011;02] | Return values of `approve()` not checked | 2 |
| [N&#x2011;03] | The `nonReentrant` `modifier` should occur before all other modifiers | 4 |
| [N&#x2011;04] | Adding a `return` statement when the function defines a named return variable, is redundant | 1 |
| [N&#x2011;05] | `constant`s should be defined rather than using magic numbers | 16 |
| [N&#x2011;06] | Numeric values having to do with time should use time units for readability | 1 |
| [N&#x2011;07] | Lines are too long | 6 |
| [N&#x2011;08] | Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables | 15 |
| [N&#x2011;09] | Non-library/interface files should use fixed compiler versions, not floating ones | 5 |
| [N&#x2011;10] | File is missing NatSpec | 1 |
| [N&#x2011;11] | NatSpec is incomplete | 7 |
| [N&#x2011;12] | Event is missing `indexed` fields | 18 |
| [N&#x2011;13] | Not using the named return variables anywhere in the function is confusing | 6 |
| [N&#x2011;14] | Avoid the use of sensitive terms | 1 |

Total: 84 instances over 14 issues



## Low Risk Issues

### [L&#x2011;01]  Allow-list not required if `maxLTV` is zero
According to `_isSolvent()`, if `maxLTV` is zero, the solvency check will always pass. The documentation states `When making under-collateralized loans, lenders know and trust the counter-party` so the check below should include a check for zero

*There is 1 instance of this issue:*
```solidity
File: /src/contracts/FraxlendPairCore.sol

196              if (_maxLTV >= LTV_PRECISION && !_isBorrowerWhitelistActive) revert BorrowerWhitelistRequired();
197:             maxLTV = _maxLTV;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L196-L197

### [L&#x2011;02]  Exchange data not fetched once per block once timestamp passes `type(uint32).max`
The comments say that the exchange data is only fetched once per block. Elsewhere timestamps are cast to `uint64`, but here they're cast to `uint32` so it's inconsistent, and one will wrap before the other. Once the `_exchangeRateInfo.lastTimestamp` passes `type(uint32).max`, the `block.timestamp` will never match the stored value, and exchange rate data will be looked up any time the function is called.

*There is 1 instance of this issue:*
```solidity
File: /src/contracts/FraxlendPairCore.sol

544:         _exchangeRateInfo.lastTimestamp = uint32(block.timestamp);

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L544

### [L&#x2011;03]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There are 5 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairDeployer.sol

104:          CIRCUIT_BREAKER_ADDRESS = _circuitBreaker;

105:          COMPTROLLER_ADDRESS = _comptroller;

106:          TIME_LOCK_ADDRESS = _timelock;

107:          FRAXLEND_WHITELIST_ADDRESS = _fraxlendWhitelist;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L104

```solidity
File: src/contracts/FraxlendPair.sol

206:          TIME_LOCK_ADDRESS = _newAddress;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L206

### [L&#x2011;04]  `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).

If all arguments are strings and or bytes, `bytes.concat()` should be used instead

*There are 3 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairDeployer.sol

204:              bytes32 salt = keccak256(abi.encodePacked(_saltSeed, _configData));

329:              keccak256(abi.encodePacked("public")),

372:              keccak256(abi.encodePacked(_name)),

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L204

## Non-critical Issues

### [N&#x2011;01]  Inconsistent constant naming pattern
In the other files `UTIL_PREC` is specified as it is here, so that seems to be the pattern. However, the other variables type out the full word. They should all be made to be consistent

*There is 1 instance of this issue:*
```solidity
File: /src/contracts/FraxlendPairConstants.sol

34       uint256 internal constant LTV_PRECISION = 1e5; // 5 decimals
35       uint256 internal constant LIQ_PRECISION = 1e5;
36       uint256 internal constant UTIL_PREC = 1e5;
37       uint256 internal constant FEE_PRECISION = 1e5;
38:      uint256 internal constant EXCHANGE_PRECISION = 1e18;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairConstants.sol#L34-L38

### [N&#x2011;02]  Return values of `approve()` not checked
Not all `IERC20` implementations `revert()` when there's a failure in `approve()`. The function signature has a `boolean` return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything

*There are 2 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

1103:         _assetContract.approve(_swapperAddress, _borrowAmount);

1184:         _collateralContract.approve(_swapperAddress, _collateralToSwap);

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1103

### [N&#x2011;03]  The `nonReentrant` `modifier` should occur before all other modifiers
This is a best-practice to protect against reentrancy in other modifiers

*There are 4 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

747:          nonReentrant

914:          nonReentrant

954:      ) external whenNotPaused nonReentrant approvedLender(msg.sender) returns (uint256 _collateralForLiquidator) {

1071:         nonReentrant

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L747

### [N&#x2011;04]  Adding a `return` statement when the function defines a named return variable, is redundant

*There is 1 instance of this issue:*
```solidity
File: src/contracts/FraxlendPairDeployer.sol

233:          return _pairAddress;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L233

### [N&#x2011;05]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 16 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

/// @audit 9000
194:              dirtyLiquidationFee = (_liquidationFee * 9000) / LIQ_PRECISION; // 90% of clean fee

/// @audit 1e18
468:              _interestEarned = (_deltaTime * _totalBorrow.amount * _currentRateInfo.ratePerSec) / 1e18;

/// @audit 1e36
522:          uint256 _price = uint256(1e36);

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L194

```solidity
File: src/contracts/FraxlendPairDeployer.sol

/// @audit 13000
171:          bytes memory _firstHalf = BytesLib.slice(_creationCode, 0, 13000);

/// @audit 13000
173:          if (_creationCode.length > 13000) {

/// @audit 13000
/// @audit 13000
174:              bytes memory _secondHalf = BytesLib.slice(_creationCode, 13000, _creationCode.length - 13000);

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L171

```solidity
File: src/contracts/FraxlendPair.sol

/// @audit 1e18
105:          _assetsPerUnitShare = totalAsset.toAmount(1e18, false);

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L105

```solidity
File: src/contracts/libraries/SafeERC20.sol

/// @audit 64
19:           if (data.length >= 64) {

/// @audit 32
21:           } else if (data.length == 32) {

/// @audit 32
23:               while (i < 32 && data[i] != 0) {

/// @audit 32
27:               for (i = 0; i < 32 && data[i] != 0; i++) {

/// @audit 32
/// @audit 18
57:           return success && data.length == 32 ? abi.decode(data, (uint8)) : 18;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L19

```solidity
File: src/contracts/VariableInterestRate.sol

/// @audit 1e18
69:               uint256 _deltaUtilization = ((MIN_UTIL - _utilization) * 1e18) / MIN_UTIL;

/// @audit 1e18
76:               uint256 _deltaUtilization = ((_utilization - MAX_UTIL) * 1e18) / (UTIL_PREC - MAX_UTIL);

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L69

### [N&#x2011;06]  Numeric values having to do with time should use time units for readability
There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks, and since they're defined, they should be used

*There is 1 instance of this issue:*
```solidity
File: src/contracts/VariableInterestRate.sol

/// @audit 43200e36
42:       uint256 private constant INT_HALF_LIFE = 43200e36; // given in seconds, equal to 12 hours, additional 1e36 to make math simpler

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L42

### [N&#x2011;07]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There are 6 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

144:      /// @param _configData abi.encode(address _asset, address _collateral, address _oracleMultiply, address _oracleDivide, uint256 _oracleNormalization, address _rateContract, bytes memory _rateInitData)

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L144

```solidity
File: src/contracts/FraxlendPairDeployer.sol

184:      /// @param _configData abi.encode(address _asset, address _collateral, address _oracleMultiply, address _oracleDivide, uint256 _oracleNormalization, address _rateContract, bytes memory _rateInitData)

239:      /// @param _configData abi.encode(address _asset, address _collateral, address _oracleMultiply, address _oracleDivide, uint256 _oracleNormalization, address _rateContract, bytes memory _rateInitData)

268:      /// @param _configData abi.encode(address _asset, address _collateral, address _oracleMultiply, address _oracleDivide, uint256 _oracleNormalization, address _rateContract, bytes memory _rateInitData)

308:      /// @param _configData abi.encode(address _asset, address _collateral, address _oracleMultiply, address _oracleDivide, uint256 _oracleNormalization, address _rateContract, bytes memory _rateInitData)

348:      /// @param _configData abi.encode(address _asset, address _collateral, address _oracleMultiply, address _oracleDivide, uint256 _oracleNormalization, address _rateContract, bytes memory _rateInitData)

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L184

### [N&#x2011;08]  Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables
If the variable needs to be different based on which class it comes from, a `view`/`pure` _function_ should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).

*There are 15 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

86:       address public TIME_LOCK_ADDRESS;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L86

```solidity
File: src/contracts/FraxlendPairDeployer.sol

49:       uint256 public DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision

50:       uint256 public GLOBAL_MAX_LTV = 1e8; // 1000x (100,000%) with 1e5 precision, protects from rounding errors in LTV calc

51:       uint256 public DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision

57:       address public CIRCUIT_BREAKER_ADDRESS;

58:       address public COMPTROLLER_ADDRESS;

59:       address public TIME_LOCK_ADDRESS;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L49

```solidity
File: src/contracts/FraxlendPair.sol

160:              uint256 _LTV_PRECISION,

161:              uint256 _LIQ_PRECISION,

162:              uint256 _UTIL_PREC,

163:              uint256 _FEE_PRECISION,

164:              uint256 _EXCHANGE_PRECISION,

165:              uint64 _DEFAULT_INT,

166:              uint16 _DEFAULT_PROTOCOL_FEE,

167:              uint256 _MAX_PROTOCOL_FEE

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L160

### [N&#x2011;09]  Non-library/interface files should use fixed compiler versions, not floating ones

*There are 5 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairDeployer.sol

2:    pragma solidity ^0.8.15;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L2

```solidity
File: src/contracts/FraxlendPair.sol

2:    pragma solidity ^0.8.15;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L2

```solidity
File: src/contracts/FraxlendWhitelist.sol

2:    pragma solidity ^0.8.15;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L2

```solidity
File: src/contracts/LinearInterestRate.sol

2:    pragma solidity ^0.8.15;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L2

```solidity
File: src/contracts/VariableInterestRate.sol

2:    pragma solidity ^0.8.15;

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L2

### [N&#x2011;10]  File is missing NatSpec

*There is 1 instance of this issue:*
```solidity
File: src/contracts/FraxlendPairConstants.sol


```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairConstants.sol

### [N&#x2011;11]  NatSpec is incomplete

*There are 7 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

/// @audit Missing: '@param _immutables'
143       /// @notice The ```constructor``` function is called on deployment
144       /// @param _configData abi.encode(address _asset, address _collateral, address _oracleMultiply, address _oracleDivide, uint256 _oracleNormalization, address _rateContract, bytes memory _rateInitData)
145       /// @param _maxLTV The Maximum Loan-To-Value for a borrower to be considered solvent (1e5 precision)
146       /// @param _liquidationFee The fee paid to liquidators given as a % of the repayment (1e5 precision)
147       /// @param _maturityDate The maturityDate date of the Pair
148       /// @param _penaltyRate The interest rate after maturity date
149       /// @param _isBorrowerWhitelistActive Enables borrower whitelist
150       /// @param _isLenderWhitelistActive Enables lender whitelist
151       constructor(
152           bytes memory _configData,
153           bytes memory _immutables,
154           uint256 _maxLTV,
155           uint256 _liquidationFee,
156           uint256 _maturityDate,
157           uint256 _penaltyRate,
158           bool _isBorrowerWhitelistActive,
159:          bool _isLenderWhitelistActive

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L143-L159

```solidity
File: src/contracts/FraxlendPairDeployer.sol

/// @audit Missing: '@param _saltSeed'
/// @audit Missing: '@param _immutables'
/// @audit Missing: '@param _penaltyRate'
183       /// @notice The ```_deployFirst``` function is an internal function with deploys the pair
184       /// @param _configData abi.encode(address _asset, address _collateral, address _oracleMultiply, address _oracleDivide, uint256 _oracleNormalization, address _rateContract, bytes memory _rateInitData)
185       /// @param _maxLTV The Maximum Loan-To-Value for a borrower to be considered solvent (1e5 precision)
186       /// @param _liquidationFee The fee paid to liquidators given as a % of the repayment (1e5 precision)
187       /// @param _maturityDate The maturityDate of the Pair
188       /// @param _isBorrowerWhitelistActive Enables borrower whitelist
189       /// @param _isLenderWhitelistActive Enables lender whitelist
190       /// @return _pairAddress The address to which the Pair was deployed
191       function _deployFirst(
192           bytes32 _saltSeed,
193           bytes memory _configData,
194           bytes memory _immutables,
195           uint256 _maxLTV,
196           uint256 _liquidationFee,
197           uint256 _maturityDate,
198           uint256 _penaltyRate,
199           bool _isBorrowerWhitelistActive,
200           bool _isLenderWhitelistActive
201:      ) private returns (address _pairAddress) {

/// @audit Missing: '@param _penaltyRate'
345       /// @notice The ```deployCustom``` function allows whitelisted users to deploy custom Term Sheets for OTC debt structuring
346       /// @dev Caller must be added to FraxLedWhitelist
347       /// @param _name The name of the Pair
348       /// @param _configData abi.encode(address _asset, address _collateral, address _oracleMultiply, address _oracleDivide, uint256 _oracleNormalization, address _rateContract, bytes memory _rateInitData)
349       /// @param _maxLTV The Maximum Loan-To-Value for a borrower to be considered solvent (1e5 precision)
350       /// @param _liquidationFee The fee paid to liquidators given as a % of the repayment (1e5 precision)
351       /// @param _maturityDate The maturityDate of the Pair
352       /// @param _approvedBorrowers An array of approved borrower addresses
353       /// @param _approvedLenders An array of approved lender addresses
354       /// @return _pairAddress The address to which the Pair was deployed
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

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L183-L201

```solidity
File: src/contracts/FraxlendPair.sol

/// @audit Missing: '@return'
181       /// @param _amount Amount of borrow
182       /// @param _roundUp Whether to roundup during division
183:      function toBorrowShares(uint256 _amount, bool _roundUp) external view returns (uint256) {

/// @audit Missing: '@return'
188       /// @param _shares Shares of borrow
189       /// @param _roundUp Whether to roundup during division
190:      function toBorrowAmount(uint256 _shares, bool _roundUp) external view returns (uint256) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L181-L183

### [N&#x2011;12]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 18 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

376       event AddInterest(
377           uint256 _interestEarned,
378           uint256 _rate,
379           uint256 _deltaTime,
380           uint256 _feesAmount,
381           uint256 _feesShare
382:      );

389:      event UpdateRate(uint256 _ratePerSec, uint256 _deltaTime, uint256 _utilizationRate, uint256 _newRatePerSec);

504:      event UpdateExchangeRate(uint256 _rate);

696       event BorrowAsset(
697           address indexed _borrower,
698           address indexed _receiver,
699           uint256 _borrowAmount,
700           uint256 _sharesAdded
701:      );

760:      event AddCollateral(address indexed _sender, address indexed _borrower, uint256 _collateralAmount);

846:      event RepayAsset(address indexed _sender, address indexed _borrower, uint256 _amountToRepay, uint256 _shares);

897       event Liquidate(
898           address indexed _borrower,
899           uint256 _collateralForLiquidator,
900           uint256 _sharesToLiquidate,
901           uint256 _amountLiquidatorToRepay,
902           uint256 _sharesToAdjust,
903           uint256 _amountToAdjust
904:      );

1045      event LeveragedPosition(
1046          address indexed _borrower,
1047          address _swapperAddress,
1048          uint256 _borrowAmount,
1049          uint256 _borrowShares,
1050          uint256 _initialCollateralAmount,
1051          uint256 _amountCollateralOut
1052:     );

1143      event RepayAssetWithCollateral(
1144          address indexed _borrower,
1145          address _swapperAddress,
1146          uint256 _collateralToSwap,
1147          uint256 _amountAssetOut,
1148          uint256 _sharesRepaid
1149:     );

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L376-L382

```solidity
File: src/contracts/FraxlendPair.sol

200:      event SetTimeLock(address _oldAddress, address _newAddress);

211:      event ChangeFee(uint32 _newFee);

228:      event WithdrawFees(uint128 _shares, address _recipient, uint256 _amountToTransfer);

268:      event SetSwapper(address _swapper, bool _approval);

282:      event SetApprovedLender(address indexed _address, bool _approval);

301:      event SetApprovedBorrower(address indexed _address, bool _approval);

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L200

```solidity
File: src/contracts/FraxlendWhitelist.sol

45:       event SetOracleWhitelist(address indexed _address, bool _bool);

60:       event SetRateContractWhitelist(address indexed _address, bool _bool);

75:       event SetFraxlendDeployerWhitelist(address indexed _address, bool _bool);

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L45

### [N&#x2011;13]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 6 instances of this issue:*
```solidity
File: src/contracts/FraxlendPairCore.sol

/// @audit _interestEarned
/// @audit _feesAmount
/// @audit _feesShare
/// @audit _newRate
393       function addInterest()
394           external
395           nonReentrant
396           returns (
397               uint256 _interestEarned,
398               uint256 _feesAmount,
399               uint256 _feesShare,
400:              uint64 _newRate

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L393-L400

```solidity
File: src/contracts/LinearInterestRate.sol

/// @audit _calldata
46:       function getConstants() external pure returns (bytes memory _calldata) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L46

```solidity
File: src/contracts/VariableInterestRate.sol

/// @audit _calldata
52:       function getConstants() external pure returns (bytes memory _calldata) {

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L52

### [N&#x2011;14]  Avoid the use of sensitive terms
Use [alternative variants](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/), e.g. allowlist/denylist instead of whitelist/blacklist

*There is 1 instance of this issue:*
```solidity
File: /src/contracts/FraxlendPair.sol

270:     /// @notice The ```setSwapper``` function is called to black or whitelist a given swapper address

```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L270





