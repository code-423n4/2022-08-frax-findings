1.

Contract: SafeERC20.sol
	
When dealing with function arguments such as in line 22, there is no inherent benefit to set to uint8 because the compiler does not pack these values. Using smaller-size uints such as uint8 is only more efficient when you can pack variables into the same storage slot, such as in structs. In this case, a function and in a loop, uint256 is more gas efficient than uint8. It brings a difference of about 1500 gas per contract deployment.

Recommendation:

	uint256;

2.

Switching the i++ to ++i in for loops will save about 5 gas per iteration
	
Contract: SafeERC20.sol
	
	line 27
	
Contract: FraxlendWhitelist.sol	

	line 51
	line 66
	line 81
	
Contract: FraxlendPair.sol

	line 289
	line 308
	
Recommendations:

	for (i = 0; i < 32 && data[i] != 0; ++i)
	
	for (uint256 i = 0; i < _addresses.length; ++i)

	for (uint256 i = 0; i < _lenders.length; ++i)
	for (uint256 i = 0; i < _borrowers.length; ++i)