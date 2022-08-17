## Table of Contents
- Storage Variables can be Packed into Fewer Storage Slots
- Use require instead of &&
- Use Calldata instead of Memory for Read Only Function Parameters
- Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas
- Unnecessary Default Value Initialization
- ++i Costs Less Gas than i++
- Empty Blocks Should Emit Something or be Removed
- Keep The Revert Strings of Error Messages within 32 Bytes
- Use Custom Errors to Save Gas

&ensp;
### Storage Variables can be Packed into Fewer Storage Slots

#### Issue
The order of storage variables can be reordered in a way to reduce the usage amount of storage slots.
```
Reference from solidity documentation:
Finally, in order to allow the EVM to optimize for this, ensure that you try to order your storage 
variables and struct members such that they can be packed tightly. For example, declaring your 
storage variables in the order of uint128, uint128, uint256 instead of uint128, uint256, uint128, 
as the former will only take up two slots of storage whereas the latter will take up three.
```
https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html#layout-of-state-variables-in-storage

#### PoC
Total of 1 issue found.

1. FraxlendPairCore.sol
We can save 2 storage slot by reordering it like below.
Move 2 bool variable (1 byte size each) to bottom of address variable (20 bytes size).
By doing this, these 2 bool variables and 1 address variable will be packed to 1 slot (32 bytes size).

Move 2 bool variabe at line 133 and 136 to bottom of address variable at line 86.

```solidity
    address public TIME_LOCK_ADDRESS;
    bool public immutable borrowerWhitelistActive;
    bool public immutable lenderWhitelistActive;
```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L86
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L133
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L136

#### Mitigation
Reorder storage variables like shown in above PoC.

&ensp;
### Use require instead of &&

#### Issue
When there are multiple conditions in require statement, break down the require statement into
multiple require statements instead of using && can save gas.

#### PoC
Total of 3 instances found.
```
./LinearInterestRate.sol:57:        require(_minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
                                    "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT");
./LinearInterestRate.sol:61:        require(_maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
                                    "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT");
./LinearInterestRate.sol:65:        require(_vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
                                    "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0");
```

#### Mitigation
Break down into several require statement.
For example,
```
require(_vertexUtilization < MAX_VERTEX_UTIL);
require(_vertexUtilization > 0, "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0");
```

&ensp;
### Use Calldata instead of Memory for Read Only Function Parameters

#### Issue
It is cheaper gas to use calldata than memory if the function parameter is read only.
Calldata is a non-modifiable, non-persistent area where function arguments are stored, 
and behaves mostly like memory. More details on following link.
link: https://docs.soliditylang.org/en/v0.8.15/types.html#data-location

#### PoC
Total of 7 instances found.
```
./FraxlendPairDeployer.sol:310:    function deploy(bytes memory _configData) external returns (address _pairAddress) {
./FraxlendPairDeployer.sol:356:        string memory _name,
./FraxlendPairDeployer.sol:357:        bytes memory _configData,
./FraxlendPairDeployer.sol:362:        address[] memory _approvedBorrowers,
./FraxlendPairDeployer.sol:363:        address[] memory _approvedLenders
./FraxlendPairDeployer.sol:398:    function globalPause(address[] memory _addresses) external returns (address[] memory _updatedAddresses) {
./FraxlendPairCore.sol:1067:        address[] memory _path
```

#### Mitigation
Change memory to calldata

&ensp;
### Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas

#### Issue
Since EVM operates on 32 bytes at a time, if the element is smaller than that, the EVM must use more operations
in order to reduce the elements from 32 bytes to specified size.

Reference: https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html

#### PoC
Total of 25 instances found.
```
./SafeERC20.sol:22:            uint8 i = 0;
./SafeERC20.sol:55:    function safeDecimals(IERC20 token) internal view returns (uint8) {
./SafeERC20.sol:57:        return success && data.length == 32 ? abi.decode(data, (uint8)) : 18;
./FraxlendPair.sol:84:    function decimals() public pure override(ERC20, IERC20Metadata) returns (uint8) {
./FraxlendPair.sol:137:        return type(uint128).max;
./FraxlendPair.sol:141:        return type(uint128).max;
./FraxlendPair.sol:165:            uint64 _DEFAULT_INT,
./FraxlendPair.sol:166:            uint16 _DEFAULT_PROTOCOL_FEE,
./FraxlendPair.sol:211:    event ChangeFee(uint32 _newFee);
./FraxlendPair.sol:215:    function changeFee(uint32 _newFee) external whenNotPaused {
./VariableInterestRate.sol:63:    function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {
./FraxlendPairCore.sol:400:            uint64 _newRate
./FraxlendPairCore.sol:415:            uint64 _newRate
./FraxlendPairCore.sol:561:        uint128 _amount,
./FraxlendPairCore.sol:562:        uint128 _shares,
./FraxlendPairCore.sol:625:        uint128 _amountToReturn,
./FraxlendPairCore.sol:626:        uint128 _shares,
./FraxlendPairCore.sol:857:        uint128 _amountToRepay,
./FraxlendPairCore.sol:858:        uint128 _shares,
./FraxlendPairCore.sol:951:        uint128 _sharesToLiquidate,
./FraxlendPairCore.sol:967:        uint128 _borrowerShares = userBorrowShares[_borrower].toUint128();
./FraxlendPairCore.sol:993:        uint128 _amountLiquidatorToRepay = (_totalBorrow.toAmount(_sharesToLiquidate, true)).toUint128();
./FraxlendPairCore.sol:997:        uint128 _sharesToAdjust;
./FraxlendPairCore.sol:998:        uint128 _amountToAdjust;
./LinearInterestRate.sol:76:    function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {
```

#### Mitigation
I suggest using uint256 instead of anything smaller or downcast where needed.

&ensp;
### Unnecessary Default Value Initialization

#### Issue
When variable is not initialized, it will have its default values.
For example, 0 for uint, false for bool and address(0) for address.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#scoping-and-declarations

#### PoC
Total of 14 instances found.
```
./FraxlendPairDeployer.sol:127:        for (i = 0; i < _lengthOfArray; ) {
./FraxlendPairDeployer.sol:152:        for (i = 0; i < _lengthOfArray; ) {
./FraxlendPairDeployer.sol:402:        for (uint256 i = 0; i < _lengthOfArray; ) {
./SafeERC20.sol:22:            uint8 i = 0;
./SafeERC20.sol:27:            for (i = 0; i < 32 && data[i] != 0; i++) {
./FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
./FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
./FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
./FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
./FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
./FraxlendPairConstants.sol:47:    uint16 internal constant DEFAULT_PROTOCOL_FEE = 0; // 1e5 precision
./FraxlendPairCore.sol:265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
./FraxlendPairCore.sol:270:        for (uint256 i = 0; i < _approvedLenders.length; ++i) {
./LinearInterestRate.sol:33:    uint256 private constant MIN_INT = 0; // 0.00% annual rate
```

#### Mitigation
I suggest removing default value initialization.
For example,
- for (i; i < _lengthOfArray; ) {

&ensp;
### ++i Costs Less Gas than i++

#### Issue
Prefix increments/decrements (++i or --i) costs cheaper gas than 
postfix increment/decrements (i++ or i--).

#### PoC
Total of 9 instances found.
```
./FraxlendPairDeployer.sol:130:                i++;
./FraxlendPairDeployer.sol:158:                i++;
./SafeERC20.sol:24:                i++;
./SafeERC20.sol:27:            for (i = 0; i < 32 && data[i] != 0; i++) {
./FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
./FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
./FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
./FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
./FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
```

#### Mitigation
Change it to postfix increments/decrements.
It saves 6 gas per loop.

&ensp;
### Empty Blocks Should Emit Something or be Removed

#### Issue
There are function with empty blocks. These should do something like emit an event or reverting.
If not it should be removed from the contract.

#### PoC
Total of 3 instances found.
```solidity
./FraxlendPairDeployer.sol:406:            } catch {}
./VariableInterestRate.sol:57:    function requireValidInitData(bytes calldata _initData) external pure {}
./FraxlendWhitelist.sol:40:    constructor() Ownable() {}
```

#### Mitigation
Add emit or revert in the function block.

&ensp;
### Keep The Revert Strings of Error Messages within 32 Bytes

#### Issue
Since each storage slot is size of 32 bytes, error messages that is longer than this will need
extra storage slot leading to more gas cost.

#### PoC
Total of 8 instances found.
```
./FraxlendPairDeployer.sol:205:            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
./FraxlendPairDeployer.sol:228:            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
./FraxlendPairDeployer.sol:253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
./FraxlendPairDeployer.sol:365:        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
./FraxlendPairDeployer.sol:366:        require(IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
                                       "FraxlendPairDeployer: Only whitelisted addresses");
./LinearInterestRate.sol:57:        require(_minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
                                    "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT");
./LinearInterestRate.sol:61:        require(_maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
                                    "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT");
./LinearInterestRate.sol:65:        require(_vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
                                    "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0");
```

#### Mitigation
Simply keep the error messages within 32 bytes to avoid extra storage slot cost.

&ensp;
### Use Custom Errors to Save Gas

#### Issue
Custom errors from Solidity 0.8.4 are cheaper than revert strings.
Details are explained here: https://blog.soliditylang.org/2021/04/21/custom-errors/

#### PoC
Total of 9 instances found.
```
./FraxlendPairDeployer.sol:205:            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
./FraxlendPairDeployer.sol:228:            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
./FraxlendPairDeployer.sol:253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
./FraxlendPairDeployer.sol:365:        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
./FraxlendPairDeployer.sol:366:        require(IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
                                       "FraxlendPairDeployer: Only whitelisted addresses");
./FraxlendPairDeployer.sol:399:        require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");
./LinearInterestRate.sol:57:        require(_minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
                                    "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT");
./LinearInterestRate.sol:61:        require(_maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
                                    "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT");
./LinearInterestRate.sol:65:        require(_vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
                                    "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0");
```

#### Mitigation
I suggest implementing custom errors to save gas.

&ensp;