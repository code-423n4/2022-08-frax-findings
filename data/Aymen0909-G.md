# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1             | Multiple address mappings can be combined into a single mapping of an address to a struct     | 2      |
| 2      | Use custom errors rather than **require()/revert()** strings to save deployments gas  |  9 |
| 3      | Splitting **require()** statements that uses && saves gas  |  3 |
| 4      | **require()/revert()** strings longer than 32 bytes cost extra gas     |  8  |
| 5      | Use **calldata** instead of **memory** for function parameters type  |  5 |
| 6      | It costs more gas to initialize variables to zero than to let the default of zero be applied    |  14  |
| 7      | Use of **++i** cost less gas than **i++/i=i+1** in for loops    |  9  |
| 8      | **++i/i++** should be **unchecked{++i}/unchecked{i++}** when it is not possible for them to overflow, as in the case when used in for & while loops |  8  |
| 9      | **X += Y/X -= Y** costs more gas than **X = X + Y/X = X - Y** for state variables  |  6 |
| 10      | Functions guaranteed to revert when called by normal users can be marked payable |  9 |


## Findings

### 1- Multiple address mappings can be combined into a single mapping of an address to a struct :

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

There are 2 instances of this issue :

```
File: contracts/FraxlendPairCore.sol

127      mapping(address => uint256) public userCollateralBalance;
129      mapping(address => uint256) public userBorrowShares;
```

These mappings could be refactored into the following struct and mapping for example :

```
struct UserInfo {
        uint256 collateralBalance;
        uint256 borrowShares;
    }
mapping(address => UserInfo) public usersInfo;
```

### 2- Use custom errors rather than **require()**/**revert()** strings to save deployments gas :

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information.

There are 9 instances of this issue :

```
File: contracts/FraxlendPairDeployer.sol

205      require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
228      require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
253      require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
365      require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
367      require(IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRES).fraxlendDeployerWhitelist(msg.sender), "FraxlendPairDeployer: Only whitelisted addresses");
399      require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");

File: contracts/LinearInterestRate.sol

57      require(_minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT, "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT");
61      require(_maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT, "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT");
65      require(_vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0, "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0");
```

### 3- Splitting **require()** statements that uses && saves gas :

There are 3 instances of this issue :

```
File: contracts/LinearInterestRate.sol

57      require(_minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT, "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT");
61      require(_maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT, "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT");
65      require(_vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0, "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0");
```

### 4- **require()**/**revert()** strings longer than 32 bytes cost extra gas :

Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**.

There are 8 instances of this issue :

```
File: contracts/FraxlendPairDeployer.sol

205      require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
228      require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
253      require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
365      require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
367      require(IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRES).fraxlendDeployerWhitelist(msg.sender), "FraxlendPairDeployer: Only whitelisted addresses");

File: contracts/LinearInterestRate.sol

57      require(_minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT, "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT");
61      require(_maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT, "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT");
65      require(_vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0, "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0");
```

### 5- Use **calldata** instead of **memory** for function parameters type :

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

There are 5 instances of this issue:

```
File: contracts/FraxlendPairCore.sol

1062     function leveragedPosition(address _swapperAddress, uint256 _borrowAmount, uint256 _initialCollateralAmount, uint256 _amountCollateralOutMin, address[] memory _path)

File: contracts/FraxlendPairDeployer.sol

242      function _deploySecond(string memory _name, address _pairAddress, bytes memory _configData, address[] memory _approvedBorrowers, address[] memory _approvedLenders) private
272      function _logDeploy(string memory _name, address _pairAddress, bytes memory _configData, uint256 _maxLTV, uint256 _liquidationFee, uint256 _maturityDate) private
355      function deployCustom(string memory _name, bytes memory _configData, uint256 _maxLTV, uint256 _liquidationFee, uint256 _maturityDate, uint256 _penaltyRate, address[] memory _approvedBorrowers, address[] memory _approvedLenders) external returns (address _pairAddres)
398      function globalPause(address[] memory _addresses) external returns (address[] memory _updatedAddresses)
```

### 6- It costs more gas to initialize variables to zero than to let the default of zero be applied :

If a variable is not set/initialized, it is assumed to have the default value (0 for uint or int, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

There are 14 instances of this issue:

```
File: contracts/libraries/SafeERC20.sol

22     uint8 i = 0;
27     for (i = 0; i < 32 && data[i] != 0; i++)

File: contracts/FraxlendPair.sol

289     for (uint256 i = 0; i < _lenders.length; i++)
308     for (uint256 i = 0; i < _borrowers.length; i++)

File: contracts/FraxlendPairConstants.sol

47      uint16 internal constant DEFAULT_PROTOCOL_FEE = 0;

File: contracts/FraxlendPairCore.sol

265     for (uint256 i = 0; i < _approvedBorrowers.length; ++i)
270     for (uint256 i = 0; i < _approvedLenders.length; ++i)

File: contracts/FraxlendPairDeployer.sol

127     for (i = 0; i < _lengthOfArray; )
152     for (i = 0; i < _lengthOfArray; )
402     for (uint256 i = 0; i < _lengthOfArray; )

File: contracts/FraxlendWhitelist.sol

51      for (uint256 i = 0; i < _addresses.length; i++)
66      for (uint256 i = 0; i < _addresses.length; i++)
81      for (uint256 i = 0; i < _addresses.length; i++) 

File: contracts/LinearInterestRate.sol

33      uint256 private constant MIN_INT = 0;
```

### 7- Use of ++i cost less gas than i++/i=i+1 in for loops :

There are 9 instances of this issue:

```
File: contracts/libraries/SafeERC20.sol

27     for (i = 0; i < 32 && data[i] != 0; i++)

File: contracts/FraxlendPair.sol

289     for (uint256 i = 0; i < _lenders.length; i++)
308     for (uint256 i = 0; i < _borrowers.length; i++)

File: contracts/FraxlendPairDeployer.sol

138     i++;
158     i++;
408     i = i + 1;

File: contracts/FraxlendWhitelist.sol

51      for (uint256 i = 0; i < _addresses.length; i++)
66      for (uint256 i = 0; i < _addresses.length; i++)
81      for (uint256 i = 0; i < _addresses.length; i++) 
```

### 8- ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 8 instances of this issue:

```
File: contracts/libraries/SafeERC20.sol

27     for (i = 0; i < 32 && data[i] != 0; i++)

File: contracts/FraxlendPair.sol

289     for (uint256 i = 0; i < _lenders.length; i++)
308     for (uint256 i = 0; i < _borrowers.length; i++)

File: contracts/FraxlendPairCore.sol

265     for (uint256 i = 0; i < _approvedBorrowers.length; ++i)
270     for (uint256 i = 0; i < _approvedLenders.length; ++i)

File: contracts/FraxlendWhitelist.sol

51      for (uint256 i = 0; i < _addresses.length; i++)
66      for (uint256 i = 0; i < _addresses.length; i++)
81      for (uint256 i = 0; i < _addresses.length; i++)
```


### 9- **X += Y/X -= Y** costs more gas than **X = X + Y/X = X - Y** for state variables :

There are 6 instances of this issue:

```
File: contracts/FraxlendPairCore.sol

724     userBorrowShares[msg.sender] += _sharesAdded;
772     userCollateralBalance[_borrower] += _collateralAmount;
773     totalCollateral += _collateralAmount;
813     userCollateralBalance[_borrower] -= _collateralAmount;
815     totalCollateral -= _collateralAmount;
867     userBorrowShares[_borrower] -= _shares;
```

### 10- Functions guaranteed to revert when called by normal users can be marked **payable** :

If a function modifier such as **onlyOwner** is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for the owner because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are : 

CALLVALUE(gas=2), DUP1(gas=3), ISZERO(gas=3), PUSH2(gas=3), JUMPI(gas=10), PUSH1(gas=3), DUP1(gas=3), REVERT(gas=0), JUMPDEST(gas=1), POP(gas=2). 
Which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There are 9 instances of this issue:

```
File: contracts/FraxlendPair.sol

204     function setTimeLock(address _newAddress) external onlyOwner
234     function withdrawFees(uint128 _shares, address _recipient) external onlyOwner returns (uint256 _amountToTransfer)
274     function setSwapper(address _swapper, bool _approval) external onlyOwner 
288     function setApprovedLenders(address[] calldata _lenders, bool _approval) external approvedLender(msg.sender)
307     function setApprovedBorrowers(address[] calldata _borrowers, bool _approval) external approvedBorrower  
317     function pause() external
330     function unpause() external

File: contracts/FraxlendWhitelist.sol

50      function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner
65      function setRateContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner
80      function setFraxlendDeployerWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner

File: contracts/FraxlendPairDeployer.sol

170     function setCreationCode(bytes calldata _creationCode) external onlyOwner
```
