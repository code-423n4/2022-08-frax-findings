-> X = X + Y IS CHEAPER THAN X += Y (same for X = X - Y IS CHEAPER THAN X -= Y)

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=_totalAsset.amount%20%2D%3D%20uint128(_amountToTransfer)
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=_totalAsset.shares%20%2D%3D%20_shares%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_totalBorrow.amount%20%2B%3D%20uint128(_interestEarned)%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_totalAsset.amount%20%2B%3D%20uint128(_interestEarned)%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_totalAsset.shares%20%2B%3D%20uint128(_feesShare)%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_totalAsset.amount%20%2B%3D%20_amount%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_totalAsset.shares%20%2B%3D%20_shares%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_totalAsset.amount%20%2D%3D%20_amountToReturn%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_totalAsset.shares%20%2D%3D%20_shares%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_totalBorrow.amount%20%2B%3D%20_borrowAmount%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_totalBorrow.shares%20%2B%3D%20uint128(_sharesAdded)%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=userBorrowShares%5Bmsg.sender%5D%20%2B%3D%20_sharesAdded%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_totalBorrow.amount%20%2D%3D%20_amountToRepay%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_totalBorrow.shares%20%2D%3D%20_shares%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=userBorrowShares%5B_borrower%5D%20%2D%3D%20_shares%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=totalAsset.amount%20%2D%3D%20_amountToAdjust%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_totalBorrow.amount%20%2D%3D%20_amountToAdjust%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=_totalBorrow.shares%20%2D%3D%20_sharesToAdjust%3B


->STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas)


https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=IERC20%20internal%20immutable%20assetContract%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=IERC20%20public%20immutable%20collateralContract%3B

-> REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#:~:text=%22FraxlendPairDeployer%3A%20create2%20failed%22)%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#:~:text=%22FraxlendPairDeployer%3A%20Pair%20name%20must%20be%20unique%22)%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#:~:text=%22FraxlendPairDeployer%3A%20_maxLTV%20is%20too%20large%22)%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#:~:text=%22FraxlendPairDeployer%3A%20Only%20whitelisted%20addresses%22
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#:~:text=%22LinearInterestRate%3A%20_minInterest%20%3C%20MAX_INT%20%26%26%20_minInterest%20%3C%3D%20_vertexInterest%20%26%26%20_minInterest%20%3E%3D%20MIN_INT%22
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#:~:text=%22LinearInterestRate%3A%20_maxInterest%20%3C%3D%20MAX_INT%20%26%26%20_vertexInterest%20%3C%3D%20_maxInterest%20%26%26%20_maxInterest%20%3E%20MIN_INT%22
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#:~:text=%22LinearInterestRate%3A%20_vertexUtilization%20%3C%20MAX_VERTEX_UTIL%20%26%26%20_vertexUtilization%20%3E%200%22

->USING > 0 COSTS MORE GAS THAN != 0 WHEN USED ON A UINT IN A REQUIRE() STATEMENT

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#:~:text=_maxInterest%20%26%26%20_maxInterest%20%3E%20MIN_INT%22-,)%3B,)%3B,-%7D

->SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#:~:text=)%3B-,require(,)%3B,-require(
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#:~:text=)%3B-,require(,)%3B,-require(
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/LinearInterestRate.sol#:~:text=_maxInterest%20%26%26%20_maxInterest%20%3E%20MIN_INT%22-,)%3B,)%3B,-%7D


-> ++i costs less gas compared to i++ or i += 1 (Also --i costs less gas compared to i--- or i -= 1)

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=_lenders.length%3B-,i%2B%2B)%20%7B,-//%20Do%20not%20set
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=_borrowers.length%3B-,i%2B%2B)%20%7B,-//%20Do%20not%20set
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=_lenders.length%3B-,i%2B%2B)%20%7B,-//%20Do%20not%20set
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=_borrowers.length%3B-,i%2B%2B)%20%7B,-//%20Do%20not%20set
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#:~:text=deployedPairsByName%5B_deployedPairsArray%5Bi%5D%5D%3B-,unchecked%20%7B,%7D,-%7D
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#:~:text=%7D)%3B-,unchecked%20%7B,%7D,-%7D
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#:~:text=_addresses.length%3B-,i%2B%2B)%20%7B,-oracleContractWhitelist%5B_addresses%5Bi
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#:~:text=_addresses.length%3B-,i%2B%2B)%20%7B,-rateContractWhitelist%5B_addresses%5Bi
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#:~:text=_addresses.length%3B-,i%2B%2B)%20%7B,-fraxlendDeployerWhitelist%5B_addresses%5Bi


-> USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=uint16%20_DEFAULT_PROTOCOL_FEE%2C
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairConstants.sol#:~:text=uint16%20internal%20constant%20DEFAULT_PROTOCOL_FEE%20%3D%200%3B


->IT COSTS MORE GAS TO INITIALIZE NON-CONSTANT/NON-IMMUTABLE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED

If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.


https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_lenders.length%3B%20i%2B%2B)%20%7B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_borrowers.length%3B%20i%2B%2B)%20%7B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_approvedBorrowers.length%3B%20%2B%2Bi)%20%7B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_approvedLenders.length%3B%20%2B%2Bi)%20%7B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_lenders.length%3B%20i%2B%2B)%20%7B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_borrowers.length%3B%20i%2B%2B)%20%7B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#:~:text=uint256%20i%3B-,for%20(i%20%3D%200%3B%20i%20%3C%20_lengthOfArray%3B%20)%20%7B,-_addresses%5Bi%5D
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#:~:text=PairCustomStatus%5B%5D(_lengthOfArray)%3B-,for%20(i%20%3D%200%3B%20i%20%3C%20_lengthOfArray%3B%20)%20%7B,-_pairCustomStatuses%5Bi%5D
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#:~:text=for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_lengthOfArray%3B%20)%20%7B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#:~:text=)%20external%20onlyOwner%20%7B-,for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_addresses.length%3B%20i%2B%2B)%20%7B,-oracleContractWhitelist%5B_addresses%5Bi
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#:~:text=)%20external%20onlyOwner%20%7B-,for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_addresses.length%3B%20i%2B%2B)%20%7B,-rateContractWhitelist%5B_addresses%5Bi
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#:~:text=)%20external%20onlyOwner%20%7B-,for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20_addresses.length%3B%20i%2B%2B)%20%7B,-fraxlendDeployerWhitelist%5B_addresses%5Bi


->USING BOOLS FOR STORAGE INCURS OVERHEAD

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=mapping(address%20%3D%3E%20bool)%20public%20approvedBorrowers%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#:~:text=mapping(address%20%3D%3E%20bool)%20public%20approvedLenders%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#:~:text=mapping(address%20%3D%3E%20bool)%20public%20deployedPairCustomStatusByAddress%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#:~:text=mapping(address%20%3D%3E%20bool)%20public%20oracleContractWhitelist%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#:~:text=mapping(address%20%3D%3E%20bool)%20public%20rateContractWhitelist%3B
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#:~:text=mapping(address%20%3D%3E%20bool)%20public%20fraxlendDeployerWhitelist%3B






