### [G01] Initialising State Variables to Default Values
**Description:**
When initialising state variables to their default value it is cheaper to just leave the value blank. (~2,000 gas in deployment costs)

**LOC:**
[FraxlendPairConstants.sol#L47](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairConstants.sol#L47)
[LinearInterestRate.sol#L33](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L33)

**Recommendation:**
Remove = 0 from the variable initialisations.


### [G02] Custom Errors
**Description:**
As your using a solidity version > 0.8.4 you can replace revert strings with cheaper custom errors. (~12,000 gas on deployment and ~80 gas on execution)

**LOC:**
[FraxlendPairDeployer.sol#L205](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L205) 
[FraxlendPairDeployer.sol#L228](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L228)
[FraxlendPairDeployer.sol#L253](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L253)
[FraxlendPairDeployer.sol#L365-L368](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L365-L368)
[FraxlendPairDeployer.sol#L399](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L399)
[LinearInterestRate.sol#L57-L68](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L68)

**Recommendation:**
Replace revert strings with custom errors.


### [G03] Long Revert Strings
**Description:**
If you opt not to use custom errors keeping revert strings <= 32 bytes in length will save gas. (~9,000 gas on deployment and ~15 gas on execution)

**LOC:**
[FraxlendPairDeployer.sol#L205](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L205) 
[FraxlendPairDeployer.sol#L228](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L228)
[FraxlendPairDeployer.sol#L253](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L253)
[FraxlendPairDeployer.sol#L365-L368](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L365-L368)
[LinearInterestRate.sol#L57-L68](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L68)

**Recommendation:**
Either replace with custom errors or shorten the revert strings to be < 32 bytes in length.


### [G04] Require Statement Order
**Description:**
Require statements that are checking function inputs should come before any other computations. That way if they fail no gas is wasted.

**LOC:**
[FraxlendPairDeployer.sol#L249-L253](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L249-L253)

**Recommendation:**
Move the require statment to line 249, before the abi.decode.


### [G05] Unchecked Increments in for loops
**Description:**
When incrementing i in for loops there is no chance of overflow so unchecked can be used to save gas. (~30,000 gas on deployment and ~140 gas per iteration)

**LOC:**
[FraxlendPair.sol#L289](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289)
[FraxlendPair.sol#L308](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308)
[FraxlendPairCore.sol#L265](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265)
[FraxlendPairCore.sol#L270](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270)
[FraxlendWhitelist.sol#L51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51)
[FraxlendWhitelist.sol#L66](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66)
[FraxlendWhitelist.sol#L81](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81)
[SafeERC20.sol#L27](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L27)

**Recommendation:**
Change for loops from: 
```
for (uint256 i; i < 1; ++i) {
} 
to 
(uint256 i; i < 1;) {
	// for loop body
	unchecked { ++i; }
}
```


### [G06] Pre Increments in Loops
**Description:**
In for loops pre increments can be used to save a small amount of gas per iteration. (~500 gas on deployment and ~5 gas per iteration)

**LOC:**
[FraxlendPair.sol#L289](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289)
[FraxlendPair.sol#L308](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308)
[FraxlendPairCore.sol#L265](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265)
[FraxlendPairCore.sol#L270](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270)
[FraxlendPairDeployer.sol#L130](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L130)
[FraxlendPairDeployer.sol#L158](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L158)
[FraxlendPairDeployer.sol#L408](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L408)
[FraxlendWhitelist.sol#L51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51)
[FraxlendWhitelist.sol#L66](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66)
[FraxlendWhitelist.sol#L81](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81)
[SafeERC20.sol#L27](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L27)

**Recommendation:**
Change increments in for loops from i++ to ++i.

### [G07] x = x + y is Cheaper than x += y
**Description:**
When using x += y or x -= y with state variables it is slightly cheaper to use x = x + y instead. (~1,000 gas on deployment and ~15 gas on execution)

**LOC:**
[FraxlendPairCore.sol#L772-L773](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L772-L773)
[FraxlendPairCore.sol#L813-L815](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L813-L815)
[FraxlendPairCore.sol#L1008](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1008)

**Recommendation:**
Change usages of x +=/-= y to x = x +/- y.

**NOTE:** All gas estimate savings where done with tests in remix