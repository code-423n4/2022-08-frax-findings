1)++i costs less gas than i++, especially when it’s used in for-loops (--i/i-- too)

Saves 6 gas PER LOOP

File: 2022-08-frax\src\contracts\FraxlendPair.sol
  289,50:         for (uint256 i = 0; i < _lenders.length; i++) {
  308,52:         for (uint256 i = 0; i < _borrowers.length; i++) {
  
File: 2022-08-frax\src\contracts\FraxlendWhitelist.sol
  51,52:         for (uint256 i = 0; i < _addresses.length; i++) {
  66,52:         for (uint256 i = 0; i < _addresses.length; i++) {
  81,52:         for (uint256 i = 0; i < _addresses.length; i++) {

File: 2022-08-frax\src\contracts\libraries\SafeERC20.sol
  27,49:             for (i = 0; i < 32 && data[i] != 0; i++) {  
  
2)It costs more gas to initialize variables to zero than to let the default of zero be applied

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {  
  
File: 2022-08-frax\src\contracts\FraxlendPair.sol
  289,50:         for (uint256 i = 0; i < _lenders.length; i++) {
  308,52:         for (uint256 i = 0; i < _borrowers.length; i++) {
  
File: 2022-08-frax\src\contracts\FraxlendPairCore.sol
  265,9:         for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
  270,9:         for (uint256 i = 0; i < _approvedLenders.length; ++i) {  
  
File: 2022-08-frax\src\contracts\FraxlendWhitelist.sol
  51,52:         for (uint256 i = 0; i < _addresses.length; i++) {
  66,52:         for (uint256 i = 0; i < _addresses.length; i++) {
  81,52:         for (uint256 i = 0; i < _addresses.length; i++) {
  
File: 2022-08-frax\src\contracts\FraxlendPairDeployer.sol
  402,9:         for (uint256 i = 0; i < _lengthOfArray; ) {

File: 2022-08-frax\src\contracts\libraries\SafeERC20.sol

  27,49:             for (i = 0; i < 32 && data[i] != 0; i++) {  

3)<ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset
  
File: 2022-08-frax\src\contracts\FraxlendPair.sol
  289,50:         for (uint256 i = 0; i < _lenders.length; i++) {
  308,52:         for (uint256 i = 0; i < _borrowers.length; i++) {
  
File: 2022-08-frax\src\contracts\FraxlendPairCore.sol
  265,9:         for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
  270,9:         for (uint256 i = 0; i < _approvedLenders.length; ++i) {  
  
File: 2022-08-frax\src\contracts\FraxlendWhitelist.sol
  51,52:         for (uint256 i = 0; i < _addresses.length; i++) {
  66,52:         for (uint256 i = 0; i < _addresses.length; i++) {
  81,52:         for (uint256 i = 0; i < _addresses.length; i++) {

4) ++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow,
as is the case when used in for- and while-loops

File: 2022-08-frax\src\contracts\FraxlendPair.sol
  289,50:         for (uint256 i = 0; i < _lenders.length; i++) {
  308,52:         for (uint256 i = 0; i < _borrowers.length; i++) {
  
File: 2022-08-frax\src\contracts\FraxlendPairCore.sol
  265,9:         for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
  270,9:         for (uint256 i = 0; i < _approvedLenders.length; ++i) {  
  
File: 2022-08-frax\src\contracts\FraxlendWhitelist.sol
  51,52:         for (uint256 i = 0; i < _addresses.length; i++) {
  66,52:         for (uint256 i = 0; i < _addresses.length; i++) {
  81,52:         for (uint256 i = 0; i < _addresses.length; i++) {
    
File: 2022-08-frax\src\contracts\libraries\SafeERC20.sol

  27,49:             for (i = 0; i < 32 && data[i] != 0; i++) {  
  
5)Use custom errors rather than revert()/require() strings to save gas

File: 2022-08-frax\src\contracts\FraxlendPairDeployer.sol
  205,13:             require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
  228,13:             require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
  253,9:         require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
  365,9:         require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
  366,9:         require(
  399,9:         require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");


File: 2022-08-frax\src\contracts\LinearInterestRate.sol
  57,9:         require(_minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
            "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
        );
  61,9:         require(_maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
            "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
        );
  65,9:         require(_vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
        );
		
6)X = X + Y IS CHEAPER THAN X += Y 		
File: 2022-08-frax\src\contracts\FraxlendPairCore.sol
  475,37:                 _totalBorrow.amount += uint128(_interestEarned);
  476,36:                 _totalAsset.amount += uint128(_interestEarned);
  484,40:                     _totalAsset.shares += uint128(_feesShare);
  566,28:         _totalAsset.amount += _amount;
  567,28:         _totalAsset.shares += _shares;
  718,29:         _totalBorrow.amount += _borrowAmount;
  719,29:         _totalBorrow.shares += uint128(_sharesAdded);
  724,38:         userBorrowShares[msg.sender] += _sharesAdded;
  772,42:         userCollateralBalance[_borrower] += _collateralAmount;
  773,25:         totalCollateral += _collateralAmount;		


 

  
  
  
  
