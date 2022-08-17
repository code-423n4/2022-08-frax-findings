## you can make this bytes3 to save gas 
`  string public version = "1.0.0";`
its waste of 29bytes  you can save and add other stuff like symbol.
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L51
##   Using bools for storage incurs overhead
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L78
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L133-L137
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendWhitelist.sol#L32-L38
##    Use Custom Errors instead of Revert Strings to save Gas
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) Source Custom Errors in Solidity: Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them. Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
```
LinearInterestRate.sol:57:        require(
LinearInterestRate.sol:61:        require(
LinearInterestRate.sol:65:        require(
FraxlendPairDeployer.sol:205:            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
FraxlendPairDeployer.sol:228:            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
FraxlendPairDeployer.sol:253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
FraxlendPairDeployer.sol:365:        require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
FraxlendPairDeployer.sol:366:        require(
FraxlendPairDeployer.sol:399:        require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");


```
## make address(0) into long form :0x000000000000
to save gas 
```
FraxlendPairHelper.sol:132:        if (_oracleMultiply != address(0)) {
FraxlendPairHelper.sol:140:        if (_oracleDivide != address(0)) {
FraxlendPairDeployer.sol:205:            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
FraxlendPairDeployer.sol:228:            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
FraxlendPairDeployer.sol:253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");

```
## USE ASSEMBLY TO CHECK FOR ADDRESS(0)
```
FraxlendPairHelper.sol:132:        if (_oracleMultiply != address(0)) {
FraxlendPairHelper.sol:140:        if (_oracleDivide != address(0)) {
FraxlendPairDeployer.sol:205:            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
FraxlendPairDeployer.sol:228:            require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
FraxlendPairDeployer.sol:253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");

```

## FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.
```
FraxlendPairDeployer.sol:170:    function setCreationCode(bytes calldata _creationCode) external onlyOwner {
FraxlendPair.sol:204:    function setTimeLock(address _newAddress) external onlyOwner {
FraxlendPair.sol:234:    function withdrawFees(uint128 _shares, address _recipient) external onlyOwner returns (uint256 _amountToTransfer) {
FraxlendPair.sol:274:    function setSwapper(address _swapper, bool _approval) external onlyOwner {
FraxlendWhitelist.sol:50:    function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
FraxlendWhitelist.sol:65:    function setRateContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
FraxlendWhitelist.sol:80:    function setFraxlendDeployerWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {



```


## Reduce the size of error messages (Long revert Strings)
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met. Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.
1 byte for character
```
FraxlendPairDeployer.sol:205:            require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
FraxlendPairDeployer.sol:253:        require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");

```
## make i++ into ++i to save 3 gas on mcopy 
```
libraries/SafeERC20.sol:24:                i++;
libraries/SafeERC20.sol:27:            for (i = 0; i < 32 && data[i] != 0; i++) {
FraxlendPairDeployer.sol:130:                i++;
FraxlendPairDeployer.sol:158:                i++;
FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
```
## make array.length cached to save gas 
```
FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
```
## Unchecking arithmetics operations that can’t underflow/overflow
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block: Checked or Unchecked Arithmetic. I suggest wrapping with an unchecked block:
Same thing with second unchecked because total can't overflow amount cant overflow

`Unchecked { uint256 total = amount + hasMinted[msg.sender]; }`
```
FraxlendPair.sol:289:        for (uint256 i = 0; i < _lenders.length; i++) {
FraxlendPair.sol:308:        for (uint256 i = 0; i < _borrowers.length; i++) {
FraxlendWhitelist.sol:51:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:66:        for (uint256 i = 0; i < _addresses.length; i++) {
FraxlendWhitelist.sol:81:        for (uint256 i = 0; i < _addresses.length; i++) {
```
