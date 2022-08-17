# #
# 1 – Missing Event for Critical Parameters Change


**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

**Proof of Concept**
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L398
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L317
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L330


**Recommendation:**
Add Event-Emit


# #
# 2 – For modern and more readable code; Update import usages

**Context:**
All Contracts

**Description:** 
Our Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct “polluted the source code” with an unnecessary object we were not using because we did not need it. This was breaking the rule of modularity and modular programming: “only import what you need” Specific imports with curly braces allow us to apply this rule better.


**Recommendation:**
import {symbol1, symbol2} from "filename.sol";


