1. Need Modified OnlyWhitelisted on FraxlendPairDeployer.sol

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L355

This fn can be used as modifier OnlyWhitelisted eversince to known a function was doesn't do anything unusual. Since Modifiers are code that can be run before and / or after a function call.

```
Modifiers can be used to:

Restrict access
Validate inputs
Guard against reentrancy hack
```

2. Optimize fn safeTransfer() and safeTransferFrom()

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L14-L17

on that side we can added as original boringERC20 used for, and adding  :

```
    bytes4 private constant SIG_TRANSFER = 0xa9059cbb; // transfer(address,uint256)
    bytes4 private constant SIG_TRANSFER_FROM = 0x23b872dd; // transferFrom(address,address,uint256)
```

since the creator was of @boringcrypto

```
This is not a full ERC20 implementation, as it's missing totalSupply. It's optimized for minimal gas usage while remaining easy to read.
```

So the implementation for safeTransfer & safeTransferFrom can be used. So for fn [safeTransfer()](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L60-L66) and [safeTransferFrom()](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L68-L74)

can used this implementation below to get it done (didn't need to import SafeERC20 as OZSafeERC20, which can optimize more) :

```
        (bool success, bytes memory data) = address(token).call(abi.encodeWithSelector(SIG_TRANSFER, to, value));
        require(success && (data.length == 0 || abi.decode(data, (bool))), "Transfer failed");
```

```
        (bool success, bytes memory data) = address(token).call(abi.encodeWithSelector(SIG_TRANSFER_FROM, from, to, value));
        require(success && (data.length == 0 || abi.decode(data, (bool))), "TransferFrom failed");
```


Source : 

https://github.com/boringcrypto/BoringSolidity
https://github.com/boringcrypto/BoringSolidity/blob/master/contracts/libraries/BoringERC20.sol

3. fn withdrawFees needed to be checked not to get Zero Addreses

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L234

in this line of source, needed to be checked if the ` _recipient` was not getting zero addresess.

4. Code operator can be changed 

for best practice, since a = a + 1, can be changed into a += 1. 

Files :

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/VaultAccount.sol#L26

```
                share += 1; // or share++;
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/VaultAccount.sol#L43

```
                amount += + 1; // or amount++
```

Source : 

https://www.geeksforgeeks.org/solidity-operators/

5. Unchecked  i = i + 1 can be changed

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L408

This can be changed by using i++ or ++i instead.

6. DEFAULT_LIQ_FEE can be changed 

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L51

Since DEFAULT_LIQ_FEE was set to be // 0% with 1e5 precision. It can be changed by using 1e4 rather than 10000

```
    uint256 public DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision
```

7. Typo Comment 

Files :

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L71

```
f(utilization) // f can be deleted
```

8. Avoid Floatin Pragma's

Since it was used ^0.8.15. As the compiler can be use for example 0.8.x and consider locking at this version the same as another. It can be consider using  locking the pragma version whenever possible and avoid using a floating pragma in the final deployment. Since it can be problematic, if there are publicly disclosed bugs and issues that affect the current compiler version used.