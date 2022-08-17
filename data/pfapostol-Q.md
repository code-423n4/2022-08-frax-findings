##### Summary

Low Risk Issues
||Issue| Instances|
|:---:|:---|:---:|
|**1**|Missing checks for `address(0x0)` when assigning values to address state variables|9|

Non-critical Issues
||Issue| Instances|
|:---:|:---|:---:|
|**1**|`approve` return values not checked|2|
|**2**|Double check for overflow|3|
|**3**|`constants` should be defined rather than using magic numbers|7|
|**4**|`public` functions not called by the contract should be declared `external` instead|5|
|**5**|Unused Parameter|1|
|**6**|Unused Named Return|1|
|**7**|Function parameter missing from NatSpec|1|
|**8**|Redundant type cast `uint256`|1|
|**9**|Two contracts the similar names|1|
|**10**|Non-library/interface files should use fixed compiler versions, not floating ones|7|

Total: 38 instances over 11 issues

---

Low Risk:

1. **Missing checks for address(0x0) when assigning values to address state variables (9 instances)**

   Zero-address checks are a best practice for input validation of critical address parameters. Accidental use of zero-addresses may result in exceptions, burn fees/tokens.

   - src/contracts/FraxlendPairDeployer.sol:[104-107](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L104-L107)

   ```soldity
   104     CIRCUIT_BREAKER_ADDRESS = _circuitBreaker;
   105     COMPTROLLER_ADDRESS = _comptroller;
   106     TIME_LOCK_ADDRESS = _timelock;
   107     FRAXLEND_WHITELIST_ADDRESS = _fraxlendWhitelist;
   ```

   - src/contracts/FraxlendPairCore.sol:[172-175](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L172-L175)

   ```solidity
   172     CIRCUIT_BREAKER_ADDRESS = _circuitBreaker;
   173     COMPTROLLER_ADDRESS = _comptrollerAddress;
   174     TIME_LOCK_ADDRESS = _timeLockAddress;
   175     FRAXLEND_WHITELIST_ADDRESS = _fraxlendWhitelistAddress;
   ```

   - src/contracts/FraxlendPair.sol:[206](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L206)

   ```solidity
   206     TIME_LOCK_ADDRESS = _newAddress;
   ```

Non-Critical:

1. **`approve` return values not checked (2 instances)**

   I chose not critical as the transaction will fail if the approval is invalid. But it can be considered as low, since the user will spend significantly more when calling an external function than if it were reverted immediately.

   - contracts/contracts/FraxlendPairCore.sol:[1103](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1103),[1184](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1184)

   ```solidity
   1103    _assetContract.approve(_swapperAddress, _borrowAmount);
   ...
   1184    _collateralContract.approve(_swapperAddress, _collateralToSwap);
   ```

   fix:

   ```solidity
   if (!_assetContract.approve(_swapperAddress, _borrowAmount)) revert NotApproved();
   ```

   OR: use `safeApprove` (just change function, because `_assetContract` and `_collateralContract` is already using `SafeERC20`)

2. **Double check for overflow (3 instances)**

   [Arithmetic operations revert](https://docs.soliditylang.org/en/v0.8.13/080-breaking-changes.html) on underflow and overflow. You can use unchecked { ... } to use the previous wrapping behaviour.

   - contracts/contracts/FraxlendPairCore.sol:[471-476](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L471-L476),[542-543](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L542-L543),[633](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L633)

   ```solidity
   471     if (
   472         _interestEarned + _totalBorrow.amount <= type(uint128).max &&
   473         _interestEarned + _totalAsset.amount <= type(uint128).max
   474     ) {
   475         _totalBorrow.amount += uint128(_interestEarned);
   476         _totalAsset.amount += uint128(_interestEarned);
   ...
   542     if (_exchangeRate > type(uint224).max) revert PriceTooLarge();
   543     _exchangeRateInfo.exchangeRate = uint224(_exchangeRate);
   ...
   633     if (allowed != type(uint256).max) _approve(_owner, msg.sender, allowed - _shares);
   ```

   fix:

   1. Use unchecked if you check code, or
   2. remove overflow check

   Both fixes save gas.

3. **`constants` should be defined rather than using magic numbers (7 instances)**

   - contracts/contracts/FraxlendPairCore.sol:[468](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L468),[522](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L522)

   ```solidity
   468     _interestEarned = (_deltaTime * _totalBorrow.amount * _currentRateInfo.ratePerSec) / 1e18;
   ...
   522     uint256 _price = uint256(1e36);
   ```

   - contracts/contracts/VariableInterestRate.sol:[69](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L69),[76](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L76)

   ```solidity
   69      uint256 _deltaUtilization = ((MIN_UTIL - _utilization) * 1e18) / MIN_UTIL;
   ...
   76      uint256 _deltaUtilization = ((_utilization - MAX_UTIL) * 1e18) / (UTIL_PREC - MAX_UTIL);
   ```

   - contracts/contracts/FraxlendPairDeployer.sol:[171](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L171),[173-174](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L173-L174)

   ```solidity
   171     bytes memory _firstHalf = BytesLib.slice(_creationCode, 0, 13000);
   ...
   173     if (_creationCode.length > 13000) {
   174         bytes memory _secondHalf = BytesLib.slice(_creationCode, 13000, _creationCode.length - 13000);
   ```

4. **`public` functions not called by the contract should be declared `external` instead (5 instances)**

   Contracts are allowed to override their parents' functions and change the visibility from `external` to `public`.

   - contracts/contracts/FraxlendPair.sol:[74](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L74),[78](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L78),[84](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L84),[89](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L89),[100](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L100),

   ```solidity
   74      function name() public view override(ERC20, IERC20Metadata) returns (string memory) {
   ...
   78      function symbol() public view override(ERC20, IERC20Metadata) returns (string memory) {
   ...
   84      function decimals() public pure override(ERC20, IERC20Metadata) returns (uint8) {
   ...
   89      function totalSupply() public view override(ERC20, IERC20) returns (uint256) {
   ...
   100     function totalAssets() public view virtual returns (uint256) {
   ```

5. **Unused Parameter (1 instances)**

   Removing unused named return variables can reduce gas usage and improve code clarity.

   - src/contracts/VariableInterestRate.sol:[63](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L63)

   ```solidity
   63      function getNewRate(bytes calldata _data, bytes calldata _initData)
   ```

6. **Unused Named Return (1 instances)**

   return is redundant when using named returns

   - src/contracts/FraxlendPairCore.sol:[519](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L519)

   ```solidity
   519     return _exchangeRate = _exchangeRateInfo.exchangeRate;
   ```

7. **Function parameter missing from NatSpec (1 instances)**

   Makes it difficult for the user to understand the parameter

   - src/contracts/FraxlendPairCore.sol:[153](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L153)

   ```solidity
   153     bytes memory _immutables,
   ```

8. **Redundant type cast uint256 (1 instances)**

   - src/contracts/FraxlendPairCore.sol:[522](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L522)

   ```solidity
   522     uint256 _price = uint256(1e36);
   ```

9. **Two contracts the similar names (1 instances)**

   Detected with: slither - https://github.com/crytic/slither/wiki/Detector-Documentation#name-reused

   If a codebase has two contracts the similar names, the compilation artifacts will not contain one of the contracts with the duplicate name.

   SafeERC20 is re-used:

   - SafeERC20 (node_modules/@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol#19-116)
   - SafeERC20 (contracts/contracts/libraries/SafeERC20.sol#13-76)

10. **Non-library/interface files should use fixed compiler versions, not floating ones (7 instances)**

    - 2022-08-frax/src/contracts/FraxlendPair.sol
    - 2022-08-frax/src/contracts/FraxlendPairConstants.sol
    - 2022-08-frax/src/contracts/FraxlendPairCore.sol
    - 2022-08-frax/src/contracts/FraxlendPairDeployer.sol
    - 2022-08-frax/src/contracts/FraxlendWhitelist.sol
    - 2022-08-frax/src/contracts/LinearInterestRate.sol
    - 2022-08-frax/src/contracts/VariableInterestRate.sol

      ```solidity
         pragma solidity ^0.8.15;
      ```
