- [Gas](#gas)
    - [**1. Don't use the length of an array for loops condition**](#1-dont-use-the-length-of-an-array-for-loops-condition)
    - [**2. ++i costs less gas compared to i++ or i += 1**](#2-i-costs-less-gas-compared-to-i-or-i--1)
    - [**3. There's no need to set default values for variables**](#3-theres-no-need-to-set-default-values-for-variables)
        - [Total gas saved: **8 * 6 = 48**](#total-gas-saved-8--6--48)
    - [**4. > 0 is less efficient than != 0 for unsigned integers**](#4--0-is-less-efficient-than--0-for-unsigned-integers)
    - [**5. Reduce the size of error messages Long revert Strings**](#5-reduce-the-size-of-error-messages-long-revert-strings)
        - [Total gas saved: **18 * 8 = 144**](#total-gas-saved-18--8--144)
    - [**6. Use Custom Errors instead of Revert Strings to save Gas**](#6-use-custom-errors-instead-of-revert-strings-to-save-gas)
        - [Total gas saved: **9 * 9 = 81**](#total-gas-saved-9--9--81)
    - [**7. Optimize returnDataToString**](#7-optimize-returndatatostring)
    - [**8. Cache abi.encodeWithSelector result**](#8-cache-abiencodewithselector-result)
    - [**9. Change bool to uint256 can save gas**](#9-change-bool-to-uint256-can-save-gas)
    - [**10. Use constants**](#10-use-constants)
    - [**11. Gas saving using immutable**](#11-gas-saving-using-immutable)
    - [**12. Reuse abi.decode results**](#12-reuse-abidecode-results)
    - [**13. Use immutable to cache constant data**](#13-use-immutable-to-cache-constant-data)
    - [**14. State variables must be cached in stack variables if they are reused**](#14-state-variables-must-be-cached-in-stack-variables-if-they-are-reused)
    - [**15. Avoid compound assignment operator in state variables**](#15-avoid-compound-assignment-operator-in-state-variables)
        - [Total gas saved: **13 * 7 = 91**](#total-gas-saved-13--7--91)
    - [**16. Reduce math operations**](#16-reduce-math-operations)


# Gas

## **1. Don't use the length of an array for loops condition**

It's cheaper to store the length of the array inside a local variable and iterate over it.

**Affected source code:**

- [FraxlendPair.sol#L289](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289)
- [FraxlendPair.sol#L308](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308)
- [FraxlendPairCore.sol#L265](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265)
- [FraxlendPairCore.sol#L270](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270)
- [FraxlendWhitelist.sol#L51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51)
- [FraxlendWhitelist.sol#L66](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66)
- [FraxlendWhitelist.sol#L81](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81)
    
## **2. `++i` costs less gas compared to `i++` or `i += 1`**

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments `i` and returns the initial value of `i`. Which means:

```solidity
uint i = 1;
i++; // == 1 but i == 2
```

But `++i` returns the actual incremented value:

```solidity
uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable
```

In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`
I suggest using `++i` instead of `i++` to increment the value of an uint variable. Same thing for `--i` and `i--`

**Affected source code:**

- [FraxlendPair.sol#L289](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289)
- [FraxlendPair.sol#L308](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308)
- [FraxlendPairDeployer.sol#L130](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L130)
- [FraxlendPairDeployer.sol#L158](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L158)
- [FraxlendPairDeployer.sol#L408](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L408)
- [FraxlendWhitelist.sol#L51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51)
- [FraxlendWhitelist.sol#L66](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66)
- [FraxlendWhitelist.sol#L81](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81)
- [SafeERC20.sol#L24](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L24)
- [SafeERC20.sol#L27](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L27)

## **3. There's no need to set default values for variables**

If a variable is not set/initialized, the default value is assumed (0, `false`, 0x0 ... depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
 function testInit() public view returns (uint) { uint a = 0; return a; }
}

contract TesterB {
 function testNoInit() public view returns (uint) { uint a; return a; }
}
```

Gas saving executing: **8 per entry**

```
TesterA.testInit:   21392
TesterB.testNoInit: 21384   
```

**Affected source code:**

- [FraxlendPairConstants.sol#L47](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairConstants.sol#L47)
- [FraxlendPairDeployer.sol#L127](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L127)
- [FraxlendPairDeployer.sol#L152](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L152)
- [SafeERC20.sol#L22](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L22)
- [SafeERC20.sol#L27](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L27)
- [LinearInterestRate.sol#L33](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L33)

### Total gas saved: **8 * 6 = 48**


## **4. `> 0` is less efficient than `!= 0` for unsigned integers**

Although it would appear that `> 0` is less expensive than `!= 0,` this is only true in the absence of the optimizer and outside of a need statement. Gas will be saved if the optimizer is enabled at 10k and you're in a require statement.

**Reference:**

- https://twitter.com/gzeon/status/1485428085885640706

**Affected source code:**

- [LinearInterestRate.sol#L66](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L66)
    
## **5. Reduce the size of error messages (Long revert Strings)**

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
 function testShortRevert(bool path) public view {
  require(path, "test error");
 }
}

contract TesterB {
 function testLongRevert(bool path) public view {
  require(path, "test big error message, more than 32 bytes");
 }
}
```

Gas saving executing: **18 per entry**

```
TesterA.testShortRevert: 21886   
TesterB.testLongRevert:  21904       
```

**Affected source code:**

- [FraxlendPairDeployer.sol#L205](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L205)
- [FraxlendPairDeployer.sol#L228](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L228)
- [FraxlendPairDeployer.sol#L253](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L253)
- [FraxlendPairDeployer.sol#L365](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L365)
- [FraxlendPairDeployer.sol#L366-L369](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L366-L369)
- [LinearInterestRate.sol#L57-L60](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L60)
- [LinearInterestRate.sol#L61-L64](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L61-L64)
- [LinearInterestRate.sol#L65-L68](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L65-L68)

### Total gas saved: **18 * 8 = 144**

## **6. Use Custom Errors instead of Revert Strings to save Gas**

Custom errors from Solidity `0.8.4` are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

**Source Custom Errors in Solidity:**

Starting from Solidity `v0.8.4`, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
 function testRevert(bool path) public view {
  require(path, "test error");
 }
}

contract TesterB {
 error MyError(string msg);
 function testError(bool path) public view {
  if(path) revert MyError("test error");
 }
}
```

Gas saving executing: **9 per entry**

```
TesterA.testRevert: 21611
TesterB.testError:  21602     
```

**Affected source code:**

- [FraxlendPairDeployer.sol#L205](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L205)
- [FraxlendPairDeployer.sol#L228](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L228)
- [FraxlendPairDeployer.sol#L253](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L253)
- [FraxlendPairDeployer.sol#L365](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L365)
- [FraxlendPairDeployer.sol#L366-L369](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L366-L369)
- [FraxlendPairDeployer.sol#L399](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L399)
- [LinearInterestRate.sol#L57-L60](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L60)
- [LinearInterestRate.sol#L61-L64](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L61-L64)
- [LinearInterestRate.sol#L65-L68](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L65-L68)

### Total gas saved: **9 * 9 = 81**

## **7. Optimize `returnDataToString`**

Since the value has already been checked to be != 0 previously, there is no need to do it again.

**Affected source code:**

- [SafeERC20.sol#L27-L29](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L27-L29)

**Recommended changes:**

```diff
    function returnDataToString(bytes memory data) internal pure returns (string memory) {
        if (data.length >= 64) {
            return abi.decode(data, (string));
        } else if (data.length == 32) {
            uint8 i = 0;
            while (i < 32 && data[i] != 0) {
                i++;
            }
            bytes memory bytesArray = new bytes(i);
-           for (i = 0; i < 32 && data[i] != 0; i++) {
+           for (uint x; x < i; x++) {
-               bytesArray[i] = data[i];
+               bytesArray[x] = data[x];
            }
            return string(bytesArray);
        } else {
            return "???";
        }
    }
```

## **8. Cache `abi.encodeWithSelector` result**

It is not necessary to call `abi.encodeWithSelector` with no data each time, it is cheaper to cache the byte result.

**Affected source code:**

- [SafeERC20.sol#L14-L16](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L14-L16)

**Recommended changes:**

```diff
library SafeERC20 {
-   bytes4 private constant SIG_SYMBOL = 0x95d89b41; // symbol()
-   bytes4 private constant SIG_NAME = 0x06fdde03; // name()
-   bytes4 private constant SIG_DECIMALS = 0x313ce567; // decimals()
+   bytes private immutable SIG_SYMBOL = abi.encodeWithSelector(0x95d89b41); // symbol()
+   bytes private immutable SIG_NAME = abi.encodeWithSelector(0x06fdde03); // name()
+   bytes private immutable SIG_DECIMALS = abi.encodeWithSelector(0x313ce567); // decimals()
    ...
    function safeSymbol(IERC20 token) internal view returns (string memory) {
+       (bool success, bytes memory data) = address(token).staticcall(SIG_SYMBOL);
-       (bool success, bytes memory data) = address(token).staticcall(abi.encodeWithSelector(SIG_SYMBOL));
        return success ? returnDataToString(data) : "???";
    }
    ...
    function safeName(IERC20 token) internal view returns (string memory) {
+       (bool success, bytes memory data) = address(token).staticcall(SIG_NAME);
-       (bool success, bytes memory data) = address(token).staticcall(abi.encodeWithSelector(SIG_NAME));
        return success ? returnDataToString(data) : "???";
    }
    ...
    function safeDecimals(IERC20 token) internal view returns (uint8) {
+       (bool success, bytes memory data) = address(token).staticcall(SIG_DECIMALS);
-       (bool success, bytes memory data) = address(token).staticcall(abi.encodeWithSelector(SIG_DECIMALS));
        return success && data.length == 32 ? abi.decode(data, (uint8)) : 18;
    }
    ...
```

## **9. Change `bool` to `uint256` can save gas**

Because each write operation requires an additional `SLOAD` to read the slot's contents, replace the bits occupied by the boolean, and then write back, `booleans` are more expensive than `uint256` or any other type that uses a complete word. This cannot be turned off because it is the compiler's defense against pointer aliasing and contract upgrades.

Reference:

- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol#L23-L27

**Affected source code:**

- [FraxlendWhitelist.sol#L32](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L32)
- [FraxlendWhitelist.sol#L35](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L35)
- [FraxlendWhitelist.sol#L38](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L38)
- [FraxlendPairDeployer.sol#L94](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L94)
- [FraxlendPairCore.sol#L78](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L78)
- [FraxlendPairCore.sol#L133](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L133)
- [FraxlendPairCore.sol#L134](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L134)
- [FraxlendPairCore.sol#L136](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L136)
- [FraxlendPairCore.sol#L137](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L137)

## **10. Use constants**

Use constant instead of storage/computed values in:

**Affected source code:**

- [FraxlendPairDeployer.sol#L49-L51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L49-L51)

## **11. Gas saving using `immutable`**

It's possible to avoid storage access a save gas using `immutable` keyword for the following variables:

It's also better to remove the initial values, because they will be set during the constructor.

**Affected source code:**

- [FraxlendPairDeployer.sol#L57-L59](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L57-L59)
- [FraxlendPairCore.sol#L51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L51)

## **12. Reuse `abi.decode` results**

`deploy` should decode `_configData` as an struct. In this way, we can avoid the third decoding in the `_deployFirst`, `_deploySecond` and `_log` methods.

**Affected source code:**

- [FraxlendPairDeployer.sol#L249-L252](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L249-L252)
- [FraxlendPairDeployer.sol#L288](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L288)

```javascript
struct ConfigData
{
    address _asset,
    address _collateral,
    address _oracleMultiply,
    address _oracleDivide,
    uint256 _oracleNormalization,
    address _rateContract,
    bytes memory _rateInitData
}
    function _deploySecond(
        string memory _name,
        address _pairAddress,
        ConfigData memory _configData,
        address[] memory _approvedBorrowers,
        address[] memory _approvedLenders
    ) private returns (ConfigData memory data) { ... }

    function _logDeploy(
        string memory _name,
        address _pairAddress,
        ConfigData memory _configData,
        uint256 _maxLTV,
        uint256 _liquidationFee,
        uint256 _maturityDate
    ) private { ... }
```

## **13. Use `immutable` to cache constant data**

It is not necessary to continuously calculate the `symbol`, it can be cached during the contract deployment.

**Affected source code:**

- [FraxlendPair.sol#L81](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L81)

**Recommended changes:**

```diff
+   string private immutable _symbol;
+
    constructor(
        bytes memory _configData,
        bytes memory _immutables,
        uint256 _maxLTV,
        uint256 _liquidationFee,
        uint256 _maturityDate,
        uint256 _penaltyRate,
        bool _isBorrowerWhitelistActive,
        bool _isLenderWhitelistActive
    )
        FraxlendPairCore(
            _configData,
            _immutables,
            _maxLTV,
            _liquidationFee,
            _maturityDate,
            _penaltyRate,
            _isBorrowerWhitelistActive,
            _isLenderWhitelistActive
        )
        ERC20("", "")
        Ownable()
        Pausable()
-   {}
+   {
+       _symbol = string(abi.encodePacked("FraxlendV1 - ", collateralContract.safeSymbol(), "/", assetContract.safeSymbol()));
+   }

    function symbol() public view override(ERC20, IERC20Metadata) returns (string memory) {
        // prettier-ignore
        // solhint-disable-next-line max-line-length
-       return string(abi.encodePacked("FraxlendV1 - ", collateralContract.safeSymbol(), "/", assetContract.safeSymbol()));
+       return _symbol;
    }
```

## **14. State variables must be cached in stack variables if they are reused**

It is possible to save accesses to storage by caching the state variables.

**Affected source code:**

- [FraxlendPair.sol#L310-L312](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L310-L312)

**Recommended changes:**

```diff
    /// @notice The ```setApprovedBorrowers``` function sets a given array of addresses to the whitelist
    /// @dev Cannot black list self
    /// @param _borrowers The addresses whos status will be set
    /// @param _approval The approcal status
    function setApprovedBorrowers(address[] calldata _borrowers, bool _approval) external approvedBorrower {
        for (uint256 i = 0; i < _borrowers.length; i++) {
            // Do not set when _approval == false and _borrower == msg.sender
-           if (_approval || _borrowers[i] != msg.sender) {
-               approvedBorrowers[_borrowers[i]] = _approval;
-               emit SetApprovedBorrower(_borrowers[i], _approval);
+           address _borrower = _borrowers[i];
+           if (_approval || _borrower != msg.sender) {
+               approvedBorrowers[_borrower] = _approval;
+               emit SetApprovedBorrower(_borrower, _approval);
            }
        }
    }
```

## **15. Avoid compound assignment operator in state variables**

Using compound assignment operator for state variables (like `State += X` or `State -= X` ...) it's more expensive than use operator assignment (like `State = State + X` or `State = State - X` ...).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
 uint private _a;
 function testShort() public {
  _a += 1;
 }
}

contract TesterB {
 uint private _a;
 function testLong() public {
  _a = _a + 1;
 }
}
```

Gas saving executing: **13 per entry**

```
TesterA.testShort: 43507
TesterB.testLong:  43494
```

**Affected source code:**

- [FraxlendPairCore.sol#L724](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L724)
- [FraxlendPairCore.sol#L772](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L772)
- [FraxlendPairCore.sol#L773](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L773)
- [FraxlendPairCore.sol#L813](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L813)
- [FraxlendPairCore.sol#L815](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L815)
- [FraxlendPairCore.sol#L867](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L867)
- [FraxlendPairCore.sol#L1008](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1008)

### Total gas saved: **13 * 7 = 91**

## **16. Reduce math operations**

It is possible to reduce the number of operations inside `_isSolvent` by creating a new constant with value `1e13`. We can now divide by just this constant instead of multiplying by `1e18` and dividing by `1e5`, with the loss of precision that this causes.

**Affected source code:**

- [FraxlendPairCore.sol#L315](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L315)

**Recommended changes:**

```diff
    uint256 internal constant LTV_PRECISION = 1e5; // 5 decimals
    uint256 internal constant EXCHANGE_PRECISION = 1e18;
    ...
+   uint256 internal constant SOLVENT_PRECISION = EXCHANGE_PRECISION / LTV_PRECISION; // 1e13
    ... 

    function _isSolvent(address _borrower, uint256 _exchangeRate) internal view returns (bool) {
        if (maxLTV == 0) return true;
        uint256 _borrowerAmount = totalBorrow.toAmount(userBorrowShares[_borrower], true);
        if (_borrowerAmount == 0) return true;
        uint256 _collateralAmount = userCollateralBalance[_borrower];
        if (_collateralAmount == 0) return false;

-       uint256 _ltv = (((_borrowerAmount * _exchangeRate) / EXCHANGE_PRECISION) * LTV_PRECISION) / _collateralAmount;
+       uint256 _ltv = (_borrowerAmount * _exchangeRate) / (_collateralAmount * SOLVENT_PRECISION);
        return _ltv <= maxLTV;
    }
```
