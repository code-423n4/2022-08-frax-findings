### Gas Optimizations
|        | Finding                                                                                                      |  Instances  |
|:-------|:-------------------------------------------------------------------------------------------------------------|:-----------:|
| [G-01] | Variable should be made a `constant`                                                                         |      3      |
| [G-02] | Using prefix(`++i`) instead of postfix (`i++`)  saves gas                                                    |      5      |
| [G-03] | Setting variable to default value is redundant                                                               |      9      |
| [G-04] | `for` loop increments should be `unchecked{}` if overflow is not possible                                    |      5      |
| [G-05] | `array.length` should be cached in `for` loop                                                                |      7      |
| [G-06] | Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate |      2      |
| [G-07] | Avoid using `bool` for storage                                                                               |      5      |
| [G-08] | Function could accept array for `bool`                                                                       |      3      |
### Gas overview per contract
| Contract                                                                                                                                                    |  Instances  |  Gas Ops  |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------:|:---------:|
| [FraxlendWhitelist.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol)       |     18      |     6     |
| [FraxlendPairCore.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol)         |     11      |     6     |
| [FraxlendPair.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol)                 |      6      |     3     |
| [FraxlendPairDeployer.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol) |      4      |     2     |
## Gas Optimizations
### [G-01] Variable should be made a `constant`
A variable that is not changed should be made `constant` in order to save gas.
*3 instances of this issue have been found:*
###### [G-01] [FraxlendPairDeployer.sol#L49-L51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L49-L51)
```solidity

    uint256 public DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision
    uint256 public GLOBAL_MAX_LTV = 1e8; // 1000x (100,000%) with 1e5 precision, protects from rounding errors in LTV calc
    uint256 public DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision
```
###### [G-01b] [FraxlendPairDeployer.sol#L57-L59](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L57-L59)
```solidity

    address public CIRCUIT_BREAKER_ADDRESS;
    address public COMPTROLLER_ADDRESS;
```
###### [G-01c] [FraxlendPairCore.sol#L86-L87](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L86-L87)
```solidity

    address public TIME_LOCK_ADDRESS;

```
### [G-02] Using prefix(`++i`) instead of postfix (`i++`)  saves gas
It saves 6 gas per iteration.
*5 instances of this issue have been found:*
###### [G-02] [FraxlendWhitelist.sol#L81-L82](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81-L82)
```solidity

        for (uint256 i = 0; i < _addresses.length; i++) {

```
###### [G-02b] [FraxlendWhitelist.sol#L66-L67](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66-L67)
```solidity

        for (uint256 i = 0; i < _addresses.length; i++) {

```
###### [G-02c] [FraxlendWhitelist.sol#L51-L52](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51-L52)
```solidity

        for (uint256 i = 0; i < _addresses.length; i++) {

```
###### [G-02d] [FraxlendPair.sol#L289-L290](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289-L290)
```solidity

        for (uint256 i = 0; i < _lenders.length; i++) {

```
###### [G-02e] [FraxlendPair.sol#L308-L309](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308-L309)
```solidity

        for (uint256 i = 0; i < _borrowers.length; i++) {

```
### [G-03] Setting variable to default value is redundant
Variables are by default set to 0 and is a waist of gas if variable sits in storage.
Examples to avoid:
`uint i = 0`
`bool success = false`
*9 instances of this issue have been found:*
###### [G-03] [FraxlendWhitelist.sol#L81-L82](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81-L82)
```solidity

        for (uint256 i = 0; i < _addresses.length; i++) {

```
###### [G-03b] [FraxlendWhitelist.sol#L66-L67](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66-L67)
```solidity

        for (uint256 i = 0; i < _addresses.length; i++) {

```
###### [G-03c] [FraxlendWhitelist.sol#L51-L52](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51-L52)
```solidity

        for (uint256 i = 0; i < _addresses.length; i++) {

```
###### [G-03d] [FraxlendPairDeployer.sol#L127-L128](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L127-L128)
```solidity

        for (i = 0; i < _lengthOfArray; ) {

```
###### [G-03e] [FraxlendPair.sol#L289-L290](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289-L290)
```solidity

        for (uint256 i = 0; i < _lenders.length; i++) {

```
###### [G-03f] [FraxlendPairDeployer.sol#L402-L403](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L402-L403)
```solidity

        for (uint256 i = 0; i < _lengthOfArray; ) {

```
###### [G-03g] [FraxlendPair.sol#L308-L309](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308-L309)
```solidity

        for (uint256 i = 0; i < _borrowers.length; i++) {

```
###### [G-03h] [FraxlendPairCore.sol#L270-L271](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270-L271)
```solidity

        for (uint256 i = 0; i < _approvedLenders.length; ++i) {

```
###### [G-03i] [FraxlendPairCore.sol#L265-L266](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265-L266)
```solidity

        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {

```
### [G-04] `for` loop increments should be `unchecked{}` if overflow is not possible
From Solidity `0.8.0` onwards using the `unchecked` keyword saves 30 to 40 gas per [loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked).
Example:
```solidity
uint length = array.length;
for (uint i; i < length;){
		uncheck { ++i }
}
```
*5 instances of this issue have been found:*
###### [G-04] [FraxlendWhitelist.sol#L81-L82](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81-L82)
```solidity

        for (uint256 i = 0; i < _addresses.length; i++) {

```
###### [G-04b] [FraxlendWhitelist.sol#L66-L67](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66-L67)
```solidity

        for (uint256 i = 0; i < _addresses.length; i++) {

```
###### [G-04c] [FraxlendWhitelist.sol#L51-L52](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51-L52)
```solidity

        for (uint256 i = 0; i < _addresses.length; i++) {

```
###### [G-04d] [FraxlendPairCore.sol#L270-L271](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270-L271)
```solidity

        for (uint256 i = 0; i < _approvedLenders.length; ++i) {

```
###### [G-04e] [FraxlendPairCore.sol#L265-L266](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265-L266)
```solidity

        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {

```
### [G-05] `array.length` should be cached in `for` loop
Caching the length array would save gas on each iteration by not having to read from memory or storage multiple times.
Example:
```solidity
uint length = array.length;
for (uint i; i < length;){
		uncheck { ++i }
}
```
*7 instances of this issue have been found:*
###### [G-05] [FraxlendWhitelist.sol#L81-L82](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81-L82)
```solidity

        for (uint256 i = 0; i < _addresses.length; i++) {

```
###### [G-05b] [FraxlendWhitelist.sol#L66-L67](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66-L67)
```solidity

        for (uint256 i = 0; i < _addresses.length; i++) {

```
###### [G-05c] [FraxlendWhitelist.sol#L51-L52](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51-L52)
```solidity

        for (uint256 i = 0; i < _addresses.length; i++) {

```
###### [G-05d] [FraxlendPair.sol#L289-L290](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289-L290)
```solidity

        for (uint256 i = 0; i < _lenders.length; i++) {

```
###### [G-05e] [FraxlendPair.sol#L308-L309](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308-L309)
```solidity

        for (uint256 i = 0; i < _borrowers.length; i++) {

```
###### [G-05f] [FraxlendPairCore.sol#L270-L271](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270-L271)
```solidity

        for (uint256 i = 0; i < _approvedLenders.length; ++i) {

```
###### [G-05g] [FraxlendPairCore.sol#L265-L266](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265-L266)
```solidity

        for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {

```
### [G-06] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operatio
*2 instances of this issue have been found:*
###### [G-06] [FraxlendPairCore.sol#L132-L137](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L132-L137)
```solidity

    // Internal Whitelists
    bool public immutable borrowerWhitelistActive;
    mapping(address => bool) public approvedBorrowers;

    bool public immutable lenderWhitelistActive;
    mapping(address => bool) public approvedLenders;
```
###### [G-06b] [FraxlendPairCore.sol#L125-L129](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L125-L129)
```solidity

    // User Level Accounting
    /// @notice Stores the balance of collateral for each user
    mapping(address => uint256) public userCollateralBalance; // amount of collateral each user is backed
    /// @notice Stores the balance of borrow shares for each user
    mapping(address => uint256) public userBorrowShares
```
### [G-07] Avoid using `bool` for storage
"Booleans are more expensive than uint256 or any type that takes up a full
word because each write operation emits an extra SLOAD to first read the
slot's contents, replace the bits taken up by the boolean, and then write
back. This is the compiler's defense against contract upgrades and
pointer aliasing, and it cannot be disabled." - [Source: OZ](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

Instead use `uint256(1)` and `uint256(2)` for true and false.
*5 instances of this issue have been found:*
###### [G-07] [FraxlendPairCore.sol#L136-L137](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L136-L137)
```solidity

    bool public immutable lenderWhitelistActive;

```
###### [G-07b] [FraxlendPairCore.sol#L133-L134](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L133-L134)
```solidity

    bool public immutable borrowerWhitelistActive;

```
###### [G-07c] [FraxlendWhitelist.sol#L32-L33](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L32-L33)
```solidity

    mapping(address => bool) public oracleContractWhitelist;

```
###### [G-07d] [FraxlendWhitelist.sol#L35-L36](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L35-L36)
```solidity

    mapping(address => bool) public rateContractWhitelist;

```
###### [G-07e] [FraxlendWhitelist.sol#L38-L39](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L38-L39)
```solidity

    mapping(address => bool) public fraxlendDeployerWhitelist;

```
### [G-08] Function could accept array for `bool`
If function accepted an array for `bool` it would be more gas efficient because it would be able to set contract for both to true and false in the same call.
*3 instances of this issue have been found:*
###### [G-08] [FraxlendWhitelist.sol#L80-L85](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L80-L85)
```solidity

    function setFraxlendDeployerWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
        for (uint256 i = 0; i < _addresses.length; i++) {
            fraxlendDeployerWhitelist[_addresses[i]] = _bool;
            emit SetFraxlendDeployerWhitelist(_addresses[i], _bool);
        }
    }
```
###### [G-08b] [FraxlendWhitelist.sol#L65-L70](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L65-L70)
```solidity

    function setRateContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
        for (uint256 i = 0; i < _addresses.length; i++) {
            rateContractWhitelist[_addresses[i]] = _bool;
            emit SetRateContractWhitelist(_addresses[i], _bool);
        }
    }
```
###### [G-08c] [FraxlendWhitelist.sol#L50-L55](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L50-L55)
```solidity

    function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
        for (uint256 i = 0; i < _addresses.length; i++) {
            oracleContractWhitelist[_addresses[i]] = _bool;
            emit SetOracleWhitelist(_addresses[i], _bool);
        }
    }
```