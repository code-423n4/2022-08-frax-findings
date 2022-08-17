# G-01 Public functions not called in the contract should be marked External

Declaring functions as public costs more gas. Replace them with external wherever possible.

Instances of this issue :

[FraxlendPair.sol#L74](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L74)
[FraxlendPair.sol#L78](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L78)
[FraxlendPair.sol#L84](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L84)
[FraxlendPair.sol#L89](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L89)
[FraxlendPair.sol#L100](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L100)

# G-02 Functions with singular simple functionalities can be removed

Functions are expensive. So, if possible, simpler function definitions should be remodified into direct use operations wherever needed.

Instances of this issue :

[FraxlendPair.sol#L85](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L85)
[LinearInterestRate.sol#L40](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L40)
[VariableInterestRate.sol#L46](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/VariableInterestRate.sol#L46)
[VariableInterestRate.sol#L57](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/VariableInterestRate.sol#L57)



# G-03 Functions that perform only Read-Only instructions should be marked as View

Within function declaration, using view instead of pure without affecting the functionality, may save gas.

Instances of this issue :

[FraxlendPair.sol#L136](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L136)
[FraxlendPair.sol#L140](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L140)

# G-04 Functions with Admin-only Access can be marked payable

Functions that are guaranteed to revert when called by normal users, because they use onlyOwner / onlyAdmin etc. modifiers, can be marked payable to save gas. This skips some checks of whether payment was provided.

Instances of this issue :

[FraxlendPair.sol#L204](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L204)
[FraxlendPair.sol#L234](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L234)
[FraxlendPair.sol#L274](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L274)
[FraxlendPairDeployer.sol#L170](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L170)
[FraxlendWhitelist.sol#L50](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L50)
[FraxlendWhitelist.sol#L65](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L65)
[FraxlendWhitelist.sol#L80](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L80)

# G-05 Change if-revert pattern to require-revert pattern

This modification saves gas.

Instances of this issue :

[FraxlendPair.sol#L216](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L216)
[FraxlendPair.sol#L217](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L217)
[FraxlendPair.sol#L247](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L247)
[FraxlendPair.sol#L318](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L318)
[FraxlendPair.sol#L331](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L331)
[FraxlendPairCore.sol#L196](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L196)
[FraxlendPairCore.sol#L206](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L206)
[FraxlendPairCore.sol#L210](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L210)


# G-06 Use IsZero EVM opcode for Zero address / Zero value checks

Since there is a direct opcode for this operation, implementing this saves gas.

Instances of this issue :

[FraxlendPair.sol#L240](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L240)
[FraxlendPairCore.sol#L206](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L206)
[FraxlendPairCore.sol#L210](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L210)
[FraxlendPairCore.sol#L254](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L254)
[FraxlendPairCore.sol#L257](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L257)


# G-07  X= X+Y  is more efficient than  X += Y

The statement X += Y goes through the creation of a temporary variable in the compiler memory, which can be avoided.

Same goes for X -= Y.

Instances of this issue :

[FraxlendPair.sol#L252](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L252)
[FraxlendPair.sol#L253](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L253)
[FraxlendPairCore.sol#L566](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L566)
[FraxlendPairCore.sol#L567](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L567)


# G-08 Avoid looking up Array length in a loop

Array length lookup is a call to the memory and we should avoid calling it repeatedly such as in a loop.

Instances of this issue :

[FraxlendPair.sol#L289](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L289)
[FraxlendPair.sol#L308](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L308)
[FraxlendPairCore.sol#L265](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L265)
[FraxlendPairCore.sol#L270](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L270)
[FraxlendWhitelist.sol#L51](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L51)
[FraxlendWhitelist.sol#L66](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L66)
[FraxlendWhitelist.sol#L81](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L81)

# G-09 ++i is more gas efficient than i++ and  i+=1

Using i++ involves storing a temporary value of i. This operation is avoidable by using pre-increment operator. You might have to tweak the logic a little bit.

Instances of this issue :

[FraxlendPair.sol#L289](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L289)
[FraxlendPair.sol#L308](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L308)
[FraxlendPairDeployer.sol#L130](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L130)
[FraxlendPairDeployer.sol#L158](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L158)
[FraxlendPairDeployer.sol#L408](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L408)
[FraxlendWhitelist.sol#L51](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L51)
[FraxlendWhitelist.sol#L66](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L66)
[FraxlendWhitelist.sol#L81](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L81)

# G-10 Use unchecked to skip overflow/underflow checks on Loop increment variables

Starting from version 0.8.0, Solidity has default arithmetic overflow and underflow checks in place. Use unchecked {} wherever you are sure about the calculations not going out of range. Loop increment variables always have conditions in place, and should be unchecked.

Instances of this issue :

[FraxlendPair.sol#L289](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L289)
[FraxlendPair.sol#L308](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L308)
[FraxlendWhitelist.sol#L51](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L51)
[FraxlendWhitelist.sol#L66](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L66)
[FraxlendWhitelist.sol#L81](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L81)

# G-11 Do not initialize state variables to their default values

Unsigned integers have 0 as the default value in Solidity, boolean has false and so on. Initializing state variables to their default values uses extra gas, with no significance.

Instances of this issue :

[FraxlendPairConstants.sol#L47](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairConstants.sol#L47)
[LinearInterestRate.sol#L33](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L33)

# G-12 State variable has been declared but not used

State variables are expensive. Using them to declare information that is not used in the code wastes gas. Same information can be conveyed via Comments or local variables etc.

Instances of this issue :

[FraxlendPairCore.sol#L51](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L51)

# G-13 Use uint256(1) and uint256(2) in place of boolean values

Booleans are more expensive than uint256 or any type that takes up a full
word because each write operation emits an extra SLOAD to first read the
slot's contents, replace the bits taken up by the boolean, and then write
back. This is the compiler's defense against contract upgrades and
pointer aliasing, and it cannot be disabled.

Instances of this issue :

[FraxlendPairCore.sol#L133](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L133)
[FraxlendPairCore.sol#L136](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L136)
[FraxlendPairDeployer.sol#L199](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L199)
[FraxlendPairDeployer.sol#L200](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L200)


# G-14 Avoid Unnecessary Casting of variables

Instances of this issue :

[FraxlendPairCore.sol#L522](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L522)

# G-15 Variables that have been assigned a constant value can be declared constant

Instances of this issue :

[FraxlendPairDeployer.sol#L49](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L49)
[FraxlendPairDeployer.sol#L50](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L50)
[FraxlendPairDeployer.sol#L51](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L51)

# G-16 Error String of require statement should be < 32 Bytes

This saves deployment gas.

Instances of this issue :

[FraxlendPairDeployer.sol#L205](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L205)
[FraxlendPairDeployer.sol#L228](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L228)
[FraxlendPairDeployer.sol#L253](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L253)
[FraxlendPairDeployer.sol#L365](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L365)
[FraxlendPairDeployer.sol#L368](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L368)
[LinearInterestRate.sol#L59](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L59)
[LinearInterestRate.sol#L63](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L63)
[LinearInterestRate.sol#L67](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L67)


Alternatively, you can also use Custom Errors.
See :  [https://blog.soliditylang.org/2021/04/21/custom-errors/](https://blog.soliditylang.org/2021/04/21/custom-errors/)

# G-17 Use abi.encodePacked in place of abi.encode

abi.encodePacked() is more gas efficient than abi.encode().

Instances of this issue :

[FraxlendPairDeployer.sol#L213](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L213)
[FraxlendPairDeployer.sol#L374](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairDeployer.sol#L374)
[LinearInterestRate.sol#L47](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L47)
[VariableInterestRate.sol#L53](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/VariableInterestRate.sol#L53)



# G-18 Constructor is Empty

If you're using a constructor, there should atleast be something inside it.

Instances of this issue :

[FraxlendWhitelist.sol#L40](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L40)

# G-19 Split a require statement checking multiple conditions

If a require statement checks multiple conditions using the && operator, it is more gas efficient to split it into multiple require statements.

Instances of this issue :

[LinearInterestRate.sol#L58](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L58)
[LinearInterestRate.sol#L62](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L62)
[LinearInterestRate.sol#L66](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L66)

# G-20 Use assembly switch statement in place of if-else constructs

We shall use assembly wherever possible, it saves gas.

Instances of this issue :

[LinearInterestRate.sol#L86](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L86)
[VariableInterestRate.sol#L75](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/VariableInterestRate.sol#L75)


# G-21 Use bytes datatype for constants instead of strings

Use a fixed size bytes datatype like bytes4 in place of string.

Instances of this issue :

[VariableInterestRate.sol#L47](https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/VariableInterestRate.sol#L47)

# G-22 May deploy contracts using Clones

This process saves significant amount of gas compared to the proxy implementation.

Here is more information about it : [Youtube Link for Clone Deployment](https://www.youtube.com/watch?v=3Mw-pMmJ7TA)

# G-23 Use Function ordering via Method ID

Prioritise most called functions in the method ID table by renaming them. This saves gas for the most called functions.

[Use this tool to find more gas efficient names](https://emn178.github.io/solidity-optimize-name/)

