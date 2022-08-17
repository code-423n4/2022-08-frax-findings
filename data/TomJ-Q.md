## Table of Contents
### Low Risk Issues
- Missing Zero Address Check 

### Non-critical Issues
- Use Fixed Compiler Versions Instead of Floating Version

&ensp;
## Low Risk Issues
### Missing Zero Address Check 

#### Issue
I recommend adding check of 0-address for input validation of critical address parameters.
Not doing so might lead to non-functional contract and have to redeploy the contract, when it is updated to 0-address accidentally.

#### PoC
Total of 10 instances found.

- FraxlendPairCore.sol: "assetContract" address 
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L190

- FraxlendPairCore.sol: "collateralContract" address
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L191

- FraxlendPairCore.sol: "CIRCUIT_BREAKER_ADDRESS" address
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L172

- FraxlendPairCore.sol: "COMPTROLLER_ADDRESS" address
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L173

- FraxlendPairCore.sol: "TIME_LOCK_ADDRESS" address
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L174

- FraxlendPairCore.sol: "FRAXLEND_WHITELIST" address
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L175

- FraxlendPairDeployer.sol: "FRAXLEND_WHITELIST_ADDRESS" address
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L107

- FraxlendPairDeployer.sol: "CIRCUIT_BREAKER_ADDRESS" address
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L104

- FraxlendPairDeployer.sol: "COMPTROLLER_ADDRESS" address
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L105

- FraxlendPairDeployer.sol: "TIME_LOCK_ADDRESS" address
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L106

#### Mitigation
Add 0-address check for above addresses.

&ensp;
## Non-critical Issues
### Use Fixed Compiler Versions Instead of Floating Version


#### Issue
it is best practice to lock your pragma instead of using floating pragma.
the use of floating pragma has a risk of accidentally get deployed using latest complier
which may have higher risk of undiscovered bugs.
Reference: https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/

#### PoC
```
./FraxlendPairDeployer.sol:2:pragma solidity ^0.8.15;
./VaultAccount.sol:2:pragma solidity ^0.8.15;
./SafeERC20.sol:2:pragma solidity ^0.8.15;
./FraxlendPair.sol:2:pragma solidity ^0.8.15;
./VariableInterestRate.sol:2:pragma solidity ^0.8.15;
./FraxlendWhitelist.sol:2:pragma solidity ^0.8.15;
./FraxlendPairConstants.sol:2:pragma solidity ^0.8.15;
./FraxlendPairCore.sol:2:pragma solidity ^0.8.15;
./LinearInterestRate.sol:2:pragma solidity ^0.8.15;
```

#### Mitigation
I suggest to lock your pragma and aviod using floating pragma.
```
// bad
pragma solidity ^0.8.15;

// good
pragma solidity 0.8.15;
```

&ensp;