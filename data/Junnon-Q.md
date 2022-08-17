## missing address 0 check

checking for address 0 is a best practice for avoiding some error because of 0 value parameters. Recalling the recent hack event <a href=https://twitter.com/samczsun/status/1554252024723546112>Nomad hack</a>, in some case not checking for 0 value can be fatal. 

Instance:
FraxlendPairCore.sol
1. FraxlendPairCore::constructor()
affected variable:
FraxlendPairCore::{
    CIRCUIT_BREAKER_ADDRESS,
    COMPTROLLER_ADDRESS,
    TIME_LOCK_ADDRESS,
    FRAXLEND_WHITELIST_ADDRESS,
    assetContract,
    collateralContract,
    oracleMultiply,
    oracleDivide,
    rateContract
}
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L151-L237

2. FraxlendPairCore::initialize()
affected variable:
FraxlendPairCore::{
    approvedBorrowers, (cannot added new if not filled first)
    approvedLenders (cannot added new if not filled first)
}
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265-L272

FraxlendPairDeployer
1. FraxlendPairDeployer::constructor()
affected variable 
FraxlendPaitDeployer::{
    CIRCUIT_BREAKER_ADDRESS,
    COMPTROLLER_ADDRESS,
    TIME_LOCK_ADDRESS,
    FRAXLEND_WHITELIST_ADDRESS
}
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L98-L108

2. FraxlendPairDeployer::deploy()
affected variable:
FraxlendPairDeployer::deploy::{
    _asset,
    _collateral,
    _rateContract
}
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L310-L343

NOTE:
actually `FraxlendPairDeployer` should deploy `FraxlendPair` which trigger FraxlendPairCore::{constructor, initialize}. both FraxlendPairDeployer and FraxlendPairCore have lack of checking the address 0

3. FraxlendPairDeployer::_deployFirst()
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L191-L234
4. FraxlendPairDeployer::_deploySecond()
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L242-L263


MITIGATION:
set the address(0) check 
```js
if(addr == address(0)) revert AddressZero();
```

## Add comment for unused function parameter
unused function parameters can make people confused while reading the code, add a comment for it will be explain why the parameters are unused. 

instance: 
VariableInterestRate.sol
VariableInterestRate::getNewRate()::_initData
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/VariableInterestRate.sol#L63

Mitigation:
Make it exist inside the function and give it comment
```js
    function getNewRate(bytes calldata _data, bytes calldata _initData) external pure returns (uint64 _newRatePerSec) {
        _initData; //give your comment here
        ...//the next implementation
    }
```

## Function parameters shadowing the existing variable
it's a best practice to avoid variable collisions but since the instances are `external view` function, it can be non-critical.
instance:
FraxlendPair.sol
1.FraxlendPair::maxWithdraw()
affected variable:
Ownable::owner
```js
    function maxWithdraw(address owner) external view returns (uint256) {
        return totalAsset.toAmount(balanceOf(owner), false);
    }
```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L144-L146
2. FraxlendPair::maxRedeem()
affected variable:
Ownable::owner
```js
    function maxRedeem(address owner) external view returns (uint256) {
        return balanceOf(owner);
    }
```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L148-L150

MITIGATION: 
change the parameters name to `_owner`

## Consider two-phase ownership transfer

since frax contracts is implemented openzeppelin's Ownable contract, owner can transfer the `owner` role with one-step function which may have a risk for transfering ownership to invalid account. 

instance:
FraxlendPairCore
```js
46  abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ownable, Pausable, ReentrancyGuard {
```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L46

FraxlendPairDeployer
```js 
44  contract FraxlendPairDeployer is Ownable {
```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L44

FraxlendPairWhitelist
```js
30  contract FraxlendWhitelist is Ownable {
```
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L30

MITIGATION:
I recommend to make ownership transfer to be two-phase, one for set the next owner, one to execute the transfership.
Example
```js
    address pendingOwner;
    function setPendingOwner(address _pendingOwner) external OnlyOwner{
        if(_pendingOwner == address(0)) revert AddressZero();
        pendingOwner = _pendingOwner;
        emit ChangePendingOwner();
    }

    function transferOwnership(address _nextOwner) external OnlyOwner{
        if(_nextOwner != pendingOwner) revert InvalidPendingOwner();
         _transferOwnership(_nextOwner);
    }
```
NOTE: if you considering this don't forget to change deployer contract too (FraxlendPairDeployer::_deploySecond).

