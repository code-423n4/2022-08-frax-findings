### Missing `@param` statements
___
[FraxlendPairCore.sol: L143-160](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L143-L160)
```solidity
    /// @notice The ```constructor``` function is called on deployment
    /// @param _configData abi.encode(address _asset, address _collateral, address _oracleMultiply, address _oracleDivide, uint256 _oracleNormalization, address _rateContract, bytes memory _rateInitData)
    /// @param _maxLTV The Maximum Loan-To-Value for a borrower to be considered solvent (1e5 precision)
    /// @param _liquidationFee The fee paid to liquidators given as a % of the repayment (1e5 precision)
    /// @param _maturityDate The maturityDate date of the Pair
    /// @param _penaltyRate The interest rate after maturity date
    /// @param _isBorrowerWhitelistActive Enables borrower whitelist
    /// @param _isLenderWhitelistActive Enables lender whitelist
    constructor(
        bytes memory _configData,
        bytes memory _immutables,
        uint256 _maxLTV,
        uint256 _liquidationFee,
        uint256 _maturityDate,
        uint256 _penaltyRate,
        bool _isBorrowerWhitelistActive,
        bool _isLenderWhitelistActive
    ) {
```
`@param` statement is missing for `_immutables`
___
[FraxlendPairCore.sol: L791-800](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L791-L800)
```solidity
    /// @notice The ```RemoveCollateral``` event is emitted when collateral is removed from a borrower's position
    /// @param _sender The account from which funds are transferred
    /// @param _collateralAmount The amount of Collateral Token to be transferred
    /// @param _receiver The address to which Collateral Tokens will be transferred
    event RemoveCollateral(
        address indexed _sender,
        uint256 _collateralAmount,
        address indexed _receiver,
        address indexed _borrower
    );
```
`@param` statement is missing for `_borrower`
___
[FraxlendPairCore.sol: L892-904](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L892-L904)
```solidity
    /// @notice The ```Liquidate``` event is emitted when a liquidation occurs
    /// @param _borrower The borrower account for which the liquidation occured
    /// @param _collateralForLiquidator The amount of Collateral Token transferred to the liquidator
    /// @param _sharesToLiquidate The number of Borrow Shares the liquidator repaid on behalf of the borrower
    /// @param _sharesToAdjust The number of Borrow Shares that were adjusted on liabilites and assets (a writeoff)
    event Liquidate(
        address indexed _borrower,
        uint256 _collateralForLiquidator,
        uint256 _sharesToLiquidate,
        uint256 _amountLiquidatorToRepay,
        uint256 _sharesToAdjust,
        uint256 _amountToAdjust
    );
```
`@param` statements missing for `_amountLiquidatorToRepay` and `_amountToAdjust`
___
[FraxlendPairDeployer.sol: L183-201](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L183-L201)
```solidity
    /// @notice The ```_deployFirst``` function is an internal function with deploys the pair
    /// @param _configData abi.encode(address _asset, address _collateral, address _oracleMultiply, address _oracleDivide, uint256 _oracleNormalization, address _rateContract, bytes memory _rateInitData)
    /// @param _maxLTV The Maximum Loan-To-Value for a borrower to be considered solvent (1e5 precision)
    /// @param _liquidationFee The fee paid to liquidators given as a % of the repayment (1e5 precision)
    /// @param _maturityDate The maturityDate of the Pair
    /// @param _isBorrowerWhitelistActive Enables borrower whitelist
    /// @param _isLenderWhitelistActive Enables lender whitelist
    /// @return _pairAddress The address to which the Pair was deployed
    function _deployFirst(
        bytes32 _saltSeed,
        bytes memory _configData,
        bytes memory _immutables,
        uint256 _maxLTV,
        uint256 _liquidationFee,
        uint256 _maturityDate,
        uint256 _penaltyRate,
        bool _isBorrowerWhitelistActive,
        bool _isLenderWhitelistActive
    ) private returns (address _pairAddress) {
```
`@param` statements missing for `_saltSeed`, `_immutables` and `_penaltyRate`
___
[FraxlendPairDeployer.sol: L345-364](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L345-L364)
```solidity
    /// @notice The ```deployCustom``` function allows whitelisted users to deploy custom Term Sheets for OTC debt structuring
    /// @dev Caller must be added to FraxLedWhitelist
    /// @param _name The name of the Pair
    /// @param _configData abi.encode(address _asset, address _collateral, address _oracleMultiply, address _oracleDivide, uint256 _oracleNormalization, address _rateContract, bytes memory _rateInitData)
    /// @param _maxLTV The Maximum Loan-To-Value for a borrower to be considered solvent (1e5 precision)
    /// @param _liquidationFee The fee paid to liquidators given as a % of the repayment (1e5 precision)
    /// @param _maturityDate The maturityDate of the Pair
    /// @param _approvedBorrowers An array of approved borrower addresses
    /// @param _approvedLenders An array of approved lender addresses
    /// @return _pairAddress The address to which the Pair was deployed
    function deployCustom(
        string memory _name,
        bytes memory _configData,
        uint256 _maxLTV,
        uint256 _liquidationFee,
        uint256 _maturityDate,
        uint256 _penaltyRate,
        address[] memory _approvedBorrowers,
        address[] memory _approvedLenders
    ) external returns (address _pairAddress) {
```
`@param` statement is missing for `_penaltyRate`
___
___

### Duplicate comment

[FraxlendPairCore.sol: L1020-1022](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L1020-L1022)
```solidity
        // NOTE: reverts if _collateralForLiquidator > userCollateralBalance
        // Collateral is removed on behalf of borrower and sent to liquidator
        // NOTE: reverts if _collateralForLiquidator > userCollateralBalance
```
Remove duplicate comment line
___
___

### Typos
___

[VariableInterestRate.sol: L32](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/VariableInterestRate.sol#L32)
```solidity
/// @notice A Contract for calulcating interest rates as a function of utilization and time
```
Change `calulcating` to `calculating`
___
[FraxlendWhitelist.sol: L47](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L47)
```solidity
    /// @notice The ```setOracleContractWhitelist``` function sets a given address to true/false for use as oralce
```
Change `oralce` to `Oracle`
___
[FraxlendPairCore.sol: L231](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L231)
```solidity
        // Set approved lenders whitlist active
```
Change `whitlist` to `whitelist`
___
[FraxlendPairCore.sol: L893](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L893)
```solidity
    /// @param _borrower The borrower account for which the liquidation occured
```
Change `occured` to `occurred`
___
[FraxlendPairCore.sol: L896](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L896)
```solidity
    /// @param _sharesToAdjust The number of Borrow Shares that were adjusted on liabilites and assets (a writeoff)
```
Change `liabilites` to `liabilities`
___
[FraxlendPairCore.sol: L1010](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L1010)
```solidity
                    // Note: Ensure this memory stuct will be passed to _repayAsset for write to state
```
Change `stuct` to `struct`
___
[FraxlendPairCore.sol: L1041](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L1041)
```solidity
    /// @param _borrowAmount The amount of Asset Token to be borrowed to be borrowed
```
Remove repeated phrase `to be borrowed`
___
The same typo (`black list`) occurs in both lines below:

[FraxlendPair.sol: L285](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L285)

[FraxlendPair.sol: L304](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L304)
```solidity
    /// @dev Cannot black list self
```
Change `black list` to `blacklist` in both cases
___
The same typo (`whos`) occurs in both lines below:

[FraxlendPair.sol: L286](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L286)

[FraxlendPair.sol: L305](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L305)
```solidity
    /// @param _lenders The addresses whos status will be set
```
Change `whos` to `whose`
___
The same typo (`approcal`) occurs in both lines below:

[FraxlendPair.sol: L287](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L287)

[FraxlendPair.sol: L306](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L306)
```solidity
    /// @param _approval The approcal status
```
Change `approcal` to `approval`
___
[FraxlendPairDeployer.sol: L168](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L168)
```solidity
    /// @dev splits the data if necessary to accomodate creation code that is slightly larger than 24kb
```
Change `accomodate` to `accommodate`
___
___

### Long single line comments 
In theory, comments over 79 characters should wrap using multi-line comment syntax. Even if somewhat longer comments are acceptable, there are cases where very long comments interfere with readability. Below are five instances of extra-long comments whose readability could be improved by wrapping, as shown:
___
[FraxlendPairCore.sol: L391](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L391)
```solidity
    /// @notice The ```addInterest``` function is a public implementation of _addInterest and allows 3rd parties to trigger interest accrual
```
Suggestion:
```solidity
    /// @notice The ```addInterest``` function is a public implementation of _addInterest
    ///   and allows 3rd parties to trigger interest accrual.
```
___
[FraxlendPairCore.sol: L406](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L406)
```solidity
    /// @notice The ```_addInterest``` function is invoked prior to every external function and is used to accrue interest and update interest rate
```
Suggestion:
```solidity
    /// @notice The ```_addInterest``` function is invoked prior to every external function
    ///   and is used to accrue interest and update interest rate.
```
___
[FraxlendPairCore.sol: L616-618](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L616-L618)
```solidity
    /// @notice The ```_redeem``` function is an internal implementation which allows a Lender to pull their Asset Tokens out of the Pair
    /// @dev Caller must invoke ```ERC20.approve``` on the Asset Token contract prior to calling function
    /// @param _totalAsset An in-memory VaultAccount struct which holds the total amount of Asset Tokens and the total number of Asset Shares (fTokens)
```
Suggestion:
```solidity
    /// @notice The ```_redeem``` function is an internal implementation 
    ///   which allows a Lender to pull their Asset Tokens out of the Pair.
    /// @dev Caller must invoke ```ERC20.approve``` on the Asset Token contract prior to calling function
    /// @param _totalAsset An in-memory VaultAccount struct which holds the total amount of Asset Tokens 
    ///   and the total number of Asset Shares (fTokens).
```
___
[FraxlendPairCore.sol: L982-983](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L982-L983)
```solidity
            // Because interest accrues every block, _liquidationAmountInCollateralUnits from a few lines up is an ever increasing value
            // This means that leftoverCollateral can occasionally go negative by a few hundred wei (cleanLiqFee premium covers this for liquidator)
```
Suggestion:
```solidity
            // Because interest accrues every block, _liquidationAmountInCollateralUnits 
            //   from a few lines up is an ever increasing value.
            // This means that leftoverCollateral can occasionally go negative
            //   by a few hundred wei (cleanLiqFee premium covers this for liquidator).
```
___
[FraxlendPairCore.sol: L1151-1156](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L1151-L1156)
```solidity
    /// @notice The ```repayAssetWithCollateral``` function allows a borrower to repay their debt using existing collateral in contract
    /// @param _swapperAddress The address of the whitelisted swapper to use for token swaps
    /// @param _collateralToSwap The amount of Collateral Tokens to swap for Asset Tokens
    /// @param _amountAssetOutMin The minimum amount of Asset Tokens to receive during the swap
    /// @param _path An array containing the addresses of ERC20 tokens to swap.  Adheres to UniV2 style path params.
    /// @return _amountAssetOut The amount of Asset Tokens received for the Collateral Tokens, the amount the borrowers account was credited
```
Suggestion:
```solidity
    /// @notice The ```repayAssetWithCollateral``` function allows a borrower
    ///   to repay their debt using existing collateral in contract.
    /// @param _swapperAddress The address of the whitelisted swapper to use for token swaps
    /// @param _collateralToSwap The amount of Collateral Tokens to swap for Asset Tokens
    /// @param _amountAssetOutMin The minimum amount of Asset Tokens to receive during the swap
    /// @param _path An array containing the addresses of ERC20 tokens to swap —
    ///   adheres to UniV2 style path params.
    /// @return _amountAssetOut The amount of Asset Tokens received for the Collateral Tokens,
    ///   the amount the borrowers account was credited.
```
___
___
