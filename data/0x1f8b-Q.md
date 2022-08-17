- [Low](#low)
    - [**1. Outdated compiler**](#1-outdated-compiler)
    - [**2. SafeERC20 mismatch logics**](#2-safeerc20-mismatch-logics)
    - [**3. Lack of ACK during owner change**](#3-lack-of-ack-during-owner-change)
    - [**4. Constant salt**](#4-constant-salt)
    - [**5. Bypasss TimeLock restrictions**](#5-bypasss-timelock-restrictions)
    - [**6. Division before multiple can lead to precision errors**](#6-division-before-multiple-can-lead-to-precision-errors)
    - [**7. Ownable and Pausable**](#7-ownable-and-pausable)
- [Non-Critical](#non-critical)
    - [**8. Use encode instead of encodePacked for hashig**](#8-use-encode-instead-of-encodepacked-for-hashig)
    - [**9. Wrong comment of toBorrowAmount function**](#9-wrong-comment-of-toborrowamount-function)
    - [**10. Confusing variables as to how they are stored**](#10-confusing-variables-as-to-how-they-are-stored)

# Low

## **1. Outdated compiler**

The pragma version used are:

```
pragma solidity ^0.8.15;
```

The minimum required version must be [0.8.16](https://github.com/ethereum/solidity/releases/tag/v0.8.16); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)

- Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

Apart from these, there are several minor bug fixes and improvements.

## **2. `SafeERC20` mismatch logics**

The `SafeERC20` library says:

> @title SafeERC20 provides helper functions for safe transfers as well as safe metadata access

But the reality is that *metada access* is not secure.

The `SafeERC20.safeTransfer` and `SafeERC20.safeTransferFrom` methods perform a safe check that the address to call is a contract by calling [functionCall](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/2dc086563f2e51620ebc43d2237fc10ef201c4e6/contracts/token/ERC20/utils/SafeERC20.sol#L110), this checks doesn't appear in the methods `safeSymbol`, `safeName` and `safeDecimals`, so some of the `SafeERC20` library methods are prone to [phantom methods](https://media.dedaub.com/phantom-functions-and-the-billion-dollar-no-op-c56f062ae49f) attacks.

**Proof of Concept:**

If you try to call the `SafeERC20` methods with the following token `0x000000000000000000000000000000000000000000000` (or any EOA) you can see that the transaction is successful, you have to check that the address is a valid contract and maybe check that the call returns a certain data. Otherwise it is possible to call it and lose tokens or produce undesired errors.

This remind me to this attack https://certik.medium.com/qubit-bridge-collapse-exploited-to-the-tune-of-80-million-a7ab9068e1a0 where we can see:

**Affected source code:**

- [SafeERC20.sol#L39-L58](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L39-L58)

**Recommended changes:**

- Use `functionStaticCall` from OpenZeppelin.

```diff
+import "@openzeppelin/contracts/utils/Address.sol";
...
library SafeERC20 {
+    using Address for address;

    /// @notice Provides a safe ERC20.symbol version which returns '???' as fallback string.
    /// @param token The address of the ERC-20 token contract.
    /// @return (string) Token symbol.
    function safeSymbol(IERC20 token) internal view returns (string memory) {
-       (bool success, bytes memory data) = address(token).staticcall(abi.encodeWithSelector(SIG_SYMBOL));
+       (bool success, bytes memory data) = address(token).functionStaticCall(abi.encodeWithSelector(SIG_SYMBOL));
        return success ? returnDataToString(data) : "???";
    }

    /// @notice Provides a safe ERC20.name version which returns '???' as fallback string.
    /// @param token The address of the ERC-20 token contract.
    /// @return (string) Token name.
    function safeName(IERC20 token) internal view returns (string memory) {
-       (bool success, bytes memory data) = address(token).staticcall(abi.encodeWithSelector(SIG_NAME));
+       (bool success, bytes memory data) = address(token).functionStaticCall(abi.encodeWithSelector(SIG_NAME));
        return success ? returnDataToString(data) : "???";
    }

    /// @notice Provides a safe ERC20.decimals version which returns '18' as fallback value.
    /// @param token The address of the ERC-20 token contract.
    /// @return (uint8) Token decimals.
    function safeDecimals(IERC20 token) internal view returns (uint8) {
-       (bool success, bytes memory data) = address(token).staticcall(abi.encodeWithSelector(SIG_DECIMALS));
+       (bool success, bytes memory data) = address(token).functionStaticCall(abi.encodeWithSelector(SIG_DECIMALS));
        return success && data.length == 32 ? abi.decode(data, (uint8)) : 18;
    }
```

## **3. Lack of ACK during owner change**

It's possible to lose the ownership under specific circumstances.

Because an human error it's possible to set a new invalid owner. When you want to change the owner's address it's better to propose a new owner, and then accept this ownership with the new wallet.

**Affected source code:**

- [FraxlendWhitelist.sol#L30](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L30)
- [FraxlendPairDeployer.sol#L44](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L44)


## **4. Constant salt**

Is not possible to use `public` as pair name, if you are using the same default values as was used in `deploy`.

Becuase the salt for deploy is *"public"*, is not possible to use the same as a name.

**Affected source code:**

- [FraxlendPairDeployer.sol#L329](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L329)
- [FraxlendPairDeployer.sol#L372](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L372)

**Recommended changes:**

```diff
-   keccak256(abi.encodePacked(_name)),
+   keccak256(abi.encodePacked("custom", _name)),
```

## **5. Bypasss `TimeLock` restrictions**

Esto permite que llamadas como la que se muestran a continuacion, que se encuentran limitadas al contrato de `TIME_LOCK_ADDRESS`, sea posible ser llamadas por el owner, evitando cualquier limitación de tiempo, debido a que en primer lugar no se comprueba que la direccion establecida en `setTimeLock` sea un contrato, y segundo, porque podria establecer cualquier contrato, y en la misma llamada hacer la llamada limitada al `TimeLock`.

The `FraxlendPair` contract has a `setTimeLock` method that allows you to modify the `TimeLock` contract (`TIME_LOCK_ADDRESS`), this call is protected by the `onlyOwner` modifier.

This allows the owner to call methods like the one shown below (`changeFee`), which are limited to the `TIME_LOCK_ADDRESS` contract, avoiding any time constraints, since the established address in `setTimeLock` is not checked to be a contract, you could set any contract, and in the same transaction you can call the protected method.

```javascript
    function changeFee(uint32 _newFee) external whenNotPaused {
        if (msg.sender != TIME_LOCK_ADDRESS) revert OnlyTimeLock();
```

This can facilitate rogue pool attacks.

**Affected source code:**

- [FraxlendPair.sol#L204-L222](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L204-L222)

## **6. Division before multiple can lead to precision errors**

Because Solidity integer division may truncate, it is often preferable to do multiplication before division to prevent precision loss.

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

## **7. `Ownable` and `Pausable`**

The contract `FraxlendPairCore` is `Ownable` and `Pausable`, so the owner could resign while the contract is paused, causing a Denial of Service. Owner resignation while the contract is paused should be avoided.

**Affected source code:**

- [FraxlendPairCore.sol#L46](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L46)

----

# Non-Critical

## **8. Use `encode` instead of `encodePacked` for hashig**

Use of `abi.encodePacked` is safe, but unnecessary and not recommended. `abi.encodePacked` can result in hash collisions when used with two dynamic arguments (string/bytes).

There is also discussion of removing `abi.encodePacked` from future versions of Solidity ([ethereum/solidity#11593](https://github.com/ethereum/solidity/issues/11593)), so using `abi.encode` now will ensure compatibility in the future.

**Affected source code:**

- [FraxlendPairDeployer.sol#L204](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L204)

## **9. Wrong comment of `toBorrowAmount` function**

The function `toBorrowAmount` is wrong commented.

**Affected source code:**

- [FraxlendPair.sol#L187-L190](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L187-L190)

**Recommended changes:**

```diff
-   /// @notice The ```toBorrtoBorrowAmountowShares``` function converts a given amount of borrow debt into the number of shares
+   /// @notice The ```toBorrowAmount``` function converts a given number of shares into the amount of borrow debt
    /// @param _shares Shares of borrow
    /// @param _roundUp Whether to roundup during division
    function toBorrowAmount(uint256 _shares, bool _roundUp) external view returns (uint256) {
        return totalBorrow.toAmount(_shares, _roundUp);
    }
```

## **10. Confusing variables as to how they are stored**

The `FraxlendPairCore` contract constructor takes two arguments, `_configData` and `_immutables`, however, there are immutables in `_configData` and variables in `_immutables`.

This may seem confusing, since for example one expects the `TIME_LOCK_ADDRESS` to be immutable as it is defined in the `_immutables` variable, however it is possible to change it with [setTimeLock](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L204).

**Affected source code:**

- [FraxlendPairCore.sol#L174](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L174)
- [FraxlendPairCore.sol#L190-L191](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L190-L191)
- [FraxlendPairCore.sol#L193-L197](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L193-L197)
