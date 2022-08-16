# Low Risk

## [L-01] Floating pragma.

It's a good practice to avoid the use of floating pragma. Code must be compiled with the same version it as been tested the most. It also avoids the use of any nightly builds, which can have unexpected and unknown behaviors.

9 instances

 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L2
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol#L2
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L2
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L2
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L2
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairConstants.sol#L2
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L2
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L2
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L2

Consider replacing `^0.8.15` by `0.8.15`.

## [L-02] Missing check for address(0) when assigning value to address state variable.
Zero address checking is the best practice to prevent the redeployment of the contract in case of a typo or an error in deploying.

1 instances:

 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L104-L107

Consider checking that anay of them is `== address(0)`.

# Non Critical

## [N-01] Typo.

Consider these changes:
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L231
 `whitlist` -> `whitelist`
 
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L896
 `liabilites` -> `liabilities`
 
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L168
 `accomodate` -> `accommodate`
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L47
 `oralce` -> `oracle`
 
 - https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L32
     `calulcating` -> `calculating`
