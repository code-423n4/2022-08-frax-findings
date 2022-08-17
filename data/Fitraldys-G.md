1. Use Custom Error instead of Revert / Require String to Save Gas

Custom error from solidity 0.8.4 are cheaper than revert strings, custom error are defined using the `error` statement can use inside and outside the contract.

source https://blog.soliditylang.org/2021/04/21/custom-errors/

i suggest replacing revert / require error strings with custom error.

POC :

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L59
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L63
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L67
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L205
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L228
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L253
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L365
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L399

2. `require()`/`revert()` strings longer than 32 bytes cost extra gas

Each extra chunk of bytes past the original 32 which costs 3 gas.

resource : https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings

POC :

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L59
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L63
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L67
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L253

3. Splitting `require()` statements that use && save gas

see https://github.com/code-423n4/2022-01-xdefi-findings/issues/128 which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper

POC :

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L66

4. use `abi.encodepacked` for gas optimization

Changing the `abi.encode` function to `abi.encodePacked` can save gas since the `abi.encode` function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient.

POC :

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L47
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L53
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L331
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L374

5. ++i costs less gas tha i++, especially when it's used in for-loops

save 6 gas per loop

POC :

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289

6. Useage of uint / int smaller than 32 bytes incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

POC :

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L166

7. Declaring `uint256 i`

Declaring `uint256 i = 0;` means doing an MSTORE of the value 0 Instead you could just declare `uint256 i` to declare the variable without assigning it’s default value, saving 3 gas per declaration

POC :

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L402

8. cheaper foor loops

You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting:

```
for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
                // Do the thing
                // Unchecked pre-increment is cheapest
                unchecked { ++i; }
        }     
```

POC :

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L402
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289

