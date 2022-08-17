## make emit of the old varible in importent setter functions, thats best practice
```
     emit ChangeFee(_newFee);

```
change to 
```
_oldFee=currentRateInfo.feeToProtocolRate;
currentRateInfo.feeToProtocolRate= _newFee;
emit ChangeFee(_newFee,_oldFee);
```
`FraxlendPair.sol:221: `
## no address zero check
```
           // Deployer contract
            DEPLOYER_ADDRESS = msg.sender;
            CIRCUIT_BREAKER_ADDRESS = _circuitBreaker;
            COMPTROLLER_ADDRESS = _comptrollerAddress;
            TIME_LOCK_ADDRESS = _timeLockAddress;
            FRAXLEND_WHITELIST_ADDRESS = _fraxlendWhitelistAddress;
        }
```
https://github.com/code-423n4/2022-08-frax/blob/b58c9b72f5fe8fab81f7436504e7daf60fd124e3/src/contracts/FraxlendPairCore.sol#L170-L176
##  block.timestmap in 2106 can overflow and it will cause a revert 
```            
uint256 _deltaTime = block.timestamp - _currentRateInfo.lastTimestamp;
```
the uniswap protocol has comment on block.timestmap that when it overflows it will go to 0 and not oveflow because of using solidty version less then 0.8.0 but here its going to revert 
so put a unchecked to make sure it dosnt revert.
##  typos 
instead of calulcating
use : calculating
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/VariableInterestRate.sol#L32
## no slippage check which can give an attacker an opportunity to  arbitrage
```      ISwapper(_swapperAddress).swapExactTokensForTokens(
            _collateralToSwap,
            _amountAssetOutMin,
            _path,
            address(this),
            block.timestamp
        );
```
there is no slippage check add one that a user can change to anything they like.
`FraxlendPairCore.sol:1190: `
`FraxlendPairCore.sol:1109: ` 
