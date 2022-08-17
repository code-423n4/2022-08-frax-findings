Overall, the code base was written quite well imo and had tons of helpful comments. Additionally, the corresponding architecture videos that the sponsor provided helped me get up to speed quite quickly.

Issue/Suggestion #1: Potentially consider adding the `whenNotPaused` modifier on core functions in `FraxLendPairCore`.

The `whenNotPaused` modifier exists on several core functions such as `deposit`, `mint`, `borrowAsset`, `liquidate`, `liquidateClean`, `leveragedPosition`, however is not visible on `addCollateral` and a few others.

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L786

My understanding is that the intention of the protocol and code is to protect borrowers. For example, in the case that the oracle price changes during a pause, borrowers are able to addCollateral to prevent liquidation. However, in the Aave lendingPool for example, it appears that all core functions contain the `whenNotPaused` modifier: https://github.com/aave/protocol-v2/blob/master/contracts/protocol/lendingpool/LendingPool.sol. 

In the Aave case, all core functions are paused so if we consider the same case where the oracle price changes during a pause, there would be a race between the liquidator and the borrower to squeeze their transaction in first. So, this perhaps may not be desirable having the borrower and the liquidator on even footing grounds during a pause and unpause. I did want to mention this in my report so the team is aware and curious what others wardens/the judge think as well.


Issue/Suggestion #2: Potentially consider adding checks for known bad addresses (address(0), address(this), etc).

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L727

It is noteworthy that any incorrect function calls being passed with address(0) or address(this) will be lost in several functions. For example, calling the above function in the snippet with address(0) or address(this) will result in funds being lost.
Again there are gas tradeoffs, etc here so this is ultimately up to the team. One could argue this would potentially be a better/safer user experience as it protects against any front-end errors or failure to validate addresses when calling any of the core functions.

Issue #3:
Spelling Error here: 

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L407

```   
/// @dev Can only called once per block
```

should be 
```   
/// @dev Can only be called once per block
``` 