

## Table of Contents:

G-01 State variables only set in the constructor should be declared immutable (3 instances)
G-02 ++i costs less gas than i++, especially when it's used in for loops (9 instances) 
G-03 ++i/i++ should be unchecked{++i}/unchecked{i++} (7 instances)
G-04 <array>.length should not be looked up in every loop of a for-loop (7 instances)
G-05 Comparison operators (9 instances)
G-06 Using private rather than public for constants saves gas (6 instances)
G-07 calldata instead of memory for read-only function parameter (23 instances)
G-08 internal functions only called once can be inlined to save gas (11 instances)
G-09 Using > 0 costs more gas than != 0 when used on a uint in a require() statement (1 instance)
G-10 Empty blocks should be removed or emit something (4 instances)
G-11 Using bools for storage incurs overhead (3 instances)
G-12 Using storage instead of memory for structs/arrays saves gas (8 instances)
G-13 <x> += <y> costs more gas than <x> = <x> + <y> for state variables (10 instances)
G-14 Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead (64 instances)
G-15 require()/revert() strings longer than 32 bytes cost extra gas (13 instances)
G-16 Functions guaranteed to revert when called by normal users can be marked payable (7 instances)
G-17 It costs more gas to initialize variables to zero than to let the default of zero be applied (10 instances)
G-18 Require instead of && (multiple require statements to save gas) (5 instances)
G-19 Using calldata instead of memory for read-only arguments in external functions saves gas (4 instances)

Total: 204 instances over 19 issues


## G-01 State variables only set in the constructor should be declared immutable

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

There are 3 instances in 1 file:
File: contracts/FraxlendPairDeployer.sol

    address public CIRCUIT_BREAKER_ADDRESS;
    address public COMPTROLLER_ADDRESS;
    address public TIME_LOCK_ADDRESS;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L57-L59


## G-02 ++i costs less gas than i++, especially when it's used in for loops  

There are 9 instances in 4 files:
File: contracts/libraries/SafeERC20.sol

            i++;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L24

            for (i = 0; i < 32 && data[i] != 0; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L27

File: contracts/FraxlendWhitelist.sol

        for (uint256 i = 0; i < _addresses.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51

        for (uint256 i = 0; i < _addresses.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66

   for (uint256 i = 0; i < _addresses.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81

File: contracts/FraxlendPair.sol

       for (uint256 i = 0; i < _lenders.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289

        for (uint256 i = 0; i < _borrowers.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308

File: contracts/FraxlendPairDeployer.sol

                i++;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L130

                i++;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L158




## G-03 ++i/i++ should be unchecked{++i}/unchecked{i++}

when it is not possible for them to overflow, as is the case when used in for- and while-loops

There are 7 instances in 3 files:
File: contracts/libraries/SafeERC20.sol

                i++;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L24

            for (i = 0; i < 32 && data[i] != 0; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L27

File: contracts/FraxlendWhitelist.sol
        for (uint256 i = 0; i < _addresses.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51

        for (uint256 i = 0; i < _addresses.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66

   for (uint256 i = 0; i < _addresses.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81

File: contracts/FraxlendPair.sol

       for (uint256 i = 0; i < _lenders.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289

        for (uint256 i = 0; i < _borrowers.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308




## G-04 <array>.length should not be looked up in every loop of a for-loop

The overheads outlined below are PER LOOP, excluding the first loop
storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

There are 7 instances in 3 files:
File: contracts/FraxlendWhitelist.sol

	for (uint256 i = 0; i < _addresses.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51

     	for (uint256 i = 0; i < _addresses.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66

        for (uint256 i = 0; i < _addresses.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81

File: contracts/FraxlendPairCore.sol

        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265

   	for (uint256 i = 0; i < _approvedLenders.length; ++i) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L270

File: contracts/FraxlendPair.sol
     	for (uint256 i = 0; i < _lenders.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289

        for (uint256 i = 0; i < _borrowers.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308



## G-05 Comparison operators

In the EVM, there is no opcode for >= or <=. When using greater than or equal, two operations are performed: > and =.
Using strict comparison operators hence saves gas.

There are 9 instances in 3 files:
File: contracts/libraries/SafeERC20.sol

        if (data.length >= 64) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L19

File: contracts/LinearInterestRate.sol

            _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L58

            _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L62

File: contracts/FraxlendPairCore.sol

            if (_maxLTV >= LTV_PRECISION && !_isBorrowerWhitelistActive) revert BorrowerWhitelistRequired();
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L196

                _interestEarned + _totalBorrow.amount <= type(uint128).max &&
                _interestEarned + _totalAsset.amount <= type(uint128).max
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L472

         if (_answer <= 0) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L525

            if (_answer <= 0) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L533

            if (_leftoverCollateral <= 0) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1000



## G-06 Using private rather than public for constants saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

There are 6 instances in 1 file:
File: contracts/FraxlendPairDeployer.sol

    uint256 public DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision
    uint256 public GLOBAL_MAX_LTV = 1e8; // 1000x (100,000%) with 1e5 precision, protects from rounding errors in LTV calc
    uint256 public DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L49

    address public CIRCUIT_BREAKER_ADDRESS;
    address public COMPTROLLER_ADDRESS;
    address public TIME_LOCK_ADDRESS;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L57



## G-07 calldata instead of memory for read-only function parameter

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. 
Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.
Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

There are 23 instances in 4 files:
File: contracts/libraries/SafeERC20.sol

    function returnDataToString(bytes memory data) internal pure returns (string memory) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L18

File: contracts/libraries/VaultAccount.sol

        VaultAccount memory total,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol#L17

        VaultAccount memory total,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol#L34

File: contracts/FraxlendPairCore.sol

    function _totalAssetAvailable(VaultAccount memory _totalAsset, VaultAccount memory _totalBorrow)
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L295

        VaultAccount memory _totalAsset,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L560

        VaultAccount memory _totalAsset,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L624

        VaultAccount memory _totalBorrow,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L856

        address[] memory _path
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1067

File: contracts/FraxlendPair.sol

        bytes memory _configData,
        bytes memory _immutables,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L46

contracts/FraxlendPairDeployer.sol
        bytes memory _configData,
        bytes memory _immutables,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L193

        string memory _name,
        address _pairAddress,
        bytes memory _configData,
        address[] memory _approvedBorrowers,
        address[] memory _approvedLenders
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L243

        string memory _name,
        address _pairAddress,
        bytes memory _configData,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L273

   	function deploy(bytes memory _configData) external returns (address _pairAddress) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L310

        string memory _name,
        bytes memory _configData,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L356

        address[] memory _approvedBorrowers,
        address[] memory _approvedLenders
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L362

   	function globalPause(address[] memory _addresses) external returns (address[] memory _updatedAddresses) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L398





## G-08 internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

There are 11 instances in 5 files:
File: contracts/libraries/SafeERC20.sol

    function safeTransfer(
        IERC20 token,
        address to,
        uint256 value
    ) internal {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L60-L65

    function safeTransferFrom(
        IERC20 token,
        address from,
        address to,
        uint256 value
    ) internal {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L68-L73

File: contracts/libraries/VaultAccount.sol

    function toShares(
        VaultAccount memory total,
        uint256 amount,
        bool roundUp
    ) internal pure returns (uint256 shares) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol#L16-L20

    function toAmount(
        VaultAccount memory total,
        uint256 shares,
        bool roundUp
    ) internal pure returns (uint256 amount) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol#L33-L37

File: contracts/FraxlendPairCore.sol

    function _totalAssetAvailable(VaultAccount memory _totalAsset, VaultAccount memory _totalBorrow)
        internal
        pure
        returns (uint256)
    {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L295-L299

File: contracts/FraxlendPairCore.sol

    function _addInterest()
        internal
        returns (
            uint256 _interestEarned,
            uint256 _feesAmount,
            uint256 _feesShare,
            uint64 _newRate
        )
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L409-L416

    function _deposit(
        VaultAccount memory _totalAsset,
        uint128 _amount,
        uint128 _shares,
        address _receiver
    ) internal {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L559-L564

    function _redeem(
        VaultAccount memory _totalAsset,
        uint128 _amountToReturn,
        uint128 _shares,
        address _receiver,
        address _owner
    ) internal {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L623-L629

    function _addCollateral(
        address _sender,
        uint256 _collateralAmount,
        address _borrower
    ) internal {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L766-L770

    function _removeCollateral(
        uint256 _collateralAmount,
        address _receiver,
        address _borrower
    ) internal {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L806-L810

File: contracts/FraxlendPairCore.sol

    function _repayAsset(
        VaultAccount memory _totalBorrow,
        uint128 _amountToRepay,
        uint128 _shares,
        address _payer,
        address _borrower
    ) internal {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L855-L861



## G-09 Using > 0 costs more gas than != 0 when used on a uint in a require() statement

This change saves 6 gas per instance.

There is 1 instance in 1 file:

File: contracts/LinearInterestRate.sol
            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L66




## G-10 Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

There are 4 instances in 4 files:
File: contracts/VariableInterestRate.sol

    function requireValidInitData(bytes calldata _initData) external pure {}
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L57

File: contracts/FraxlendWhitelist.sol

    constructor() Ownable() {}
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L40

File: contracts/FraxlendPair.sol

    {}
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L68

File: contracts/FraxlendPairDeployer.sol

            } catch {}
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L406




## G-11 Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, 
replace the bits taken up by the boolean, and then write back. 
This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use uint256(1) and uint256(2) for true/false

There are 3 instances in 2 files:
File: contracts/mixins/shared/MarketFees.sol

   bool private immutable assumePrimarySale;
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L61

File: contracts/FraxlendPairCore.sol

    bool public immutable borrowerWhitelistActive;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L133

    bool public immutable lenderWhitelistActive;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L136




## G-12 Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. 
If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. 
Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. 
The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

There are 8 instances in 5 files:
File: contracts/FraxlendPairCore.sol

        address[] memory _path
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1067

File: contracts/FraxlendPairDeployer.sol

    function getAllPairAddresses() external view returns (address[] memory) {
        string[] memory _deployedPairsArray = deployedPairsArray;
        uint256 _lengthOfArray = _deployedPairsArray.length;
        address[] memory _addresses = new address[](_lengthOfArray);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L122

File: contracts/FraxlendPairDeployer.sol

        returns (PairCustomStatus[] memory _pairCustomStatuses)
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L147

File: contracts/FraxlendPairDeployer.sol

        address[] memory _approvedBorrowers,
        address[] memory _approvedLenders
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L246

File: contracts/FraxlendPairDeployer.sol

        address[] memory _approvedBorrowers,
        address[] memory _approvedLenders
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L362


## G-13 <x> += <y> costs more gas than <x> = <x> + <y> for state variables

There are 10 instances in 1 file:
File: contracts/FraxlendPairCore.sol

        _totalBorrow.amount += uint128(_interestEarned);
        _totalAsset.amount += uint128(_interestEarned);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L475

        _totalAsset.shares += uint128(_feesShare);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L484

        _totalAsset.amount += _amount;
        _totalAsset.shares += _shares;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L566

        _totalBorrow.amount += _borrowAmount;
        _totalBorrow.shares += uint128(_sharesAdded);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L718

        userBorrowShares[msg.sender] += _sharesAdded;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L724

        userCollateralBalance[_borrower] += _collateralAmount;
        totalCollateral += _collateralAmount;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L772



## G-14 Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. 
Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Use a larger size then downcast where needed

There are 64 instances in 6 files:
File: contracts/libraries/SafeERC20.sol

           uint8 i = 0;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L22

    function safeDecimals(IERC20 token) internal view returns (uint8) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L55

        return success && data.length == 32 ? abi.decode(data, (uint8)) : 18;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L57

File: contracts/libraries/VaultAccount.sol

    uint128 amount; // Total amount, analogous to market cap
    uint128 shares; // Total shares, analogous to shares outstanding
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol#L5

    function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L76

        (, , uint256 _utilization, ) = abi.decode(_data, (uint64, uint256, uint256, uint256));
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L78

            _newRatePerSec = uint64(_minInterest + ((_utilization * _slope) / UTIL_PREC));
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L85

            _newRatePerSec = uint64(_vertexInterest + (((_utilization - _vertexUtilization) * _slope) / UTIL_PREC));
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L88

            _newRatePerSec = uint64(_vertexInterest);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L90

File: contracts/VariableInterestRate.sol

    uint32 private constant MIN_UTIL = 75000; // 75%
    uint32 private constant MAX_UTIL = 85000; // 85%
    uint32 private constant UTIL_PREC = 1e5; // 5 decimals
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L35

    uint64 private constant MIN_INT = 79123523; // 0.25% annual rate
    uint64 private constant MAX_INT = 146248508681; // 10,000% annual rate
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L40

    function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {
        (uint64 _currentRatePerSec, uint256 _deltaTime, uint256 _utilization, ) = abi.decode(
            _data,
            (uint64, uint256, uint256, uint256)
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L63

            _newRatePerSec = uint64((_currentRatePerSec * INT_HALF_LIFE) / _decayGrowth);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L71

            _newRatePerSec = uint64((_currentRatePerSec * _decayGrowth) / INT_HALF_LIFE);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L78

File: contracts/FraxlendPairConstants.sol

    uint64 internal constant DEFAULT_INT = 158247046; // 0.5% annual rate 1e18 precision
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairConstants.sol#L41

    uint16 internal constant DEFAULT_PROTOCOL_FEE = 0; // 1e5 precision
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairConstants.sol#L47

File: contracts/FraxlendPairCore.sol

        uint64 lastBlock;
        uint64 feeToProtocolRate; // Fee amount 1e5 precision
        uint64 lastTimestamp;
        uint64 ratePerSec;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L106

        uint32 lastTimestamp;
        uint224 exchangeRate; // collateral:asset ratio. i.e. how much collateral to buy 1e18 asset
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L117

            _currentRateInfo.lastTimestamp = uint64(block.timestamp);
            _currentRateInfo.lastBlock = uint64(block.number);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L434

                _newRate = uint64(penaltyRate);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L448

            _currentRateInfo.lastTimestamp = uint64(block.timestamp);
            _currentRateInfo.lastBlock = uint64(block.number);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L464

                _interestEarned + _totalBorrow.amount <= type(uint128).max &&
                _interestEarned + _totalAsset.amount <= type(uint128).max
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L472

                _totalBorrow.amount += uint128(_interestEarned);
                _totalAsset.amount += uint128(_interestEarned);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L475

                    _totalAsset.shares += uint128(_feesShare);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L484

        if (_exchangeRate > type(uint224).max) revert PriceTooLarge();
        _exchangeRateInfo.exchangeRate = uint224(_exchangeRate);
        _exchangeRateInfo.lastTimestamp = uint32(block.timestamp);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L542

        uint128 _amount,
        uint128 _shares,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L561

        uint128 _amountToReturn,
        uint128 _shares,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L625

  	 function _borrowAsset(uint128 _borrowAmount, address _receiver) internal returns (uint256 _sharesAdded) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L707

        uint128 _amountToRepay,
        uint128 _shares,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L857

        uint128 _sharesToLiquidate,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L951

        uint256 _userCollateralBalance = userCollateralBalance[_borrower];
        uint128 _borrowerShares = userBorrowShares[_borrower].toUint128();
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L966

        uint128 _amountLiquidatorToRepay = (_totalBorrow.toAmount(_sharesToLiquidate, true)).toUint128();
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L993

        uint128 _sharesToAdjust;
        uint128 _amountToAdjust;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L997

                uint128 _leftoverBorrowShares = _borrowerShares - _sharesToLiquidate;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1001


contracts/FraxlendPair.sol

    function decimals() public pure override(ERC20, IERC20Metadata) returns (uint8) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L84

        return type(uint128).max;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L137

        return type(uint128).max;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L141

            uint64 _DEFAULT_INT,
            uint16 _DEFAULT_PROTOCOL_FEE,
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L165

    event ChangeFee(uint32 _newFee);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L211

    function changeFee(uint32 _newFee) external whenNotPaused {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L215

    event WithdrawFees(uint128 _shares, address _recipient, uint256 _amountToTransfer);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L228

        if (_shares == 0) _shares = uint128(balanceOf(address(this)));
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L240

        _totalAsset.amount -= uint128(_amountToTransfer);
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L252




## G-14 Use custom errors rather than revert()/require() strings to save gas

There are 13 instances in 2 files:
File: contracts/LinearInterestRate.sol

        require(
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
        );
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L57

File: contracts/FraxlendPairDeployer.sol

            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L205

            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L228

        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
        require(
            IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
            "FraxlendPairDeployer: Only whitelisted addresses"
        );
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L365

        require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L399


## G-15 require()/revert() strings longer than 32 bytes cost extra gas

There are 7 instances in 2 files: 
File: contracts/LinearInterestRate.sol

        require(
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
        );
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L57

File: contracts/FraxlendPairDeployer.sol

            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L205

            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L228

        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
        require(
            IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
            "FraxlendPairDeployer: Only whitelisted addresses"
        );
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L365




## G-16  Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. 
Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 
The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), 
which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There are 7 instances in 3 files:
File: contracts/FraxlendWhitelist.sol

    function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L50

    function setRateContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L65

    function setFraxlendDeployerWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L80

File: contracts/FraxlendPair.sol

    function setTimeLock(address _newAddress) external onlyOwner {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L204

    function withdrawFees(uint128 _shares, address _recipient) external onlyOwner returns (uint256 _amountToTransfer) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L234

    function setSwapper(address _swapper, bool _approval) external onlyOwner {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L274

File: contracts/FraxlendPairDeployer.sol

    function setCreationCode(bytes calldata _creationCode) external onlyOwner {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L170



G-17 It costs more gas to initialize variables to zero than to let the default of zero be applied

There are 10 instances in 6 files:
File: contracts/libraries/SafeERC20.sol

            uint8 i = 0;
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L22

File: contracts/LinearInterestRate.so

    uint256 private constant MIN_INT = 0; // 0.00% annual rate
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L33

File: contracts/FraxlendWhitelist.sol

        for (uint256 i = 0; i < _addresses.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51

      for (uint256 i = 0; i < _addresses.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66

        for (uint256 i = 0; i < _addresses.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81

File: contracts/FraxlendPairCore.sol

        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265

    for (uint256 i = 0; i < _approvedLenders.length; ++i) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L270

File: contracts/FraxlendPair.sol

     for (uint256 i = 0; i < _lenders.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289

      for (uint256 i = 0; i < _borrowers.length; i++) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308

File: contracts/FraxlendPairDeployer.sol

       for (uint256 i = 0; i < _lengthOfArray; ) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L402




## G-18 Require instead of && (multiple require statements to save gas)

Require statements including conditions with the && operator can be broken down in multiple require statements to save gas.

There are 5 instances in 1 file:
File: contracts/LinearInterestRate.sol

        require(
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
        );
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L57




## G-19 Using calldata instead of memory for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. 
Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). 
Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one.

There are 4 instances in 2 files:
File: contracts/FraxlendPairCore.sol

        address[] memory _path
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1067

File: contracts/FraxlendPairDeployer.sol

    function deploy(bytes memory _configData) external returns (address _pairAddress) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L310

        address[] memory _approvedBorrowers,
        address[] memory _approvedLenders
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L362

    function globalPause(address[] memory _addresses) external returns (address[] memory _updatedAddresses) {
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L398



