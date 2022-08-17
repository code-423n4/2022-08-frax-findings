# [L-01] Block `TIME_LOCK_ADDRESS` modifications when the the contract `FraxlendPair.sol` is paused

For the contract `FraxlendPair.sol`, the variable `TIME_LOCK_ADDRESS` is only used inside `changeFee()`.
`changeFee` can be paused and disallow changes. However, `TIME_LOCK_ADDRESS` can be modified at any time.

```
File: contracts/FraxlendPair.sol
function setTimeLock(address _newAddress) external onlyOwner {
    emit SetTimeLock(TIME_LOCK_ADDRESS, _newAddress);
    TIME_LOCK_ADDRESS = _newAddress;
}

/// @notice The ```ChangeFee``` event first when the fee is changed
/// @param _newFee The new fee
event ChangeFee(uint32 _newFee);

/// @notice The ```changeFee``` function changes the protocol fee, max 50%
// @param _newFee The new fee
function changeFee(uint32 _newFee) external whenNotPaused {
    if (msg.sender != TIME_LOCK_ADDRESS) revert OnlyTimeLock();
    if (_newFee > MAX_PROTOCOL_FEE) {
        revert BadProtocolFee();
    }
    currentRateInfo.feeToProtocolRate = _newFee;
    emit ChangeFee(_newFee);
}

```

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L204-L222

## Recommended Mitigation Steps

The `whenNotPaused` modifier could be added into `setTimeLock` to ensure `TIME_LOCK_ADDRESS` is paused in synchrony with `changeFee()`.

```
$ git diff --no-index FraxlendPair.sol.orig FraxlendPair.sol
diff --git a/FraxlendPair.sol.orig b/FraxlendPair.sol
index d54b8f5..1ae7fa0 100644
--- a/FraxlendPair.sol.orig
+++ b/FraxlendPair.sol
@@ -201,7 +201,7 @@ contract FraxlendPair is FraxlendPairCore {

     /// @notice The ```setTimeLock``` function sets the TIME_LOCK address
     /// @param _newAddress the new time lock address
-    function setTimeLock(address _newAddress) external onlyOwner {
+    function setTimeLock(address _newAddress) external onlyOwner whenNotPaused {
         emit SetTimeLock(TIME_LOCK_ADDRESS, _newAddress);
         TIME_LOCK_ADDRESS = _newAddress;
     }
```


# [NC-01] Non library/interface files should use fixed compiler verion

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package.

There's 7 non library files with floating pragma.

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L2

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L2

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L2

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairConstants.sol#L2

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L2

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L2

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol

# [NC-02] Constants should be defined rather than using magic number

```
File: contracts/FraxlendPairDeployer.sol
function setCreationCode(bytes calldata _creationCode) external onlyOwner {
    bytes memory _firstHalf = BytesLib.slice(_creationCode, 0, 13000);
    contractAddress1 = SSTORE2.write(_firstHalf);
    if (_creationCode.length > 13000) {
        bytes memory _secondHalf = BytesLib.slice(_creationCode, 13000, _creationCode.length - 13000);
        contractAddress2 = SSTORE2.write(_secondHalf);
    }
}
```

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L170-L177

```
File: contracts/FraxlendPairCore.sol#L194
194: dirtyLiquidationFee = (_liquidationFee * 9000) / LIQ_PRECISION; // 90% of clean fee
```

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L194

# [NC-03] Constants are missing the constant keyword

```
File: contracts/FraxlendPairDeployer.sol
// Constants
uint256 public DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision
uint256 public GLOBAL_MAX_LTV = 1e8; // 1000x (100,000%) with 1e5 precision, protects from rounding errors in LTV calc
uint256 public DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision
```

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L48-L51

# [NC-04] Inconsistent use of line breaks for conditionals

Conditionals with and without lines breaks are being used interchangibily.

```
File: contracts/FraxlendPair.sol
if (msg.sender != TIME_LOCK_ADDRESS) revert OnlyTimeLock();
if (_newFee > MAX_PROTOCOL_FEE) {
    revert BadProtocolFee();
}
```

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L216-L219

The project should choose a coding style and use throughout the project.

E.g. always use line breaking:

```
if (msg.sender != TIME_LOCK_ADDRESS) {
    revert OnlyTimeLock();
}
if (_newFee > MAX_PROTOCOL_FEE) {
    revert BadProtocolFee();
}
```

Or don't break the lines for conditionals bellow a threshold witdh (e.g. 80 or 100 chars):

```
if (msg.sender != TIME_LOCK_ADDRESS) revert OnlyTimeLock();
if (_newFee > MAX_PROTOCOL_FEE) revert BadProtocolFee();
```

# [NC-05] Use 16 bits instead of 32 for `_newFee`

`_newFee` cannot be larger than 50000, due to this conditional:

```
File: contracts/FraxlendPair.sol
if (_newFee > MAX_PROTOCOL_FEE) {
    revert BadProtocolFee();
}
```

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L217-L219

The variable can be declared with uint16 (max 65535) instead of uint32 (4294967295). Therefore, the following changes can be made.

```
$ git diff --no-index FraxlendPair.sol.orig FraxlendPair.sol
diff --git a/FraxlendPair.sol.orig b/FraxlendPair.sol
index d54b8f5..5114db2 100644
--- a/FraxlendPair.sol.orig
+++ b/FraxlendPair.sol
@@ -208,11 +208,11 @@ contract FraxlendPair is FraxlendPairCore {

     /// @notice The ```ChangeFee``` event first when the fee is changed
     /// @param _newFee The new fee
-    event ChangeFee(uint32 _newFee);
+    event ChangeFee(uint16 _newFee);

     /// @notice The ```changeFee``` function changes the protocol fee, max 50%
     /// @param _newFee The new fee
-    function changeFee(uint32 _newFee) external whenNotPaused {
+    function changeFee(uint16 _newFee) external whenNotPaused {
         if (msg.sender != TIME_LOCK_ADDRESS) revert OnlyTimeLock();
         if (_newFee > MAX_PROTOCOL_FEE) {
             revert BadProtocolFee();
```

# [NC-06] Use the function `deployedPairsLength` instead of computing `deployedPairsArray.length` multiple times

`deployedPairsArray.length` is computed twice in `FraxlendPairDeployer.sol`.

```
File: contracts/FraxlendPairDeployer.sol
123: string[] memory _deployedPairsArray = deployedPairsArray;
124: uint256 _lengthOfArray = _deployedPairsArray.length;
```

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L123-L124

```
File: contracts/FraxlendPairDeployer.sol
324: (deployedPairsArray.length + 1).toString()
```

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L324

The contract has an extenal function that returns the length of the array.

```
File: contracts/FraxlendPairDeployer.sol
function deployedPairsLength() external view returns (uint256) {
    return deployedPairsArray.length;
}
```

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L116-L118

I would recommend converting the `deployedPairsLength()` into a public function and using it to compute the array length throughout the `FraxlendPairDeployed` contract.

# [NC-07] Public functions not called by the contract should be declared external

```
File: contracts/FraxlendPair.sol#L100
100: function totalAssets() public view virtual returns (uint256) {
```

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L100
