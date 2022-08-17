### [G-01] ++i costs less gas compared to i++

++i costs **about 5 gas less per iteration** compared to i++ for unsigned integer.
This statement is true even with the optimizer enabled.
Summarized my results where i is used in a loop, is unsigned integer, and you safely can be changed to ++i without changing any behavior,

I've found 7 locations in 3 files:

```
src/contracts/FraxlendPair.sol:
  288      function setApprovedLenders(address[] calldata _lenders, bool _approval) external approvedLender(msg.sender) {
  289:         for (uint256 i = 0; i < _lenders.length; i++) {
  290              // Do not set when _approval == false and _lender == msg.sender

  307      function setApprovedBorrowers(address[] calldata _borrowers, bool _approval) external approvedBorrower {
  308:         for (uint256 i = 0; i < _borrowers.length; i++) {
  309              // Do not set when _approval == false and _borrower == msg.sender

src/contracts/FraxlendWhitelist.sol:
  50      function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
  51:         for (uint256 i = 0; i < _addresses.length; i++) {
  52              oracleContractWhitelist[_addresses[i]] = _bool;

  65      function setRateContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
  66:         for (uint256 i = 0; i < _addresses.length; i++) {
  67              rateContractWhitelist[_addresses[i]] = _bool;

  80      function setFraxlendDeployerWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
  81:         for (uint256 i = 0; i < _addresses.length; i++) {
  82              fraxlendDeployerWhitelist[_addresses[i]] = _bool;

src/contracts/libraries/SafeERC20.sol:
  23              while (i < 32 && data[i] != 0) {
  24:                 i++;
  25              }
  26              bytes memory bytesArray = new bytes(i);
  27:             for (i = 0; i < 32 && data[i] != 0; i++) {
  28                  bytesArray[i] = data[i];
```

---------------------------------------------------------------------------

### [G-02] An arrays length should be cached to save gas in for-loops

An array’s length should be cached to save gas in for-loops
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset).
Caching the array length in the stack saves around **3 gas per iteration**.

I've found 7 locations in 3 files:

```
src/contracts/FraxlendPair.sol:
  288      function setApprovedLenders(address[] calldata _lenders, bool _approval) external approvedLender(msg.sender) {
  289:         for (uint256 i = 0; i < _lenders.length; i++) {
  290              // Do not set when _approval == false and _lender == msg.sender

  307      function setApprovedBorrowers(address[] calldata _borrowers, bool _approval) external approvedBorrower {
  308:         for (uint256 i = 0; i < _borrowers.length; i++) {
  309              // Do not set when _approval == false and _borrower == msg.sender

src/contracts/FraxlendPairCore.sol:
  264          // Set approved borrowers
  265:         for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
  266              approvedBorrowers[_approvedBorrowers[i]] = true;

  269          // Set approved lenders
  270:         for (uint256 i = 0; i < _approvedLenders.length; ++i) {
  271              approvedLenders[_approvedLenders[i]] = true;

src/contracts/FraxlendWhitelist.sol:
  50      function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
  51:         for (uint256 i = 0; i < _addresses.length; i++) {
  52              oracleContractWhitelist[_addresses[i]] = _bool;

  65      function setRateContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
  66:         for (uint256 i = 0; i < _addresses.length; i++) {
  67              rateContractWhitelist[_addresses[i]] = _bool;

  80      function setFraxlendDeployerWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
  81:         for (uint256 i = 0; i < _addresses.length; i++) {
  82              fraxlendDeployerWhitelist[_addresses[i]] = _bool;
```

---------------------------------------------------------------------------


### [G-03] Using default values is cheaper than assignment

If a variable is not set/initialized, it is assumed to have the default value 0 for uint, and false for boolean.
Explicitly initializing it with its default value is an anti-pattern and wastes gas.
For example: ```uint8 i = 0;``` should be replaced with ```uint8 i;```

I've found 9 locations in 5 files:

```
src/contracts/FraxlendPair.sol:
  288      function setApprovedLenders(address[] calldata _lenders, bool _approval) external approvedLender(msg.sender) {
  289:         for (uint256 i = 0; i < _lenders.length; i++) {
  290              // Do not set when _approval == false and _lender == msg.sender

  307      function setApprovedBorrowers(address[] calldata _borrowers, bool _approval) external approvedBorrower {
  308:         for (uint256 i = 0; i < _borrowers.length; i++) {
  309              // Do not set when _approval == false and _borrower == msg.sender

src/contracts/FraxlendPairCore.sol:
  264          // Set approved borrowers
  265:         for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
  266              approvedBorrowers[_approvedBorrowers[i]] = true;

  269          // Set approved lenders
  270:         for (uint256 i = 0; i < _approvedLenders.length; ++i) {
  271              approvedLenders[_approvedLenders[i]] = true;

src/contracts/FraxlendPairDeployer.sol:
  401          uint256 _lengthOfArray = _addresses.length;
  402:         for (uint256 i = 0; i < _lengthOfArray; ) {
  403              _pairAddress = _addresses[i];

src/contracts/FraxlendWhitelist.sol:
  50      function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
  51:         for (uint256 i = 0; i < _addresses.length; i++) {
  52              oracleContractWhitelist[_addresses[i]] = _bool;

  65      function setRateContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
  66:         for (uint256 i = 0; i < _addresses.length; i++) {
  67              rateContractWhitelist[_addresses[i]] = _bool;

  80      function setFraxlendDeployerWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
  81:         for (uint256 i = 0; i < _addresses.length; i++) {
  82              fraxlendDeployerWhitelist[_addresses[i]] = _bool;

src/contracts/libraries/SafeERC20.sol:
  21          } else if (data.length == 32) {
  22:             uint8 i = 0;
  23              while (i < 32 && data[i] != 0) {
```

---------------------------------------------------------------------------


### [G-04] != 0 is cheaper than > 0

!= 0 costs less gas compared to > 0 for unsigned integers even when optimizer enabled
All of the following findings are uint (E&OE) so >0 and != have exactly the same effect.
** saves 6 gas ** each

I've found 9 locations in 3 files:

```
src/contracts/FraxlendPairCore.sol:
   476                  _totalAsset.amount += uint128(_interestEarned);
   477:                 if (_currentRateInfo.feeToProtocolRate > 0) {
   478                      _feesAmount = (_interestEarned * _currentRateInfo.feeToProtocolRate) / FEE_PRECISION;

   753          _updateExchangeRate();
   754:         if (_collateralAmount > 0) {
   755              _addCollateral(msg.sender, _collateralAmount, msg.sender);

   834          // Note: exchange rate is irrelevant when borrower has no debt shares
   835:         if (userBorrowShares[msg.sender] > 0) {
   836              _updateExchangeRate();

  1001                  uint128 _leftoverBorrowShares = _borrowerShares - _sharesToLiquidate;
  1002:                 if (_leftoverBorrowShares > 0) {
  1003                      // Write off bad debt

  1093          // Add initial collateral
  1094:         if (_initialCollateralAmount > 0) {
  1095              _addCollateral(msg.sender, _initialCollateralAmount, msg.sender);

src/contracts/FraxlendPairDeployer.sol:
  378              _penaltyRate,
  379:             _approvedBorrowers.length > 0,
  380:             _approvedLenders.length > 0
  381          );

src/contracts/FraxlendPairHelper.sol:
  234          (uint128 _totalAssetAmount, uint128 _totalAssetShares) = _fraxlendPair.totalAsset();
  235:         if (_feeToProtocolRate > 0) {
  236              _feesAmount = (_interestEarned * _feeToProtocolRate) / _FEE_PRECISION;

  291          // Determine if and how much debt to socialize
  292:         if (_leftoverCollateral <= 0 && (_borrowerShares - _sharesToLiquidate) > 0) {
  293              // Socialize bad debt
```

---------------------------------------------------------------------------

### [G-05] Custom errors save gas

From solidity 0.8.4 and above,
Custom errors from are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)
Source: https://blog.soliditylang.org/2021/04/21/custom-errors/:
```Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.```

I've found 5 locations in 1 file:

```
src/contracts/FraxlendPairDeployer.sol:
  204              bytes32 salt = keccak256(abi.encodePacked(_saltSeed, _configData));
  205:             require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
  206  

  227              }
  228:             require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
  229  

  252          );
  253:         require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
  254          deployedPairsByName[_name] = _pairAddress;

  364      ) external returns (address _pairAddress) {
  365:         require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
  366          require(

  398      function globalPause(address[] memory _addresses) external returns (address[] memory _updatedAddresses) {
  399:         require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");
  400          address _pairAddress;
```

