## Loop can be Cheaper 

Loop is a costly operation that will consume a lot of gas, we can make it cheaper with several small changes in the operation. This is what we should do to reduce gas consumtion in loop operation:

1. Avoid declaring 0 value to index. `uint` have a default value 0, passing it with 0 value just consume gas for nothing. 
Example
```js 
    uint i ;
    // is cheaper than 
    uint i = 0;
```
2. Avoid using `array.length` for loop, instead pass it to new variable. `array.length` will cost gas for every reading, in loop case it will read every loop step, for avoid that pass `array.length` in variable first. 
Example
```js
    //expensive version
    for (uint i; i < array.length ) {}

    //cheaper version
    uint length = array.length;
    for (uint i; i < length ){}
```
3. Using `++i` instead of `i++`, because `++i` is cheaper.
4. Using `unchecked` for operation increment of index
Example 
```js
    //expensive version
    for (uint i; i < length; i++){
        ...//implementation
    }

    //cheaper version
    for (uint i; i < length) {
        ... // implementation
        unchecked {
            ++i;
        }
    }
```

Instance:
note: external view function now using gas for operation but if dev want it still better to fix it for cleaner and consistent code.

FraxlendPair.sol
1. FraxlendPair::setApprovedLenders()
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289
2. FraxlendPair::setApprovedBorrowers()
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308

FraxlendPairCore.sol
1. FraxlendPairCore::initialize()
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265
2. FraxlendPairCore::initialize()
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270

FraxlendPairDeployer.sol
1. FraxlendPairDeployer::getAllPairAddresses() (external view)
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L127
2. FraxlendPairDeployer::getCustomStatuses() (external view)
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L152
3. FraxlendPairDeployer::globalPause()
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L398

FraxlendPairWhitelist.sol
1. FraxlendPairWhitelist::setOracleContractWhitelist()
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51
2. FraxlendPairWhitelist::setRateContractWhitelist()
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66
3. FraxlendPairWhitelist::setFraxlendDeployerWhitelist()
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81

## Variable that declaring in the contract that never changed can be marked as `constants`

Marking a variable as `constants` can make save gas and make sure the value never changed by any activities of contract, and it's now using a storage slot.

Instance:
FraxlendDeployer.sol::FraxlendDeployer::{DEFAULT_MAX_LTV, GLOBAL_MAX_LTV, DEFAULT_LIQ_FEE}
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L49-L51

## Variable that only changed in `constructor` can be marked as immutable

Immutable variable more cheaper that the default one, it can make sure that the variable cannot changed too. 

Instance:
FraxlendDeployer.sol::FraxlendDeployer::{CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS, TIME_LOCK_ADDRESS}
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L57-L59

## Unused Import

Importing an unused file will be cost gas for deploying the contract. So it's better get rid all unused import.

Instance:
FraxlendPair.sol
```js 
import "./interfaces/IERC4626.sol";
import "./interfaces/IFraxlendWhitelist.sol";
import "./interfaces/IRateCalculator.sol";
import "./interfaces/ISwapper.sol";
```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L36-L39

## Early revert 

for saving gas when transaction is reverted, `revert` must be placed as early as possible, in some instance it can be checked first before everything else.

Instance:
FraxlendPair.sol
FraxlendPair::withdrawFees()
```js
    //reason 

255        // Effects: write to states
256        // NOTE: will revert if _shares > balanceOf(address(this))
257        _burn(address(this), _shares);

    //_shares > balanceOf(address(this)) can be checked in the begining of function
```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L234

FraxlendPairCore.sol
FraxlendPairCore::{leveragePosition, repayAssetWithCollateral}
```js
///leveragePosition
        if (!swappers[_swapperAddress]) {
            revert BadSwapper();
        }
        if (_path[0] != address(_assetContract)) {
            revert InvalidPath(address(_assetContract), _path[0]);
        }
        if (_path[_path.length - 1] != address(_collateralContract)) {
            revert InvalidPath(address(_collateralContract), _path[_path.length - 1]);
        }
```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1083-L1091
and
```js
///repayAssetWithCollateral
        if (!swappers[_swapperAddress]) {
            revert BadSwapper();
        }
        if (_path[0] != address(_collateralContract)) {
            revert InvalidPath(address(_collateralContract), _path[0]);
        }
        if (_path[_path.length - 1] != address(_assetContract)) {
            revert InvalidPath(address(_assetContract), _path[_path.length - 1]);
        }
```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1083-L1091
can be checked before 
```js
        _addInterest();
        _updateExchangeRate();
```
in both function