## FINDINGS

### Using immutable on variables that are only set in the constructor and never after 
Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD. Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

File: FraxlendPairDeployer.sol [Line 57](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L57)
```solidity
    address public CIRCUIT_BREAKER_ADDRESS;
```

File: FraxlendPairDeployer.sol [Line 58](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L58)
```solidity
    address public COMPTROLLER_ADDRESS;
```

File: FraxlendPairDeployer.sol [Line 59](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L59)
```solidity
    address public TIME_LOCK_ADDRESS;
```

### Using Constants on state variables that are never changed after 
The compiler does not reserve a storage slot for these variables, and every occurrence is replaced by the respective value.
Compared to regular state variables, the gas costs of constant  variables are much lower. For a constant variable, the expression assigned to it is copied to all the places where it is accessed and also re-evaluated each time. This allows for local optimizations

As evident from the comment , the following are meant to be constants but they were not declared using the constant keyword, as a result everytime they are accessed they still incur an SLOAD

File: FraxlendPairDeployer.sol [Line 49](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L49)
```solidity
    uint256 public DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision
```

File: FraxlendPairDeployer.sol [Line 50](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L50)
```solidity
    uint256 public GLOBAL_MAX_LTV = 1e8; // 1000x (100,000%) with 1e5 precision, protects from rounding errors in LTV calc
```

File: FraxlendPairDeployer.sol [Line 51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L51)
```solidity
    uint256 public DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision
```

With the keyword constant missing this are just normal state variables thus an SLOAD is incurred everytime they are accessed.

### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

NB: *The function has been truncated where neccessary to just show affected parts of the code*

FIle: FraxlendPairDeployer.sol [Line 310-343](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L310-L343)
### FraxlendPairDeployer.sol.deploy(): DEFAULT_MAX_LTV  and DEFAULT_LIQ_FEE should be cached(see audit tags)
**Note: from the comments(at declaration) this variables were meant to be constants but the keyword `constant`  was never used.

```solidity
    function deploy(bytes memory _configData) external returns (address _pairAddress) {
       _pairAddress = _deployFirst(
            keccak256(abi.encodePacked("public")),
            _configData,
            abi.encode(CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS, TIME_LOCK_ADDRESS, FRAXLEND_WHITELIST_ADDRESS),
            DEFAULT_MAX_LTV, //@audit: SLOAD 1 for  DEFAULT_MAX_LTV
            DEFAULT_LIQ_FEE, //@audit: SLOAD 1 for  DEFAULT_LIQ_FEE
            0,
            0,
            false,
            false
        );
        _logDeploy(_name, _pairAddress, _configData, DEFAULT_MAX_LTV, DEFAULT_LIQ_FEE, 0);//@audit: SLOAD 2 for  DEFAULT_MAX_LTV & DEFAULT_LIQ_FEE
    }
```
DEFAULT_MAX_LTV: SLOAD 1 [Line 332](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L332)
DEFAULT_MAX_LTV: SLOAD 2 [Line 342](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L342)

DEFAULT_LIQ_FEE: SLOAD 1 [Line 333](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L333)
DEFAULT_LIQ_FEE: SLOAD 2 [Line 342](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L342)


### Use calldata instead of memory for readonly function parameters
When arguments are read-only on external functions, the data location should be calldata:

File: FraxlendPairCore.sol [Line 1062](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1062-L1069)
`_path` is only being read as such we can declare it as **calldata**
```solidity
    function leveragedPosition(
        address _swapperAddress,
        uint256 _borrowAmount,
        uint256 _initialCollateralAmount,
        uint256 _amountCollateralOutMin,
        address[] memory _path
    )
        external
```

File: FraxlendPairDeployer.sol [Line 398-411](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L398-L411)
```solidity
    function globalPause(address[] memory _addresses) external returns (address[] memory _updatedAddresses) {
        require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");
        address _pairAddress;
        uint256 _lengthOfArray = _addresses.length;
        for (uint256 i = 0; i < _lengthOfArray; ) {
            _pairAddress = _addresses[i];
            try IFraxlendPair(_pairAddress).pause() {
                _updatedAddresses[i] = _addresses[i];
            } catch {}
            unchecked {
                i = i + 1;
            }
        }
    }
```

`address[] memory _addresses` should be modified to `address[] calldata _addresses`

File: FraxlendPairDeployer.sol [Line 355](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L355-L364)

`_name,_configData,_approvedBorrowers,_approvedLenders` should all be declared using calldata
```solidity
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

File: FraxlendPairDeployer.sol [Line 310](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L310)
`_configData` should be calldata
```solidity
    function deploy(bytes memory _configData) external returns (address _pairAddress) {
```

### Using bools for storage incurs overhead
 Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

**Instances affected include**
File: FraxlendWhitelist.sol [Line 32](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L32)
```solidity
    mapping(address => bool) public oracleContractWhitelist;
```

File: FraxlendWhitelist.sol [Line 35](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L35)
```solidity
    mapping(address => bool) public rateContractWhitelist;
```

File: FraxlendWhitelist.sol [Line 38](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L38)
```solidity
    mapping(address => bool) public fraxlendDeployerWhitelist;
```

File: FraxlendPairCore.sol [Line 78](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L78)
```solidity
    mapping(address => bool) public swappers; // approved swapper addresses
```

File:FraxlendPairCore.sol [Line 134](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L134)
```solidity
    mapping(address => bool) public approvedBorrowers;
```

File: FraxlendPairCore.sol [Line 137](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L137)
```solidity
    mapping(address => bool) public approvedLenders;
```

File: FraxlendPairDeployer.sol [Line 94](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L94)
```solidity
    mapping(address => bool) public deployedPairCustomStatusByAddress;
```

### Using private rather than public for constants, saves gas
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

File: FraxlendPairDeployer.sol [Line 49](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L49)
```solidity
    uint256 public DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision
```

### x += y costs more gas than x = x + y for state variables(sames goes for x -= y)

File: FraxlendPairCore.sol [Line 773](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L773)
```solidity
        totalCollateral += _collateralAmount;
```

File: FraxlendPairCore.sol [Line 772](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L772)
```solidity
        userCollateralBalance[_borrower] += _collateralAmount;
```
File: FraxlendPairCore.sol [Line 815](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L815)
```solidity
        totalCollateral -= _collateralAmount;
```

File: FraxlendPairCore.sol [Line 1008](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1008)
```solidity
                    totalAsset.amount -= _amountToAdjust;
```

File: FraxlendPairCore.sol [Line 867](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L867)
```solidity
        userBorrowShares[_borrower] -= _shares;
```

File: FraxlendPairCore.sol [Line 813](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L813)
```solidity
        userCollateralBalance[_borrower] -= _collateralAmount;
```
File: FraxlendPairCore.sol [Line 724](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L724)
```solidity
        userBorrowShares[msg.sender] += _sharesAdded;
```

### Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Use a larger size then downcast where needed [See source](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) 

File: FraxlendPairCore.sol [Line 707](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L707)
`uint128 _borrowAmount`
```solidity
    function _borrowAsset(uint128 _borrowAmount, address _receiver) internal returns (uint256 _sharesAdded) {
```

File: FraxlendPairCore.sol [Line 559-564](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L559-L564)
` uint128 _amount` and `uint128 _shares `
```solidity
    function _deposit(
        VaultAccount memory _totalAsset,
        uint128 _amount,
        uint128 _shares,
        address _receiver
    ) internal {
```

File: FraxlendPairCore.sol [Line 623-629](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L623-L629)
` uint128 _amountToReturn` and `uint128 _shares `
```solidity
    function _redeem(
        VaultAccount memory _totalAsset,
        uint128 _amountToReturn,
        uint128 _shares,
        address _receiver,
        address _owner
    ) internal {
```
File: FraxlendPairCore.sol [Line 855-858](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L855-L858)
` uint128 _amountToRepay` and `uint128 _shares `
```solidity
    function _repayAsset(
        VaultAccount memory _totalBorrow,
        uint128 _amountToRepay,
        uint128 _shares,
```

File: FraxlendPairCore.sol [Line 950](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L950-L953)
`uint128 _sharesToLiquidate`
```solidity
    function liquidateClean(
        uint128 _sharesToLiquidate,
        uint256 _deadline,
        address _borrower
```

File: FraxlendPair.sol [Line 215](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L215)
```solidity
    function changeFee(uint32 _newFee) external whenNotPaused {
```

File: FraxlendPair.sol [Line 234](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L234)
```solidity
    function withdrawFees(uint128 _shares, address _recipient) external onlyOwner returns (uint256 _amountToTransfer) {
```

### Splitting require() statements that use && saves gas - (saves 8 gas per &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).

File: LinearInterestRate.sol [Line 57-60](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L60)
```solidity
        require(
            _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
            "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
        );
```

File: LinearInterestRate.sol [Line 61-64](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L61-L64)
```solidity
        require(
            _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
            "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
        );
```

File: LinearInterestRate.sol [Line 65-68](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L65-L68)
```solidity
        require(
            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
        );
```

**Proof**
**The following tests were carried out in remix with both optimization turned on and off**

```solidity
function multiple (uint a) public pure returns (uint){
	require ( a > 1 && a < 5, "Initialized");
	return  a + 2;
}
```
**Execution cost**
21617 with optimization and using &&
21976 without optimization and using &&

**After splitting the require statement**
```solidity
function multiple(uint a) public pure returns (uint){
	require (a > 1 ,"Initialized");
	require (a < 5 , "Initialized");
	return a + 2;
}
```
**Execution cost**
21609 with optimization and split require
21968 without optimization and using split require

### Comparisons: != is more efficient than > in require (6 gas less)
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)

For uints the minimum value would be 0 and never a negative value. Since it cannot be a negative value, then the check > 0 is essentially checking that the value is not equal to 0 therefore >0 can be replaced with !=0 which saves gas.

I suggest changing > 0 with != 0 here:

File: LinearInterestRate.sol [Line 65-68](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L65-L68)
`_vertexUtilization > 0`
```solidity
        require(_vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0 "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0");
```

### Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

File: VariableInterestRate.sol [Line 69](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L69)
```solidity
            uint256 _deltaUtilization = ((MIN_UTIL - _utilization) * 1e18) / MIN_UTIL;
```

The operation `MIN_UTIL - _utilization` cannot underflow due to the check on [Line 68](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L68) which ensures that `MIN_UTIL` is greater than `_utilization` before perfoming the subtraction operation

File: VariableInterestRate.sol [Line 76](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L76)
```solidity
            uint256 _deltaUtilization = ((_utilization - MAX_UTIL) * 1e18) / (UTIL_PREC - MAX_UTIL);
```
The operation `_utilization - MAX_UTIL` cannot underflow due to the check on [Line 75](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L75) which ensures that `_utilization` is greater than `MAX_UTIL` before perfoming the subtraction operation

### Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop .

**Affected code**
File: FraxlendWhitelist.sol [Line 50-55](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L50-L55)
```solidity
    function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
        for (uint256 i = 0; i < _addresses.length; i++) {
            oracleContractWhitelist[_addresses[i]] = _bool;
            emit SetOracleWhitelist(_addresses[i], _bool);
        }
    }
```
The above should be modified to:
```solidity
    function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
        for (uint256 i = 0; i < _addresses.length;) {
            oracleContractWhitelist[_addresses[i]] = _bool;
            emit SetOracleWhitelist(_addresses[i], _bool);
	    unchecked{
				++i;
	    }
        }
    }
```

**Other Instances to modify**
File: FraxlendWhitelist.sol [Line 65-70](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L65-L70)

File: FraxlendWhitelist.sol [Line 81](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81)

File: FraxlendPairCore.sol [Line 265](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265-L267)

File: FraxlendPairCore.sol [Line 270](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270)

File: FraxlendPair.sol [Line 289](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289)

File: FraxlendPair.sol [Line 308](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308)

[see resource](https://github.com/ethereum/solidity/issues/10695)

### Cache the length of arrays in loops (saves ~6 gas per iteration)
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

The solidity compiler will always read the length of the array during each iteration. That is,

   1.if it is a storage array, this is an extra sload operation (100 additional extra gas (EIP-2929 2) for each iteration except for the first)
   2.if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first)
   3.if it is a calldata array, this is an extra calldataload operation (3 additional gas for each iteration except for the first)

This extra costs can be avoided by caching the array length (in stack):
 When reading the length of an array,  **sload** or **mload** or **calldataload** operation is only called once and subsequently replaced by a cheap **dupN** instruction. Even though mload , calldataload and dupN have the same gas cost, mload and calldataload needs an additional dupN to put the offset in the stack, i.e., an extra 3 gas. which brings this to 6 gas
 
Here, I suggest storing the array’s length in a variable before the for-loop, and use it instead:

File: FraxlendWhitelist.sol [Line 50-55](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L50-L55)
```solidity
    function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
        for (uint256 i = 0; i < _addresses.length; i++) {
            oracleContractWhitelist[_addresses[i]] = _bool;
            emit SetOracleWhitelist(_addresses[i], _bool);
        }
    }
```

**The above should be modified to**
```solidity
    function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
				uint256 length = _addresses.length;
        for (uint256 i = 0; i < length; i++) {
            oracleContractWhitelist[_addresses[i]] = _bool;
            emit SetOracleWhitelist(_addresses[i], _bool);
        }
    }
```

**Other instances to modify**
File: FraxlendWhitelist.sol [Line 66](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66)

File: FraxlendWhitelist.sol [Line 81](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81)

File: FraxlendPairCore.sol [Line 265](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265-L267)

File: FraxlendPairCore.sol [Line 270](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270)

File: FraxlendPair.sol [Line 289](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289)

File: FraxlendPair.sol [Line 308](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308)

### ++i costs less gas compared to i++ or i += 1  (~5 gas per iteration)

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i. Which means:

```solidity
uint i = 1;  
i++; // == 1 but i == 2  
```

But ++i returns the actual incremented value:

```solidity
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

Instances include:
File: FraxlendWhitelist.sol [Line 51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51)
```solidity
        for (uint256 i = 0; i < _addresses.length; i++) {
```
File: FraxlendPairDeployer.sol [Line 158](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L158)

File: FraxlendWhitelist.sol [Line 66](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66)

File: FraxlendWhitelist.sol [Line 81](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81)

File: FraxlendPair.sol [Line 289](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289)

File: FraxlendPair.sol [Line 308](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308)

### use shorter revert strings(less than 32 bytes) 
Every reason string takes at least 32 bytes so make sure your string fits in 32 bytes or it will become more expensive.Each extra chunk of byetes past the original 32 incurs an MSTORE which costs 3 gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

File: LinearInterestRate.sol [Line 57-60](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L60)
```solidity
        require(
            _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
            "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
        );
```

**Other instances to modify**
File: LinearInterestRate.sol [Line 61-64](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L61-L64)

File: LinearInterestRate.sol [Line 65-68](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L65-L68)

File: FraxlendPairDeployer.sol [Line 205](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L205)

FIle: FraxlendPairDeployer.sol [Line 228](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L228)

File: FraxlendPairDeployer.sol [Line 253](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L253)

File: FraxlendPairDeployer.sol [Line 365,366](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L365-L366)

I suggest shortening the revert strings to fit in 32 bytes, or using custom errors.

### Use Custom Errors instead of Revert Strings to save Gas
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)
Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
see [Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

File: LinearInterestRate.sol [Line 57-60](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L60)
```solidity
        require(
            _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
            "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
        );
```

**Other instances to modify**
File: LinearInterestRate.sol [Line 61-64](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L61-L64)

File: LinearInterestRate.sol [Line 65-68](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L65-L68)

File: FraxlendPairDeployer.sol [Line 205](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L205)

FIle: FraxlendPairDeployer.sol [Line 228](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L228)

File: FraxlendPairDeployer.sol [Line 253](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L253)

File: FraxlendPairDeployer.sol [Line 365,366](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L365-L366)

**Proof of custom errors optimizations**

```solidity
contract GasTest is DSTest {
    Contract0 c0;
    Contract1 c1;

    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }

    function testFailGas() public {
        c0.stringErrorMessage();
        c1.customErrorMessage();
    }
}

contract Contract0 {
    function stringErrorMessage() public {
        bool check = false;
        require(check, "error message");
    }
}

contract Contract1 {
    error CustomError();

    function customErrorMessage() public {
        bool check = false;
        if (!check) {
            revert CustomError();
        }
    }
}
```

**Gas comparisons**
```solidity
╭────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ Contract0 contract ┆                 ┆     ┆        ┆     ┆         │
╞════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost    ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 34087              ┆ 200             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name      ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ stringErrorMessage ┆ 218             ┆ 218 ┆ 218    ┆ 218 ┆ 1       │
╰────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
╭────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost    ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 26881              ┆ 164             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name      ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ customErrorMessage ┆ 161             ┆ 161 ┆ 161    ┆ 161 ┆ 1       │
╰────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
```
