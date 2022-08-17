## 1. Define constant variables as `constant`

A state variable whose value has been assigned at the time of declaration should be defined as `constant` to save gas.

### Proof of Concept

[`FraxlendPairCore.sol`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L51)
[`FraxlendPairDeployer.sol`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L49-L51)

### Recommended Mitigation Steps
Define `version`, `DEFAULT_MAX_LTV `, `GLOBAL_MAX_LTV `, and `DEFAULT_LIQ_FEE ` as `constant`.

## 2. Not to initialize uint variables to zero 

Uint variable's default value is zero. So, you can save gas by just defining uint variables.

### Proof of Concept

[`initialize()`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265-L272)
[`getAllPairAddresses()`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L127)
[`getCustomStatuses()`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L152)
[`globalPause()`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L402)
[`setRateContractWhitelist()`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66)
[`setFraxlendDeployerWhitelist()`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81)

### Recommended Mitigation Steps
You can just define `uint256 i` in `for` loops without initializing it to zero.

## 3. Use unchecked block if possible

Use unchecked blocks for arithmetic operations that can't underflow/overflow. 


### Proof of Concept

[`initialize()`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265-L272)
[`setRateContractWhitelist()`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66)
[`setFraxlendDeployerWhitelist()`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81)
### Recommended Mitigation Steps
You can put `++i` or `i++` in for loops in an unchecked block.

## 4. Use !=0 instead of > 0

!= 0 is a cheaper operation compared to > 0, when dealing with uint. > 0 can be replaced with != 0 for gas optimization.


### Proof of Concept

[`borrowAsset()`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L754)
[`requireValidInitData()`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L66)

### Recommended Mitigation Steps

Replace > 0 with != 0 when comparing unsigned integer variables to save gas.

## 5. Define variables as immutable

When a variable is just set once during deployment, it can be defined as immutable to save gas.

### Proof of Concept

[`FraxlendPairDeployer.sol`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L57-L59)

### Recommended Mitigation Steps
Define `CIRCUIT_BREAKER_ADDRESS`, `COMPTROLLER_ADDRESS`, and `TIME_LOCK_ADDRESS` as immutable.

## 6. Use custom errors instead of `require()` statements with revert strings.  

Using custom errors can save gas instead of using string errors.

### Proof of Concept

[`requireValidInitData()`](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L68)

### Recommended Mitigation Steps

Use custom errors instead of `require()` statements with revert strings. 
