# [G-01] `x = x + y` is cheaper than `x += y`:-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 252):

   `_totalAsset.amount -= uint128(_amountToTransfer);`       

2. File: fraxlend/src/contracts/FraxlendPair.sol (line 253):

   `_totalAsset.shares -= _shares;`

3. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 643-644):

   `        _totalAsset.amount -= _amountToReturn;
        _totalAsset.shares -= _shares;`

4. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 813-815):

   `        userCollateralBalance[_borrower] -= _collateralAmount;
        // Following line will revert on underflow if totalCollateral < _collateralAmount
        totalCollateral -= _collateralAmount;`

5. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 863-867):

   `        _totalBorrow.amount -= _amountToRepay;
        _totalBorrow.shares -= _shares;

        // Effects: write to state
        userBorrowShares[_borrower] -= _shares;`

6. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 1008-1012):

   `                    totalAsset.amount -= _amountToAdjust;

                    // Note: Ensure this memory stuct will be passed to _repayAsset for write to state
                    _totalBorrow.amount -= _amountToAdjust;
                    _totalBorrow.shares -= _sharesToAdjust;`        

7. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 475-476):

   `                _totalBorrow.amount += uint128(_interestEarned);
                _totalAsset.amount += uint128(_interestEarned);`       

8. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 484):

   `_totalAsset.shares += uint128(_feesShare);`

9. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 566-567):

   `        _totalAsset.amount += _amount;
        _totalAsset.shares += _shares;`

10. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 718-719):

   `        _totalBorrow.amount += _borrowAmount;
        _totalBorrow.shares += uint128(_sharesAdded);`

11. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 724):

   `userBorrowShares[msg.sender] += _sharesAdded;`

12. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 772-773):

   `        userCollateralBalance[_borrower] += _collateralAmount;
        totalCollateral += _collateralAmount;`                    




    

  




# [G-02] `<array>.length` should not be looked up in every loop of a `for` loop (Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.):-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 289):

   `for (uint256 i = 0; i < _lenders.length; i++) {`       

2. File: fraxlend/src/contracts/FraxlendPair.sol (line 308):

   `for (uint256 i = 0; i < _borrowers.length; i++) {`

3. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 265):

   `for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {`

4. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 270):

   `for (uint256 i = 0; i < _approvedLenders.length; ++i) {`

5. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 51):

   `for (uint256 i = 0; i < _addresses.length; i++) {

6. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 66):

   ` for (uint256 i = 0; i < _addresses.length; i++) {`        

7. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 81):

   `for (uint256 i = 0; i < _addresses.length; i++) {`       











# [G-03] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops:-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 289):

   `for (uint256 i = 0; i < _lenders.length; i++) {`       

2. File: fraxlend/src/contracts/FraxlendPair.sol (line 308):

   `for (uint256 i = 0; i < _borrowers.length; i++) {`

3. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 265):

   `for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {`

4. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 270):

   `for (uint256 i = 0; i < _approvedLenders.length; ++i) {`

5. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 51):

   `for (uint256 i = 0; i < _addresses.length; i++) {

6. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 66):

   ` for (uint256 i = 0; i < _addresses.length; i++) {`        

7. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 81):

   `for (uint256 i = 0; i < _addresses.length; i++) {`       

8. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 27):

   `for (i = 0; i < 32 && data[i] != 0; i++) {`










# [G-03] `require`()/`revert()` strings longer than 32 bytes cost extra gas:-


1. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 205):

   `require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");`       

2. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 228):

   `require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");`

3. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 253):

   `require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");`

4. File: fraxlend/src/contracts/LinearInterestRate.sol (line 57-68):

   `require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
        require(
            IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
            "FraxlendPairDeployer: Only whitelisted addresses"
        );`

5. File: fraxlend/src/contracts/LinearInterestRate.sol (line 57-68):

   `        require(
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
        );'










# [G-04] Not using the named return variables when a function returns, wastes deployment gas:-


1. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 133):

   `return _addresses;`       

2. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 233):

   `return _pairAddress;`












# [G-05] Using `> 0` costs more gas than `!= 0` when used on a uint in a `require()` statement:-


1. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 65-68):

   ` require(
            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
        );`       









# [G-06] It costs more gas to initialize variables to zero than to let the default of zero be applied:-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 289):

   `for (uint256 i = 0; i < _lenders.length; i++) {`       

2. File: fraxlend/src/contracts/FraxlendPair.sol (line 308):

   `for (uint256 i = 0; i < _borrowers.length; i++) {`

3. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 265):

   `for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {`

4. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 270):

   `for (uint256 i = 0; i < _approvedLenders.length; ++i) {`

5. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 51):

   `for (uint256 i = 0; i < _addresses.length; i++) {

6. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 66):

   ` for (uint256 i = 0; i < _addresses.length; i++) {`        

7. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 81):

   `for (uint256 i = 0; i < _addresses.length; i++) {`       

8. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 27):

   `for (i = 0; i < 32 && data[i] != 0; i++) {`

9. File: fraxlend/src/contracts/FraxlendPairConstants.sol (line 47):

   `uint16 internal constant DEFAULT_PROTOCOL_FEE = 0; // 1e5 precision`

10. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 127):

   `for (i = 0; i < _lengthOfArray; ) {'

11. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 152):

   `for (i = 0; i < _lengthOfArray; ) {`        

12. File: fraxlend/src/contracts/LinearInterestRate.sol (line 33):

   `uint256 private constant MIN_INT = 0; // 0.00% annual rate`       

13. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 22):

   `uint8 i = 0;`   

14. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 27):

   `for (i = 0; i < 32 && data[i] != 0; i++) {`   



 











# [G-07] `++i` costs less gas than `i++`, especially when it’s used in forloops (--i/i-- too):-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 289):

   `for (uint256 i = 0; i < _lenders.length; i++) {`       

2. File: fraxlend/src/contracts/FraxlendPair.sol (line 308):

   `for (uint256 i = 0; i < _borrowers.length; i++) {`

3. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 265):

   `for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {`

4. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 270):

   `for (uint256 i = 0; i < _approvedLenders.length; ++i) {`

5. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 51):

   `for (uint256 i = 0; i < _addresses.length; i++) {

6. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 66):

   ` for (uint256 i = 0; i < _addresses.length; i++) {`        

7. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 81):

   `for (uint256 i = 0; i < _addresses.length; i++) {`       

8. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 27):

   `for (i = 0; i < 32 && data[i] != 0; i++) {`   

9. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 27):

   `for (i = 0; i < 32 && data[i] != 0; i++) {`      








# [G-08] Splitting `require()` statements that use `&&` Cost gas:-


1. File: fraxlend/src/contracts/LinearInterestRate.sol (line 57-68):

   `        require(
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
        );'   






   




# [G-09] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead:-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 84):

   `function decimals() public pure override(ERC20, IERC20Metadata) returns (uint8) {`       

2. File: fraxlend/src/contracts/FraxlendPair.sol (line 137):

   `return type(uint128).max;`

3. File: fraxlend/src/contracts/FraxlendPair.sol (line 141):

   `return type(uint128).max;`

4. File: fraxlend/src/contracts/FraxlendPair.sol (line 165-166):

   `            uint64 _DEFAULT_INT,
            uint16 _DEFAULT_PROTOCOL_FEE,`

5. File: fraxlend/src/contracts/FraxlendPair.sol (line 211):

   `event ChangeFee(uint32 _newFee);'

6. File: fraxlend/src/contracts/FraxlendPair.sol (line 215):

   `function changeFee(uint32 _newFee) external whenNotPaused {`        

7. File: fraxlend/src/contracts/FraxlendPair.sol (line 240):

   `if (_shares == 0) _shares = uint128(balanceOf(address(this)));`       

8. File: fraxlend/src/contracts/FraxlendPair.sol (line 252):

   `_totalAsset.amount -= uint128(_amountToTransfer);`   

9. File: fraxlend/src/contracts/FraxlendPairConstants.sol (line 41):

   `uint64 internal constant DEFAULT_INT = 158247046;`       

10. File: fraxlend/src/contracts/FraxlendPairConstants.sol (line 47):

   `uint16 internal constant DEFAULT_PROTOCOL_FEE = 0;`

11. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 106-109):

   `    uint64 lastBlock;
        uint64 feeToProtocolRate; // Fee amount 1e5 precision
        uint64 lastTimestamp;
        uint64 ratePerSec;`

12. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 116-117):

   `    uint32 lastTimestamp;
        uint224 exchangeRate`

13. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 400):

   `uint64 _newRate'

14. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 415):

   `uint64 _newRate`        

15. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 434-435):

   `            _currentRateInfo.lastTimestamp = uint64(block.timestamp);
            _currentRateInfo.lastBlock = uint64(block.number);`       

16. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 448):

   `_newRate = uint64(penaltyRate);`

17. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 464-465):

   `  _currentRateInfo.lastTimestamp = uint64(block.timestamp);
            _currentRateInfo.lastBlock = uint64(block.number);`

18. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 472-476):

   `                _interestEarned + _totalBorrow.amount <= type(uint128).max &&
                _interestEarned + _totalAsset.amount <= type(uint128).max
            ) {
                _totalBorrow.amount += uint128(_interestEarned);
                _totalAsset.amount += uint128(_interestEarned);`

19. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 484):

   `_totalAsset.shares += uint128(_feesShare);'

20. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 542-544):

   `       if (_exchangeRate > type(uint224).max) revert PriceTooLarge();
        _exchangeRateInfo.exchangeRate = uint224(_exchangeRate);
        _exchangeRateInfo.lastTimestamp = uint32(block.timestamp);`        

21. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 561-562):

   ` uint128 _amount,
        uint128 _shares,`       

22. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 625-626):

   `uint128 _amountToReturn,
        uint128 _shares,`

23. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 719):

   `_totalBorrow.shares += uint128(_sharesAdded);`

24. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 857-858):

   `        uint128 _amountToRepay,
        uint128 _shares,`

25. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 951):

   `uint128 _sharesToLiquidate,'

26. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 967):

   `uint128 _borrowerShares = userBorrowShares[_borrower].toUint128();`        

27. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 993-998):

   `        uint128 _amountLiquidatorToRepay = (_totalBorrow.toAmount(_sharesToLiquidate, true)).toUint128();

        // Determine if and how much debt to writeoff
        // Note: ensures that sharesToLiquidate is never larger than borrowerShares
        uint128 _sharesToAdjust;
        uint128 _amountToAdjust;`       

28. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 1001):

   `uint128 _leftoverBorrowShares = _borrowerShares - _sharesToLiquidate;`        

29. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 54-57):

   `        uint64 lastBlock;
        uint64 feeToProtocolRate; // Fee amount 1e5 precision
        uint64 lastTimestamp;
        uint64 ratePerSec;`       

30. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 112-115):

   `            uint128 _totalAssetAmount,
            uint128 _totalAssetShares,
            uint128 _totalBorrowAmount,
            uint128 _totalBorrowShares,`

31. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 166):

   `(uint64 lastBlock, uint64 feeToProtocolRate, uint64 lastTimestamp, uint64 ratePerSec) = _fraxlendPair`

32. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 180):

   `(uint128 _totalAssetAmount, uint128 _totalAssetShares) = _fraxlendPair.totalAsset();`

33. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 182):

   `(uint128 _totalBorrowAmount, uint128 _totalBorrowShares) = _fraxlendPair.totalBorrow();'

34. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 198):

   `_newRate = uint64(_fraxlendPair.penaltyRate());`        

35. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 232):

   `(, uint64 _feeToProtocolRate, , ) = _fraxlendPair.currentRateInfo();`       

36. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 234):

   (uint128 _totalAssetAmount, uint128 _totalAssetShares) = _fraxlendPair.totalAsset();`   

37. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 243):

   `uint128 _sharesToLiquidate`       

38. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 259):

   `(uint128 _totalBorrowAmount, uint128 _totalBorrowShares) = _fraxlendPair.totalBorrow();`

39. File: fraxlend/src/contracts/LinearInterestRate.sol (line 76):

   ` function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {`

40. File: fraxlend/src/contracts/LinearInterestRate.sol (line 85):

   `_newRatePerSec = uint64(_minInterest + ((_utilization * _slope) / UTIL_PREC));`

41. File: fraxlend/src/contracts/LinearInterestRate.sol (line 88):

   `_newRatePerSec = uint64(_vertexInterest + (((_utilization - _vertexUtilization) * _slope) / UTIL_PREC));`

42. File: fraxlend/src/contracts/LinearInterestRate.sol (line 90):

   `_newRatePerSec = uint64(_vertexInterest);'

43. File: fraxlend/src/contracts/VariableInterestRate.sol (line 35-41):

   `    uint32 private constant MIN_UTIL = 75000; // 75%
    uint32 private constant MAX_UTIL = 85000; // 85%
    uint32 private constant UTIL_PREC = 1e5; // 5 decimals

    // Interest Rate Settings (all rates are per second), 365.24 days per year
    uint64 private constant MIN_INT = 79123523; // 0.25% annual rate
    uint64 private constant MAX_INT = 146248508681; // 10,000% annual rate`        

44. File: fraxlend/src/contracts/VariableInterestRate.sol (line 63):

   `function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {`       

45. File: fraxlend/src/contracts/VariableInterestRate.sol (line 71):

   '_newRatePerSec = uint64((_currentRatePerSec * INT_HALF_LIFE) / _decayGrowth);'`   

46. File: fraxlend/src/contracts/VariableInterestRate.sol (line 78):

   `_newRatePerSec = uint64((_currentRatePerSec * _decayGrowth) / INT_HALF_LIFE);`       

47. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 22):

   `uint8 i = 0;`

48. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 55):

   `function safeDecimals(IERC20 token) internal view returns (uint8) {`

49. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 57):

   `return success && data.length == 32 ? abi.decode(data, (uint8)) : 18;'

50. File: fraxlend/src/contracts/libraries/VaultAccount.sol (line 5-6):

   `    uint128 amount; // Total amount, analogous to market cap
    uint128 shares; // Total shares, analogous to shares outstanding'

51. File: fraxlend/src/contracts/interfaces/IFraxlendPair.sol (line 23):

   `uint64 _newRate`

52. File: fraxlend/src/contracts/interfaces/IFraxlendPair.sol (line 50):

   `function changeFee(uint32 _newFee) external;`

53. File: fraxlend/src/contracts/interfaces/IFraxlendPair.sol (line 64-70):

   `            uint64 lastBlock,
            uint64 feeToProtocolRate,
            uint64 lastTimestamp,
            uint64 ratePerSec
        );

    function decimals() external pure returns (uint8);'

54. File: fraxlend/src/contracts/interfaces/IFraxlendPair.sol (line 89-90):

   `            uint64 _DEFAULT_INT,
            uint16 _DEFAULT_PROTOCOL_FEE,`        

55. File: fraxlend/src/contracts/interfaces/IFraxlendPair.sol (line 114):

   `uint128 _sharesToLiquidate,`       







# [G-10] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function:-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 65-68):

   `require(
            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
        );`






# [G-11] `require()` or `revert()` statements that check input arguments should be at the top of the function (Checks that involve constants should come before checks that involve state variables):-


1. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 228):

   `require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");`






# [G-12]  Use custom errors rather than `revert()`/`require()` strings to save deployment gas:-


1. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 205):

   `require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");`       

2. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 228):

   `require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");`

3. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 253):

   `require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");`

4. File: fraxlend/src/contracts/LinearInterestRate.sol (line 57-68):

   `require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
        require(
            IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
            "FraxlendPairDeployer: Only whitelisted addresses"
        );`

5. File: fraxlend/src/contracts/LinearInterestRate.sol (line 57-68):

   `        require(
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
        );'





# [G-13] Functions guaranteed to revert when called by normal users can be marked `payable` (If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.):-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 204):

   `function setTimeLock(address _newAddress) external onlyOwner {`       

2. File: fraxlend/src/contracts/FraxlendPair.sol (line 234):

   `function withdrawFees(uint128 _shares, address _recipient) external onlyOwner returns (uint256 _amountToTransfer) {`

3. File: fraxlend/src/contracts/FraxlendPair.sol (line 274):

   `function setSwapper(address _swapper, bool _approval) external onlyOwner {`

4. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 170):

   `function setCreationCode(bytes calldata _creationCode) external onlyOwner {`

5. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 50):

   `function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {`       

6. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 65):

   `function setRateContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {`

7. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 80):

   `function setFraxlendDeployerWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {`




# [G-14] Use simple comparison in if statement (The comparison operators >= and <= use more gas than >, 
<, or ==. Replacing the  >= and ≤ operators with a comparison 
operator that has an opcode in the EVM saves gas.):-


1. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 196):

   `if (_maxLTV >= LTV_PRECISION && !_isBorrowerWhitelistActive) revert BorrowerWhitelistRequired();`       

2. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 525-527):

   ` if (_answer <= 0) {
                revert OracleLTEZero(oracleMultiply);
            }`

3. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 1000):

   `if (_leftoverCollateral <= 0) {`

4. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 165-19):

   `if (data.length >= 64) {`

`Recommended Mitigation Steps

Replace the comparison operator and reverse the logic to save gas using the suggestions above.`






# [G-15] Use calldata instead of memory for function parameters (Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.

There are several cases of function arguments using memory instead of calldata):-


1. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 295):

   ` function _totalAssetAvailable(VaultAccount memory _totalAsset, VaultAccount memory _totalBorrow)`       

2. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 559-564):

   `    function _deposit(
        VaultAccount memory _totalAsset,
        uint128 _amount,
        uint128 _shares,
        address _receiver
    )`

3. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 623-629):

   `    function _redeem(
        VaultAccount memory _totalAsset,
        uint128 _amountToReturn,
        uint128 _shares,
        address _receiver,
        address _owner
    ) `

4. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 855-861):

   `    function _repayAsset(
        VaultAccount memory _totalBorrow,
        uint128 _amountToRepay,
        uint128 _shares,
        address _payer,
        address _borrower
    ) internal {`

5. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 1062-1068):

   `    function leveragedPosition(
        address _swapperAddress,
        uint256 _borrowAmount,
        uint256 _initialCollateralAmount,
        uint256 _amountCollateralOutMin,
        address[] memory _path
    )'

6. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 191-201):

   `    function _deployFirst(
        bytes32 _saltSeed,
        bytes memory _configData,
        bytes memory _immutables,
        uint256 _maxLTV,
        uint256 _liquidationFee,
        uint256 _maturityDate,
        uint256 _penaltyRate,
        bool _isBorrowerWhitelistActive,
        bool _isLenderWhitelistActive
    )`        

7. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 242-248):

   `    function _deploySecond(
        string memory _name,
        address _pairAddress,
        bytes memory _configData,
        address[] memory _approvedBorrowers,
        address[] memory _approvedLenders
    ) private {`       

8. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 272-279):

   `    function _logDeploy(
        string memory _name,
        address _pairAddress,
        bytes memory _configData,
        uint256 _maxLTV,
        uint256 _liquidationFee,
        uint256 _maturityDate
    ) private {`   

9. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 310):

   `function deploy(bytes memory _configData) external returns (address _pairAddress) {`       

10. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 355-363):

   `    function deployCustom(
        string memory _name,
        bytes memory _configData,
        uint256 _maxLTV,
        uint256 _liquidationFee,
        uint256 _maturityDate,
        uint256 _penaltyRate,
        address[] memory _approvedBorrowers,
        address[] memory _approvedLenders`

11. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 398):

   `function globalPause(address[] memory _addresses) external returns (address[] memory _updatedAddresses) {`        

12. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 18):

   `function returnDataToString(bytes memory data) internal pure returns (string memory) {`   

13. File: fraxlend/src/contracts/libraries/VaultAccount.sol (line 16-20):

   `    function toShares(
        VaultAccount memory total,
        uint256 amount,
        bool roundUp
    )`        

14. File: fraxlend/src/contracts/libraries/VaultAccount.sol (line 33-37):

   `    function toAmount(
        VaultAccount memory total,
        uint256 shares,
        bool roundUp
    ) internal pure returns (uint256 amount) {`   


 




# [G-16] Amounts should be checked for 0 before calling a transfer (Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done at some places, it’s not consistently done in the solution.

I suggest adding a non-zero-value check here):-


1. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 574):

   `assetContract.safeTransferFrom(msg.sender, address(this), _amount);`










# [G-17] Using b`ools` for storage incurs overhead:-


1. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 33-34):

   `        bool _borrowerWhitelistActive;
        bool _lenderWhitelistActive;`       

2. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 78):

   `mapping(address => bool) public swappers;`

3. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 133-137):

   `    bool public immutable borrowerWhitelistActive;
    mapping(address => bool) public approvedBorrowers;

    bool public immutable lenderWhitelistActive;
    mapping(address => bool) public approvedLenders;`

4. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 94):

   `mapping(address => bool) public deployedPairCustomStatusByAddress;`

5. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 138):

   `bool _isCustom;'

6. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 32-38):

   `    mapping(address => bool) public oracleContractWhitelist;

    // Interest Rate Calculator Whitelist Storage
    mapping(address => bool) public rateContractWhitelist;

    // Fraxlend Deployer Whitelist Storage
    mapping(address => bool) public fraxlendDeployerWhitelist;`       






# [G-18] Empty blocks should be removed or emit something (The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be `abstract` and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified `(if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}})`):-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 68):

   `{}`       

2. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 406):

   `} catch {}`

3. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 40):

   `constructor() Ownable() {}`

4. File: fraxlend/src/contracts/VariableInterestRate.sol (line 57):

   `function requireValidInitData(bytes calldata _initData) external pure {}`







# [G-19] `abi.encode()` is less efficient than `abi.encodePacked()`:-


1. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 450-455):

   `                bytes memory _rateData = abi.encode(
                    _currentRateInfo.ratePerSec,
                    _deltaTime,
                    _utilizationRate,
                    block.number - _currentRateInfo.lastBlock
                );`       

2. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 201-206):

   `                    abi.encode(
                        _currentRateInfo.ratePerSec,
                        _timestamp - _currentRateInfo.lastTimestamp,
                        (_totalBorrow.amount * _UTIL_PREC) / _totalAsset.amount,
                        _blockNumber - _currentRateInfo.lastBlock
                    ),`

3. File: fraxlend/src/contracts/LinearInterestRate.sol (line 47):

   `return abi.encode(MIN_INT, MAX_INT, MAX_VERTEX_UTIL, UTIL_PREC);`

4. File: fraxlend/src/contracts/VariableInterestRate.sol (line 53):

   `return abi.encode(MIN_UTIL, MAX_UTIL, UTIL_PREC, MIN_INT, MAX_INT, INT_HALF_LIFE);`





