## Title 
Unused variable after declaration (Version)

Vulnerability details *
## Impact
Detailed description of the impact of this finding.

There’s no function to change or modify the string variable; version. This data type is stored on slot zero of the contract. This also combines to the byte code of the contract. 

## Proof of Concept
Provide direct links to all referenced code in GitHub. Add screenshots, logs, or any other relevant proof that illustrates the concept.
https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L51

## Recommended Mitigation Steps

If variable is needed in contract, it could be made immutable or constant to prevent occupying storage. This also can still be viewed with the public visibility.