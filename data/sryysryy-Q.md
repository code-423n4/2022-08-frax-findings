## [N-01] NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

_There are 7 instances of this issue:_

```solidity
contracts/FraxlendPair.sol::2            pragma solidity ^0.8.15;
contracts/FraxlendPairConstants.sol::2   pragma solidity ^0.8.15;
contracts/FraxlendPairCore.sol::2        pragma solidity ^0.8.15;
contracts/FraxlendPairDeployer.sol::2    pragma solidity ^0.8.15;
contracts/FraxlendWhitelist.sol::2       pragma solidity ^0.8.15;
contracts/LinearInterestRate.sol::2      pragma solidity ^0.8.15;
contracts/VariableInterestRate.sol::2    pragma solidity ^0.8.15;
```
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L2
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairConstants.sol#L2
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L2
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L2
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L2
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L2
