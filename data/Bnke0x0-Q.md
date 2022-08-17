## Low Risk


# [L-01] Unbounded loops with external calls (The interface and the function should require a start index and a 
lenght, so that the index composition can be fetched in batches without 
running out of gas. If there are thousands of index components (e.g. 
like the Wilshire 5000 index), the function may revert):-


1. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 122-132):

   `   function getAllPairAddresses() external view returns (address[] memory) {
        string[] memory _deployedPairsArray = deployedPairsArray;
        uint256 _lengthOfArray = _deployedPairsArray.length;
        address[] memory _addresses = new address[](_lengthOfArray);
        uint256 i;
        for (i = 0; i < _lengthOfArray; ) {
            _addresses[i] = deployedPairsByName[_deployedPairsArray[i]];
            unchecked {
                i++;
            }
        }`       

2. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 144-161):

   `    function getCustomStatuses(address[] calldata _addresses)
        external
        view
        returns (PairCustomStatus[] memory _pairCustomStatuses)
    {
        uint256 _lengthOfArray = _addresses.length;
        uint256 i;
        _pairCustomStatuses = new PairCustomStatus[](_lengthOfArray);
        for (i = 0; i < _lengthOfArray; ) {
            _pairCustomStatuses[i] = PairCustomStatus({
                _address: _addresses[i],
                _isCustom: deployedPairCustomStatusByAddress[_addresses[i]]
            });
            unchecked {
                i++;
            }
        }
    `









# [L-02] Unsafe calls to optional ERC20 functions (`decimals()`, `name()` and `symbol()`
 are optional parts of the ERC20 specification, so there are tokens that
 do not implement them. It’s not safe to cast arbitrary token addresses 
in order to call these functions. If `IERC20Metadata` is to be relied on, that should be the variable type of the token variable, rather than it being `address`, so the compiler can verify that types correctly match, rather than this being a runtime failure. ):-


1. File: fraxlend/src/contracts/interfaces/IFraxlendPair.sol (line 133):

   `function name() external view returns (string calldata);`       

2. File: fraxlend/src/contracts/interfaces/IFraxlendPair.sol (line 70):

   `function decimals() external pure returns (uint8);`

3. File: fraxlend/src/contracts/interfaces/IFraxlendPair.sol (line 190):

   `function symbol() external view returns (string calldata);`

4. File: fraxlend/src/contracts/interfaces/IRateCalculator.sol (line 5):

   `function name() external pure returns (string memory);`       

5. File: fraxlend/src/contracts/LinearInterestRate.sol (line 40):

   `function name() external pure returns (string memory) {` 



 






 # [L-02] Missing checks for `address(0x0)` when assigning values to `address` state variables:-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 170-177):

   `    _LTV_PRECISION = LTV_PRECISION;
        _LIQ_PRECISION = LIQ_PRECISION;
        _UTIL_PREC = UTIL_PREC;
        _FEE_PRECISION = FEE_PRECISION;
        _EXCHANGE_PRECISION = EXCHANGE_PRECISION;
        _DEFAULT_INT = DEFAULT_INT;
        _DEFAULT_PROTOCOL_FEE = DEFAULT_PROTOCOL_FEE;
        _MAX_PROTOCOL_FEE = MAX_PROTOCOL_FEE;`       

2. File: fraxlend/src/contracts/FraxlendPair.sol (line 206):

   `TIME_LOCK_ADDRESS = _newAddress;`

3. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 278):

   `rateInitCallData = _rateInitCallData;`

4. File: fraxlend/src/contracts/FraxlendPairCore.sol(line 510):

   `_exchangeRate = _updateExchangeRate();`       

5. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 571):

   `totalAsset = _totalAsset;totalAsset = _totalAsset;`  

6. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 647):

   `totalAsset = _totalAsset;`

7. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 815):

   `totalCollateral -= _collateralAmount;`       







# [L-03] `approve` should be replaced with `safeApprove` or `safeIncreaseAllowance()` / `safeDecreaseAllowance()` (`approve` is subject to a known front-running attack. Consider using `safeApprove` instead:):-


1. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 1103):

   `_assetContract.approve(_swapperAddress, _borrowAmount);`       

2. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 1184):

   `_collateralContract.approve(_swapperAddress, _collateralToSwap);`







##Non-Critical Issues




# [N-01] Adding a `return` statement when the function defines a named return variable, is redundant:-


1. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 133):

`return _addresses;`       

2. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 233):

`return _pairAddress;`










# [N-02] constants should be defined rather than using magic numbers:-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 105):

`_assetsPerUnitShare = totalAsset.toAmount(1e18, false);`       

2. File: fraxlend/src/contracts/FraxlendPairConstants.sol (line 34-48):

`    uint256 internal constant LTV_PRECISION = 1e5; // 5 decimals
    uint256 internal constant LIQ_PRECISION = 1e5;
    uint256 internal constant UTIL_PREC = 1e5;
    uint256 internal constant FEE_PRECISION = 1e5;
    uint256 internal constant EXCHANGE_PRECISION = 1e18;

    // Default Interest Rate (if borrows = 0)
    uint64 internal constant DEFAULT_INT = 158247046; // 0.5% annual rate 1e18 precision

    // Dependencies
    address internal constant FRAXSWAP_ROUTER_ADDRESS = 0xE52D0337904D4D0519EF7487e707268E1DB6495F;

    // Protocol Fee
    uint16 internal constant DEFAULT_PROTOCOL_FEE = 0; // 1e5 precision
    uint256 internal constant MAX_PROTOCOL_FEE = 5e4; // 50% 1e5 precision`

3. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 198):

   `dirtyLiquidationFee = (_liquidationFee * 9000) / LIQ_PRECISION; // 90% of clean fee`

4. File: fraxlend/src/contracts/FraxlendPairCore.sol(line 468):

   `_interestEarned = (_deltaTime * _totalBorrow.amount * _currentRateInfo.ratePerSec) / 1e18;`       

5. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 522):

   `uint256 _price = uint256(1e36);`  

6. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 49-51):

   `    uint256 public DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision
    uint256 public GLOBAL_MAX_LTV = 1e8; // 1000x (100,000%) with 1e5 precision, protects from rounding errors in LTV calc
    uint256 public DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision`

7. File: fraxlend/src/contracts/FraxlendPairDeployer.sol(line 171):

   `bytes memory _firstHalf = BytesLib.slice(_creationCode, 0, 13000);`       

8. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 173-174):

   `        if (_creationCode.length > 13000) {
            bytes memory _secondHalf = BytesLib.slice(_creationCode, 13000, _creationCode.length - 13000);`      

9. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 212):

   `_interestEarned = (_totalBorrow.amount * _newRate * (_timestamp - _currentRateInfo.lastTimestamp)) / 1e18;`   

10. File: fraxlend/src/contracts/LinearInterestRate.sol (line 35-42):

   `    uint256 private constant MAX_INT = 146248508681; // 10,000% annual rate
    uint256 private constant MAX_VERTEX_UTIL = 1e5; // 100%
    uint256 private constant UTIL_PREC = 1e5;`

11. File: fraxlend/src/contracts/VariableInterestRate.sol (line 35-37):

   `    uint32 private constant MIN_UTIL = 75000; // 75%
    uint32 private constant MAX_UTIL = 85000; // 85%
    uint32 private constant UTIL_PREC = 1e5; // 5 decimals

    // Interest Rate Settings (all rates are per second), 365.24 days per year
    uint64 private constant MIN_INT = 79123523; // 0.25% annual rate
    uint64 private constant MAX_INT = 146248508681; // 10,000% annual rate
    uint256 private constant INT_HALF_LIFE = 43200e36; // given in seconds, equal to 12 hours, additional 1e36 to make math simpler`

12. File: fraxlend/src/contracts/VariableInterestRate.sol (line 69):

   `uint256 _deltaUtilization = ((MIN_UTIL - _utilization) * 1e18) / MIN_UTIL;`                    

13. File: fraxlend/src/contracts/VariableInterestRate.sol (line 76):

   `uint256 _deltaUtilization = ((_utilization - MAX_UTIL) * 1e18) / (UTIL_PREC - MAX_UTIL);`   

14. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 14-16):

   '    bytes4 private constant SIG_SYMBOL = 0x95d89b41; // symbol()
    bytes4 private constant SIG_NAME = 0x06fdde03; // name()
    bytes4 private constant SIG_DECIMALS = 0x313ce567; // decimals()'  

15. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 57):

   'return success && data.length == 32 ? abi.decode(data, (uint8)) : 18;'      











# [N-03]  `Event` is missing `indexed` fields
(Each `event` should use three `indexed` fields if there are three or more fields):-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 200):

`event SetTimeLock(address _oldAddress, address _newAddress);`       

2. File: fraxlend/src/contracts/FraxlendPair.sol (line 228):

`event WithdrawFees(uint128 _shares, address _recipient, uint256 _amountToTransfer);`

3. File: fraxlend/src/contracts/FraxlendPair.sol (line 198):

   `event SetSwapper(address _swapper, bool _approval);`

4. File: fraxlend/src/contracts/FraxlendPair.sol(line 468):

   ` event SetApprovedLender(address indexed _address, bool _approval);`       

5. File: fraxlend/src/contracts/FraxlendPair.sol (line 522):

   `event SetApprovedBorrower(address indexed _address, bool _approval);`  

6. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 376-382):

   `    event AddInterest(
        uint256 _interestEarned,
        uint256 _rate,
        uint256 _deltaTime,
        uint256 _feesAmount,
        uint256 _feesShare
    );`

7. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 389):

   `event UpdateRate(uint256 _ratePerSec, uint256 _deltaTime, uint256 _utilizationRate, uint256 _newRatePerSec);`       

8. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 696-701):

   `    event BorrowAsset(
        address indexed _borrower,
        address indexed _receiver,
        uint256 _borrowAmount,
        uint256 _sharesAdded
    );`      

9. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 760):

   `event AddCollateral(address indexed _sender, address indexed _borrower, uint256 _collateralAmount);`   

10. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 846):

   `event RepayAsset(address indexed _sender, address indexed _borrower, uint256 _amountToRepay, uint256 _shares);`

11. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 897-904):

   `    event Liquidate(
        address indexed _borrower,
        uint256 _collateralForLiquidator,
        uint256 _sharesToLiquidate,
        uint256 _amountLiquidatorToRepay,
        uint256 _sharesToAdjust,
        uint256 _amountToAdjust
    );`

12. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 1045-1052):

   `    event LeveragedPosition(
        address indexed _borrower,
        address _swapperAddress,
        uint256 _borrowAmount,
        uint256 _borrowShares,
        uint256 _initialCollateralAmount,
        uint256 _amountCollateralOut
    );`                    

13. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 1143-1149):

   `    event RepayAssetWithCollateral(
        address indexed _borrower,
        address _swapperAddress,
        uint256 _collateralToSwap,
        uint256 _amountAssetOut,
        uint256 _sharesRepaid
    );`   

14. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 45):

` event SetOracleWhitelist(address indexed _address, bool _bool);`       

15. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 60):

` event SetRateContractWhitelist(address indexed _address, bool _bool);`

16. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 75):

   `event SetFraxlendDeployerWhitelist(address indexed _address, bool _bool);`

17. File: fraxlend/src/contracts/interfaces/IERC4626.sol (line 8):

`event Deposit(address indexed caller, address indexed owner, uint256 assets, uint256 shares);`       








# [N-04]  Use of sensitive/non-inclusive terms:-


1. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 138):

`bool _isCustom;`  

Rename to '_isCustom'     

2. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 33-34):

`        bool _borrowerWhitelistActive;
        bool _lenderWhitelistActive;`

Rename to '_borrowerWhitelistActive,'
Rename to '_lenderWhitelistActive'









# [N-05]  Solidity versions lass than the current version should not be included in the pragma range (The below pragmas should be `>` 0.9.0, not `>=`):-


1. File: fraxlend/src/contracts/interfaces/IERC4626.sol (line 2):

`pragma solidity >=0.8.15;
`       

2. File: fraxlend/src/contracts/interfaces/IFraxlendPair.sol (line 2):

`pragma solidity >=0.8.15;`

3. File: fraxlend/src/contracts/interfaces/IFraxlendWhitelist.sol (line 2):

   `pragma solidity >=0.8.15;`

4. File: fraxlend/src/contracts/interfaces/IRateCalculator.sol (line 2):

   `pragma solidity >=0.8.15;`       

5. File: fraxlend/src/contracts/interfaces/ISwapper.sol (line 2):

   `pragma solidity >=0.8.15;`  








# [N-06]  `public`ذfunctions not called by the contract should be declared `external` instead:-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 74-91):

  '    function name() public view override(ERC20, IERC20Metadata) returns (string memory) {
        return nameOfContract;
    }

    function symbol() public view override(ERC20, IERC20Metadata) returns (string memory) {
        // prettier-ignore
        // solhint-disable-next-line max-line-length
        return string(abi.encodePacked("FraxlendV1 - ", collateralContract.safeSymbol(), "/", assetContract.safeSymbol()));
    }

    function decimals() public pure override(ERC20, IERC20Metadata) returns (uint8) {
        return 18;
    }

    // totalSupply for fToken ERC20 compatibility
    function totalSupply() public view override(ERC20, IERC20) returns (uint256) {
        return totalAsset.shares;
    }'
       

2. File: fraxlend/src/contracts/FraxlendPair.sol (line 100):

`function totalAssets() public view virtual returns (uint256) {`

3. File: fraxlend/src/contracts/LinearInterestRate.sol (line 52):

   `function requireValidInitData(bytes calldata _initData) public pure {`









# [N-07]  Non-library/interface files should use fixed compiler versions, not floating ones:-


1. File: fraxlend/src/contracts/FraxlendPair.sol (line 2):

  'pragma solidity ^0.8.15;'
       

2. File: fraxlend/src/contracts/FraxlendPairConstants.sol (line 2):

`pragma solidity ^0.8.15;`

3. File: fraxlend/src/contracts/FraxlendPairCore.sol (line 2):

   `pragma solidity ^0.8.15;`

4. File: fraxlend/src/contracts/FraxlendPairDeployer.sol (line 2):

   `pragma solidity ^0.8.15;`       

5. File: fraxlend/src/contracts/FraxlendPairHelper.sol (line 2):

   `pragma solidity ^0.8.15;`

6. File: fraxlend/src/contracts/FraxlendWhitelist.sol (line 2):

  'pragma solidity ^0.8.15;'   

7. File: fraxlend/src/contracts/LinearInterestRate.sol (line 2):

`pragma solidity ^0.8.15;`

8. File: fraxlend/src/contracts/VariableInterestRate.sol (line 2):

   `pragma solidity ^0.8.15;`

9. File: fraxlend/src/contracts/libraries/SafeERC20.sol (line 2):

   `pragma solidity ^0.8.15;`       

10. File: fraxlend/src/contracts/libraries/VaultAccount.sol (line 2):

   `pragma solidity ^0.8.15;`     