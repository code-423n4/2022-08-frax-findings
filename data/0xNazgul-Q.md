## [NAZ-L1] Value Range Validity
**Severity** Low
**Context**: [`FraxlendPairCore.sol#L193`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L193), [`FraxlendPairCore.sol#L235-L236`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L235-L236), [`FraxlendPair.sol#L215`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L215)

**Description**:
These functions doesn't have any checks to ensure that the variables being set is within some kind of value range.

**Recommendation**:
Each variable input parameter updated should have it's own value range checks to ensure their validity.


## [NAZ-L2] Missing Equivalence Checks in Setters
**Severity**: Low
**Context**: [`FraxlendWhitelist.sol#L50`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L50), [`FraxlendWhitelist.sol#L65`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L65), [`FraxlendWhitelist.sol#L80`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L80), [`FraxlendPair.sol#L204`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L204), [`FraxlendPair.sol#L215`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L215)

**Description**:
Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.

**Recommendation**:
This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.


## [NAZ-L3] Missing Zero-address Validation
**Severity**: Low
**Context**: [`FraxlendPairCore.sol#L172-L175`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L172-L175), [`FraxlendPairCore.sol#L190-L191`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L190-L191), [`FraxlendPairCore.sol#L225`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L225), [`FraxlendPair.sol#L204`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L204), [`FraxlendPair.sol#L274`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L274), [`FraxlendPairDeployer.sol#L104-L107`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L104-L107)

**Description**:
Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

**Recommendation**:
Consider adding explicit zero-address validation on input parameters of address type.


## [NAZ-L4] Missing Events In Initialize Functions
**Severity**: Low
**Context**: [`FraxlendPairCore.sol#L245`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L245)

**Description**:
None of the initialize functions emit emit init-specific events. They all however have the initializer modifier (from Initializable) so that they can be called only once. Off-chain monitoring of calls to these critical functions is not possible.

**Recommendation**:
It is recommended to emit events in your initialization functions.


## [NAZ-N5] Unintuitive Variable Name
**Severity**: Informational
**Context**: [`FraxlendPairCore.sol#L602 (mint => lendAssets)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L602)

**Description**:
The variable name is either not as it describes or can be more descriptive.

**Recommendation**:
Consider changing all occurrences of these variables to be more intuitive.


## [NAZ-N6] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`VaultAccount.sol#L16`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol#L16), [`VaultAccount.sol#L33`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/VaultAccount.sol#L33), [`FraxlendPairCore.sol#L81-L89`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L81-L89), [`FraxlendPair.sol#L160-L167`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L160-L167), [`FraxlendPairDeployer.sol#L49-L51`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L49-L51), [`FraxlendPairDeployer.sol#L57-L62`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L57-L62)

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the `mixedCase` format unless variables are declared as `constant` in which case they utilize the `UPPER_CASE_WITH_UNDERSCORES` format. Internal functions should lead with an underscore.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the Solidity style guide.


## [NAZ-N7] Code Contains Empty Blocks
**Severity**: Informational
**Context**: [`VariableInterestRate.sol#L57`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L57), [`FraxlendWhitelist.sol#L40`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L40), [`FraxlendPair.sol#L68`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L68), [`FraxlendPairDeployer.sol#L406`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L406)

**Description**:
It's best practice that when there is an empty block, to add a comment in the block explaining why it's empty.

**Recommendation**:
Consider adding `/* Comment on why */` to the empty blocks.


## [NAZ-N8] Code Structure Deviates From Best-Practice
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts)

**Description**:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.  Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Some constructs deviate from this recommended best-practice: Events are in the middle of contracts. External/public functions are mixed with internal/private ones.

**Recommendation**:
Consider adopting recommended best-practice for code structure and layout.


## [NAZ-N9] Unindexed Event Parameters
**Severity** Informational
**Context**: [`FraxlendPairCore.sol#L376-L382`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L376-L382), [`FraxlendPairCore.sol#L389`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L389), [`FraxlendPair.sol#L200`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L200), [`FraxlendPair.sol#L228`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L228), [`FraxlendPair.sol#L268`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L268)

**Description**:
Parameters of certain events are expected to be indexed so that they’re included in the block’s bloom filter for faster access. Failure to do so might confuse off-chain tooling looking for such indexed events.

**Recommendation**:
Consider adding the indexed keyword to event parameters that should include it.


## [NAZ-N10] Use Underscores for Number Literals
**Severity**: Informational
**Context**: [`LinearInterestRate.sol#L34`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#L34), [`VariableInterestRate.sol#L35-L36`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L35-L36), [`VariableInterestRate.sol#L40-L42`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L40-L42), [`FraxlendPairConstants.sol#L41`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairConstants.sol#L41), [`FraxlendPairCore.sol#L194`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L194), [`FraxlendPairDeployer.sol#L49`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L49), [`FraxlendPairDeployer.sol#L51`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L51), [`FraxlendPairDeployer.sol#L171`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L171), [`FraxlendPairDeployer.sol#L173-L174`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L173-L174)

**Description**:
There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

**Recommendation**:
Consider using underscores for number literals to improve its readability.


## [NAZ-N11] Spelling Errors
**Severity**: Informational
**Context**: [`VariableInterestRate.sol#L32 (calulcating => calculating)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/VariableInterestRate.sol#L32), [`FraxlendWhitelist.sol#L47 (oralce => oracle)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L47), [`FraxlendPairCore.sol#L231 (whitelist => whitlist)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L231), [`FraxlendPairCore.sol#L893 (occured => occurred)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L893), [`FraxlendPairCore.sol#L896 (liabilites => liabilities)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L896), [`FraxlendPairCore.sol#L945 (Liquidate => LiquidateClean)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L945), [`FraxlendPairCore.sol#L992 (calced => Calculated)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L992), [`FraxlendPairCore.sol#L992 (calcs => calculations)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L992), [`FraxlendPairCore.sol#L1010 (stuct => struct)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1010), [`FraxlendPair.sol#L286 (whos => who's)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L286), [`FraxlendPair.sol#L287 (approcal => approval)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L287), [`FraxlendPair.sol#L305 (whos => who's)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L305), [`FraxlendPair.sol#L306 (approcal => approval)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L306), [`FraxlendPairDeployer.sol#L168 (accomodate => accommodate)`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L168)

**Description**:
Spelling errors in comments can cause confusion to both users and developers.

**Recommendation**:
Consider checking all misspellings to ensure they are corrected.


## [NAZ-N12] Floating Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts)

**Description**:
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

**Recommendation**: 
Consider locking the pragma version.