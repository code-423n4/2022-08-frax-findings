**Overview**
Risk Rating | Number of issues
--- | ---
Low Risk | 8
Non-Critical Risk | 6

**Table of Contents**

- [1. Low Risk Issues](#1-low-risk-issues)
  - [1.1. Known Issue: Chainlink's `latestRoundData` might return stale or incorrect results](#11-known-issue-chainlinks-latestrounddata-might-return-stale-or-incorrect-results)
  - [1.2. Known vulnerabilities exist in currently used `@openzeppelin/contracts` version](#12-known-vulnerabilities-exist-in-currently-used-openzeppelincontracts-version)
  - [1.3. Unsafe casting may overflow](#13-unsafe-casting-may-overflow)
  - [1.4. Deprecated approve() function](#14-deprecated-approve-function)
  - [1.5. Missing address(0) checks](#15-missing-address0-checks)
  - [1.6. `deployedPairsArray` is an unbounded array, this could lead to DoS](#16-deployedpairsarray-is-an-unbounded-array-this-could-lead-to-dos)
  - [1.7. Prevent accidentally burning tokens](#17-prevent-accidentally-burning-tokens)
  - [1.8. Use a 2-step ownership transfer pattern](#18-use-a-2-step-ownership-transfer-pattern)
- [2. Non-Critical Issues](#2-non-critical-issues)
  - [2.1. The `nonReentrant` `modifier` should occur before all other modifiers](#21-the-nonreentrant-modifier-should-occur-before-all-other-modifiers)
  - [2.2. Typos](#22-typos)
  - [2.3. Related mappings should be grouped in a struct](#23-related-mappings-should-be-grouped-in-a-struct)
  - [2.4. Use `string.concat()` or `bytes.concat()`](#24-use-stringconcat-or-bytesconcat)
  - [2.5. Adding a `return` statement when the function defines a named return variable, is redundant](#25-adding-a-return-statement-when-the-function-defines-a-named-return-variable-is-redundant)
  - [2.6. Non-library/interface files should use fixed compiler versions, not floating ones](#26-non-libraryinterface-files-should-use-fixed-compiler-versions-not-floating-ones)

# 1. Low Risk Issues

## 1.1. Known Issue: Chainlink's `latestRoundData` might return stale or incorrect results

Even as a [known issue](https://github.com/code-423n4/2022-08-frax#known-issues), the mitigation still has its place in a QA Report. 

`latestRoundData()` is used to fetch the asset price from a Chainlink aggregator, but it's missing additional validations to ensure that the round is complete. If there is a problem with Chainlink starting a new round and finding consensus on the new value for the oracle (e.g. Chainlink nodes abandon the oracle, chain congestion, vulnerability/attacks on the Chainlink system) consumers of this contract may continue using outdated stale data / stale prices.

Consider adding missing checks for stale data here:

```diff
File: FraxlendPairCore.sol
523:         if (oracleMultiply != address(0)) {
- 524:             (, int256 _answer, , , ) = AggregatorV3Interface(oracleMultiply).latestRoundData();
+ 524:             (uint80 roundID, int256 _answer, , uint256 updatedAt, uint80 answeredInRound) = AggregatorV3Interface(oracleMultiply).latestRoundData();
+ 524:             require(updatedAt > 0, "Price data is stale");
+ 524:             require(answeredInRound >= roundID, "Price data is stale");
525:             if (_answer <= 0) {
526:                 revert OracleLTEZero(oracleMultiply);
527:             }
528:             _price = _price * uint256(_answer);
529:         }
530: 
531:         if (oracleDivide != address(0)) {
- 532:             (, int256 _answer, , , ) = AggregatorV3Interface(oracleDivide).latestRoundData();
+ 532:             (uint80 roundID, int256 _answer, , uint256 updatedAt, uint80 answeredInRound) = AggregatorV3Interface(oracleDivide).latestRoundData();
+ 532:             require(updatedAt > 0, "Price data is stale");
+ 532:             require(answeredInRound >= roundID, "Price data is stale");
533:             if (_answer <= 0) {
534:                 revert OracleLTEZero(oracleDivide);
535:             }
536:             _price = _price / uint256(_answer);
537:         }
```

## 1.2. Known vulnerabilities exist in currently used `@openzeppelin/contracts` version

As some [known vulnerabilities](https://snyk.io/test/npm/@openzeppelin/contracts/4.7.2) exist in the current `@openzeppelin/contracts` version, consider updating `package.json` with at least `@openzeppelin/contracts@4.7.3` here:

<https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/package.json#L23>

```json
    "@openzeppelin/contracts": "^4.7.2",
```

While vulnerabilities are known, the current scope isn't affected (this might not hold true for the whole solution)

## 1.3. Unsafe casting may overflow

SafeMath and Solidity 0.8.* handles overflows for basic math operations but not for casting.

```solidity
FraxlendPair.sol:252:        _totalAsset.amount -= uint128(_amountToTransfer);
FraxlendPairCore.sol:475:                _totalBorrow.amount += uint128(_interestEarned);
FraxlendPairCore.sol:476:                _totalAsset.amount += uint128(_interestEarned);
FraxlendPairCore.sol:484:                    _totalAsset.shares += uint128(_feesShare);
FraxlendPairCore.sol:543:        _exchangeRateInfo.exchangeRate = uint224(_exchangeRate);
FraxlendPairCore.sol:544:        _exchangeRateInfo.lastTimestamp = uint32(block.timestamp);
FraxlendPairCore.sol:719:        _totalBorrow.shares += uint128(_sharesAdded);
LinearInterestRate.sol:85:            _newRatePerSec = uint64(_minInterest + ((_utilization * _slope) / UTIL_PREC));
LinearInterestRate.sol:90:            _newRatePerSec = uint64(_vertexInterest);
VariableInterestRate.sol:71:            _newRatePerSec = uint64((_currentRatePerSec * INT_HALF_LIFE) / _decayGrowth);
VariableInterestRate.sol:78:            _newRatePerSec = uint64((_currentRatePerSec * _decayGrowth) / INT_HALF_LIFE);
```

Consider casting like it's done here:

```solidity
FraxlendPairCore.sol:613:        _deposit(_totalAsset, _amountReceived.toUint128(), _shares.toUint128(), _receiver);
FraxlendPairCore.sol:668:        _redeem(_totalAsset, _amountToReturn.toUint128(), _shares.toUint128(), _receiver, _owner);
FraxlendPairCore.sol:684:        _redeem(_totalAsset, _amount.toUint128(), _shares.toUint128(), _receiver, _owner);
FraxlendPairCore.sol:757:        _shares = _borrowAsset(_borrowAmount.toUint128(), _receiver);
FraxlendPairCore.sol:886:        _repayAsset(_totalBorrow, _amountToRepay.toUint128(), _shares.toUint128(), msg.sender, _borrower);
FraxlendPairCore.sol:937:        _repayAsset(_totalBorrow, _amountToRepay.toUint128(), _shares.toUint128(), msg.sender, _borrower); // liquidator repays shares on behalf of borrower
FraxlendPairCore.sol:967:        uint128 _borrowerShares = userBorrowShares[_borrower].toUint128();
FraxlendPairCore.sol:984:            _leftoverCollateral = (_userCollateralBalance.toInt256() - _optimisticCollateralForLiquidator.toInt256());
FraxlendPairCore.sol:993:        uint128 _amountLiquidatorToRepay = (_totalBorrow.toAmount(_sharesToLiquidate, true)).toUint128();
FraxlendPairCore.sol:1005:                    _amountToAdjust = (_totalBorrow.toAmount(_sharesToAdjust, false)).toUint128();
FraxlendPairCore.sol:1100:        uint256 _borrowShares = _borrowAsset(_borrowAmount.toUint128(), address(this));
FraxlendPairCore.sol:1209:        _repayAsset(_totalBorrow, _amountAssetOut.toUint128(), _sharesToRepay.toUint128(), address(this), msg.sender);
```

## 1.4. Deprecated approve() function

While `safeApprove()` in itself is deprecated, it is still better than `approve` which is subject to a known front-running attack and failing for certain token implementations that do not return a boolean value. Consider using `safeApprove` instead (or better: `safeIncreaseAllowance()`/`safeDecreaseAllowance()`):

```solidity
FraxlendPairCore.sol:1103:        _assetContract.approve(_swapperAddress, _borrowAmount);
FraxlendPairCore.sol:1184:        _collateralContract.approve(_swapperAddress, _collateralToSwap);
```

## 1.5. Missing address(0) checks

Consider adding an `address(0)` check for immutable variables:

```solidity
FraxlendPairCore.sol:58:    IERC20 internal immutable assetContract;
FraxlendPairCore.sol:59:    IERC20 public immutable collateralContract;
FraxlendPairCore.sol:62:    address public immutable oracleMultiply;
FraxlendPairCore.sol:63:    address public immutable oracleDivide;
FraxlendPairCore.sol:74:    IRateCalculator public immutable rateContract; // For complex rate calculations
FraxlendPairCore.sol:81:    address public immutable DEPLOYER_ADDRESS;
FraxlendPairCore.sol:84:    address public immutable CIRCUIT_BREAKER_ADDRESS;
FraxlendPairCore.sol:85:    address public immutable COMPTROLLER_ADDRESS;
FraxlendPairCore.sol:89:    address public immutable FRAXLEND_WHITELIST_ADDRESS;
FraxlendPairDeployer.sol:62:    address private immutable FRAXLEND_WHITELIST_ADDRESS;
```

## 1.6. `deployedPairsArray` is an unbounded array, this could lead to DoS

As `deployedPairsArray` can grow quite large (only `push` operations, no `pop`, no `delete`, no subtraction on `<array>.length`), the transaction's gas cost could exceed the block gas limit and make it impossible to call the impacted functions at all.

```solidity
FraxlendPairDeployer.sol:127:        for (i = 0; i < _lengthOfArray; ) {
FraxlendPairDeployer.sol:152:        for (i = 0; i < _lengthOfArray; ) {
FraxlendPairDeployer.sol:255:        deployedPairsArray.push(_name);
FraxlendPairDeployer.sol:402:        for (uint256 i = 0; i < _lengthOfArray; ) {
```

Consider introducing a reasonable upper limit based on block gas limits and adding a method to remove elements in the array.

## 1.7. Prevent accidentally burning tokens

Transferring tokens to the zero address is usually prohibited to accidentally avoid "burning" tokens by sending them to an unrecoverable zero address.

Consider adding a check to prevent accidentally burning tokens here:

```solidity
FraxlendPair.sol:261:        assetContract.safeTransfer(_recipient, _amountToTransfer);
FraxlendPairCore.sol:651:        assetContract.safeTransfer(_receiver, _amountToReturn);
FraxlendPairCore.sol:728:            assetContract.safeTransfer(_receiver, _borrowAmount);
FraxlendPairCore.sol:777:            collateralContract.safeTransferFrom(_sender, address(this), _collateralAmount);
FraxlendPairCore.sol:819:            collateralContract.safeTransfer(_receiver, _collateralAmount);
FraxlendPairCore.sol:872:            assetContract.safeTransferFrom(_payer, address(this), _amountToRepay);
```

## 1.8. Use a 2-step ownership transfer pattern

Contracts inheriting from OpenZeppelin's libraries have the default `transferOwnership()` function (a one-step process). It's possible that the `onlyOwner` role mistakenly transfers ownership to a wrong address, resulting in a loss of the `onlyOwner` role.
Consider overriding the default `transferOwnership()` function to first nominate an address as the `pendingOwner` and implementing an `acceptOwnership()` function which is called by the `pendingOwner` to confirm the transfer.

```solidity
FraxlendPairDeployer.sol:29:import "@openzeppelin/contracts/access/Ownable.sol";
FraxlendPairDeployer.sol:44:contract FraxlendPairDeployer is Ownable {
FraxlendPairDeployer.sol:262:        _fraxlendPair.transferOwnership(COMPTROLLER_ADDRESS);
```

```solidity
FraxlendWhitelist.sol:28:import "@openzeppelin/contracts/access/Ownable.sol";
FraxlendWhitelist.sol:30:contract FraxlendWhitelist is Ownable {
```

# 2. Non-Critical Issues

## 2.1. The `nonReentrant` `modifier` should occur before all other modifiers

This is a best-practice to protect against re-entrancy in other modifiers

- src/contracts/FraxlendPairCore.sol:

```solidity
   739      function borrowAsset(
   740          uint256 _borrowAmount,
   741          uint256 _collateralAmount,
   742          address _receiver
   743      )
   744          external
   745          isNotPastMaturity
   746          whenNotPaused
   747:         nonReentrant
   748          isSolvent(msg.sender)
   749          approvedBorrower
   750          returns (uint256 _shares)
```

```solidity
   911      function liquidate(uint256 _shares, address _borrower)
   912          external
   913          whenNotPaused
   914:         nonReentrant
   915          approvedLender(msg.sender)
   916          returns (uint256 _collateralForLiquidator)
```

```solidity
   950      function liquidateClean(
   951          uint128 _sharesToLiquidate,
   952          uint256 _deadline,
   953          address _borrower
   954:     ) external whenNotPaused nonReentrant approvedLender(msg.sender) returns (uint256 _collateralForLiquidator) {
```

```solidity
  1062      function leveragedPosition(
  1063          address _swapperAddress,
  1064          uint256 _borrowAmount,
  1065          uint256 _initialCollateralAmount,
  1066          uint256 _amountCollateralOutMin,
  1067          address[] memory _path
  1068      )
  1069          external
  1070          isNotPastMaturity
  1071:         nonReentrant
  1072          whenNotPaused
  1073          approvedBorrower
  1074          isSolvent(msg.sender)
  1075          returns (uint256 _totalCollateralBalance)

```

## 2.2. Typos

- toBorrtoBorrowAmountowShares

```solidity
FraxlendPair.sol:187:    /// @notice The ```toBorrtoBorrowAmountowShares``` function converts a given amount of borrow debt into the number of shares
```

- whos

```solidity
FraxlendPair.sol:286:    /// @param _lenders The addresses whos status will be set
FraxlendPair.sol:305:    /// @param _borrowers The addresses whos status will be set
```

- approcal

```solidity
FraxlendPair.sol:287:    /// @param _approval The approcal status
FraxlendPair.sol:306:    /// @param _approval The approcal status
```

- whitlist

```solidity
FraxlendPairCore.sol:231:        // Set approved lenders whitlist active
```

- occured

```solidity
FraxlendPairCore.sol:893:    /// @param _borrower The borrower account for which the liquidation occured
```

- liabilites

```solidity
FraxlendPairCore.sol:896:    /// @param _sharesToAdjust The number of Borrow Shares that were adjusted on liabilites and assets (a writeoff)
```

- Calced

```solidity
FraxlendPairCore.sol:992:        // Calced here for use during repayment, grouped with other calcs before effects start
```

- stuct

```solidity
FraxlendPairCore.sol:1010:                    // Note: Ensure this memory stuct will be passed to _repayAsset for write to state
```

- accomodate

```solidity
FraxlendPairDeployer.sol:168:    /// @dev splits the data if necessary to accomodate creation code that is slightly larger than 24kb
```

- unopinionated

```solidity
LinearInterestRate.sol:72:    /// @dev We use calldata to remain unopinionated about future implementations
```

- calulcating

```solidity
VariableInterestRate.sol:32:/// @notice A Contract for calulcating interest rates as a function of utilization and time
```

## 2.3. Related mappings should be grouped in a struct

The "Given address in the DA" `mapping`s should be grouped in a struct.

```solidity
FraxlendPairCore.sol:127:    mapping(address => uint256) public userCollateralBalance; // amount of collateral each user is backed
FraxlendPairCore.sol:129:    mapping(address => uint256) public userBorrowShares; // represents the shares held by individuals
```

It would be less error-prone, more readable, and it would be possible to delete all related fields with a simple `delete userInfo[address]`.

## 2.4. Use `string.concat()` or `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)
Solidity version 0.8.12 introduces `string.concat()` (vs `abi.encodePacked(<str>,<str>)`)

```solidity
FraxlendPair.sol:2:pragma solidity ^0.8.15;
FraxlendPair.sol:81:        return string(abi.encodePacked("FraxlendV1 - ", collateralContract.safeSymbol(), "/", assetContract.safeSymbol()));
```

```solidity
FraxlendPairDeployer.sol:2:pragma solidity ^0.8.15;
FraxlendPairDeployer.sol:204:            bytes32 salt = keccak256(abi.encodePacked(_saltSeed, _configData));
FraxlendPairDeployer.sol:211:             bytes memory bytecode = abi.encodePacked(
FraxlendPairDeployer.sol:212:                 _creationCode,
FraxlendPairDeployer.sol:213:                 abi.encode(
FraxlendPairDeployer.sol:214:                     _configData,
FraxlendPairDeployer.sol:215:                     _immutables,
FraxlendPairDeployer.sol:216:                     _maxLTV,
FraxlendPairDeployer.sol:217:                     _liquidationFee,
FraxlendPairDeployer.sol:218:                     _maturityDate,
FraxlendPairDeployer.sol:219:                     _penaltyRate,
FraxlendPairDeployer.sol:220:                     _isBorrowerWhitelistActive,
FraxlendPairDeployer.sol:221:                     _isLenderWhitelistActive
FraxlendPairDeployer.sol:222:                 )
FraxlendPairDeployer.sol:223:             );
```

## 2.5. Adding a `return` statement when the function defines a named return variable, is redundant

While not consuming more gas with the Optimizer enabled: using both named returns and a return statement isn't necessary. Removing one of those can improve code clarity.

Affected code:

- File: LinearInterestRate.sol

```solidity
46:     function getConstants() external pure returns (bytes memory _calldata) {
47:         return abi.encode(MIN_INT, MAX_INT, MAX_VERTEX_UTIL, UTIL_PREC);
48:     }
```

- File: VariableInterestRate.sol

```solidity
52:     function getConstants() external pure returns (bytes memory _calldata) {
53:         return abi.encode(MIN_UTIL, MAX_UTIL, UTIL_PREC, MIN_INT, MAX_INT, INT_HALF_LIFE);
54:     }
```

## 2.6. Non-library/interface files should use fixed compiler versions, not floating ones

```solidity
FraxlendPair.sol:2:pragma solidity ^0.8.15;
FraxlendPairConstants.sol:2:pragma solidity ^0.8.15;
FraxlendPairCore.sol:2:pragma solidity ^0.8.15;
FraxlendPairDeployer.sol:2:pragma solidity ^0.8.15;
FraxlendWhitelist.sol:2:pragma solidity ^0.8.15;
LinearInterestRate.sol:2:pragma solidity ^0.8.15;
VariableInterestRate.sol:2:pragma solidity ^0.8.15;
```
