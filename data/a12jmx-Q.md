QA

_________________________________________________________________________________________________________________________________________________

1.

Contract: SafeERC20.sol

	Initializing the uint8 in line 22 is unnecessary as they get set to 0 by default

Recommendation:
	
	uint8 i;

2.

Initializing the variable in the following for loops is unnecessary as they get set to 0 by default

Contract: FraxlendWhitelist.sol

	line 51
	line 66
	line 81
	
Contract: FraxlendPairCore.sol
	
	line 265
	line 270
	
Contract: FraxlendPair.sol

	line 289
	line 308
	
Contract: FraxlendPairDeployer.sol

	line 402
	
Recommendations:

	for (uint256 i; i < _addresses.length; i++)
	
	for (uint256 i; i < _approvedBorrowers.length; ++i)
	for (uint256 i; i < _approvedLenders.length; ++i)
	
	for (uint256 i; i < _lenders.length; i++)
	for (uint256 i; i < _borrowers.length; i++)
	
	for (uint256 i; i < _lengthOfArray; )
	
3.

Contract: FraxlendPairDeployer.sol

Not initializing uint256 i in line 126 and line 150 is excellent, but there is no need for this, and the for var to then get set to i = 0 in line 127 and line 152 as they get set to 0 by default. Both uint256 do not get used outside of the for loops throughout the rest of either function so this might as well be added inside the for loops.
	
Recommendation:

	for (uint256 i; i < _lengthOfArray; )