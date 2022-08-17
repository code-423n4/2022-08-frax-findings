### Unspecific Compiler Version Pragma

Floating pragmas make sense for libraries but in other contracts their are necessary and it is recommended to use specific pragma version. Floating pragmas may be a security risk for application implementations.


_There are **13** instances of this issue:_

```solidity
File: ./src/contracts/FraxlendPair.sol

2: pragma solidity ^0.8.15;
```

```solidity
File: ./src/contracts/FraxlendPairConstants.sol

2: pragma solidity ^0.8.15;
```

```solidity
File: ./src/contracts/FraxlendPairCore.sol

2: pragma solidity ^0.8.15;
```

```solidity
File: ./src/contracts/FraxlendPairDeployer.sol

2: pragma solidity ^0.8.15;
```

```solidity
File: ./src/contracts/FraxlendPairHelper.sol
2: pragma solidity ^0.8.15;
```

```solidity
File: ./src/contracts/FraxlendWhitelist.sol

2: pragma solidity ^0.8.15;
```

```solidity
File: ./src/contracts/LinearInterestRate.sol

2: pragma solidity ^0.8.15;
```

```solidity
File: ./src/contracts/VariableInterestRate.sol

2: pragma solidity ^0.8.15;
```
```solidity
File: ./src/contracts/interfaces/IERC4626.sol

2: pragma solidity >=0.8.15;
```
```solidity
File: ./src/contracts/interfaces/IFraxlendPair.sol

2: pragma solidity >=0.8.15;
```

```solidity
File: ./src/contracts/interfaces/IFraxlendWhitelist.sol 

2: pragma solidity >=0.8.15;
```

```solidity
File: ./src/contracts/interfaces/IRateCalculator.sol

2: pragma solidity >=0.8.15;
```

```solidity
File: ./src/contracts/interfaces/ISwapper.sol

2:  pragma solidity >=0.8.15;
```
