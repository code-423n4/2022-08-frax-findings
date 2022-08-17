  # Gas Optimizations

## Summary Of Findings:

  | Issue 
-- | -- 
1 | Use `unchecked` blocks whenever possible
2 | Rewriting the `returnDataToString` function in a gas efficient way
3 | Optimizing the `for` loops

## Detailed Report on Gas Optimization Findings:

### 1. <ins>Use `unchecked` blocks whenever possible</ins>
Starting solidity 0.8.0, you have overflow and underflow protection without using safeMath library. But these checks cost extra gas. And in some cases when we are sure that the result wont overflow or underflow, we can avoid such checks by putting the calculations inside an `unchecked` block.

The following instances shows where this could be applied:
 1. File: [FraxlendPairCore.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol)
```solidity
//_addInterest function
Line 441:       uint256 _deltaTime = block.timestamp - _currentRateInfo.lastTimestamp;

Line 446:       uint256 _utilizationRate = (UTIL_PREC * _totalBorrow.amount) / _totalAsset.amount;

Line 468:       _interestEarned = (_deltaTime * _totalBorrow.amount * _currentRateInfo.ratePerSec) / 1e18;

Line 472-484:   _interestEarned + _totalBorrow.amount <= type(uint128).max &&
                _interestEarned + _totalAsset.amount <= type(uint128).max
              ) {
                _totalBorrow.amount += uint128(_interestEarned);
                _totalAsset.amount += uint128(_interestEarned);
                if (_currentRateInfo.feeToProtocolRate > 0) {
                    _feesAmount = (_interestEarned * _currentRateInfo.feeToProtocolRate) / FEE_PRECISION;

                    _feesShare = (_feesAmount * _totalAsset.shares) / (_totalAsset.amount - _feesAmount);

                    // Effects: Give new shares to this contract, effectively diluting lenders an amount equal to the fees
                    // We can safely cast because _feesShare < _feesAmount < interestEarned which is always less than uint128
                    _totalAsset.shares += uint128(_feesShare);

//_updateExchangeRate function
Line 536:       _price = _price / uint256(_answer);
Line 539:       _exchangeRate = _price / oracleNormalization;

//_redeem function
Line 643-644:   _totalAsset.amount -= _amountToReturn;
                _totalAsset.shares -= _shares;

//_borrowAsset function
Line 724:       userBorrowShares[msg.sender] += _sharesAdded;

//liquidateClean function
Line 1004-1012:  if (_leftoverBorrowShares > 0) {
                    // Write off bad debt
                    _sharesToAdjust = _leftoverBorrowShares;
                    _amountToAdjust = (_totalBorrow.toAmount(_sharesToAdjust, false)).toUint128();

                    // Effects: write to state
                    totalAsset.amount -= _amountToAdjust;

                    // Note: Ensure this memory stuct will be passed to _repayAsset for write to state
                    _totalBorrow.amount -= _amountToAdjust;
                    _totalBorrow.shares -= _sharesToAdjust;
                }

```

 2. File: [FraxlendPair.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol)
```solidity
//withdrawFees function
Line 252-253:  _totalAsset.amount -= uint128(_amountToTransfer);
               _totalAsset.shares -= _shares;
```

 3. File: [VaultAccount.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/VaultAccount.sol#L26)
```solidity
//toShares function
Line 26:       shares = shares + 1;

//toAmount function
Line 43:       amount = amount + 1;
```

After the above mentioned calculations are put inside an unchecked block, the gas savings as per foundry were as follows:

<ins>Foundry Gas report:</ins>
  | Contract |  Method  | Original Avg Gas Cost  | Optimized Avg Gas Cost  | Avg Gas Saved  
-- | :--:| :--:| :--:  | :--: | :--:    
1 | FraxlendPair | addInterest | 15853 | 14778 | 1075
2 | FraxlendPair | borrowAsset |  96541| 96472| 69
3 | FraxlendPair |  initialize | 179105 | 179041 | 64
4 | FraxlendPair |   leveragedPosition | 171595| 171524| 71
5 | FraxlendPair |  liquidate| 15723| 15666| 57  
6 | FraxlendPair |  redeem | 26594| 26381| 213
7 | FraxlendPair |  withdraw| 27728| 27510| 218
8 | FraxlendPairDeployer|  deploy | 5371074| 5345080 | 25994
9 | FraxlendPairDeployer|   deployCustom  | 3287310| 3271423| 15887
10 | FraxlendPairDeployer|  setCreationCode | 5944599| 5916718| 27881

Summing up the total average gas saved in all the functions, we get **71,529**.

### 2. <ins>Rewriting the `returnDataToString` function in a gas efficient way</ins>

In `SafeERC20.sol`, the original [returnDataToString](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L18-L34) function can be rewritten as follows to make it more gas efficient:
```solidity
    function returnDataToString(bytes memory data) internal pure returns (string memory) {
        if (data.length >= 64) {
            return abi.decode(data, (string));
        } else if (data.length == 32) {
            uint256 i = 0;   // saves casting operations from 256 to 8 bit
            while (i < 32 && data[i] != 0) {
                unchecked   //overflow safe.
                { ++i;
                }
            }
            bytes memory bytesArray = new bytes(i);
            //avoid redundant initilization to zero
            //limit of j is last value of i. calculated above. 
            //unchecked block for ++j
            for (uint256 j; j < i; ) {   
                bytesArray[i] = data[i];
                unchecked {
                    ++j;
                }
            }
            return string(bytesArray);
        } else {
            return "???";
        }
    }
```

The above single change reduced the gas usage in the `FraxlendPairDeployer` contract as given below:

<ins>Foundry Gas report:</ins>
  | Contract |  Method  | Original Avg Gas Cost  | Optimized Avg Gas Cost  | Avg Gas Saved  
-- | :--:| :--:| :--:  | :--: | :--:    
1 | FraxlendPairDeployer|  deploy |  5345080 | 5323605 |21475
2 | FraxlendPairDeployer|   deployCustom  |  3271423| 3258279 | 13144
3 | FraxlendPairDeployer|  setCreationCode |  5916718| 5893728 |22990

Summing up the total average gas saved in all the three functions, we get **57,609**.

### 3. <ins>Optimizing the `for` loops</ins>

The `for` loops in the code base generally follow the following pattern:
```solidity
      for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
            approvedBorrowers[_approvedBorrowers[i]] = true;
        }
```
This can be optimized in 3 ways:
```solidity
      len = _approvedBorrowers.length;  // 1. cache the array length
      for (uint256 i; i < len; ) {  // 2. remove redundant initialization of i
            approvedBorrowers[_approvedBorrowers[i]] = true;
            unchecked {  // 3. using unchecked block for incrementing the index
              ++i;
            }
        }
```
Note that the above optimization is already seen used in the [FraxlendPairDeployer.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol) file.

The following instances could be optimized in this way:

 1. [FraxlendPair.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol)
```solidity
Line 289:        for (uint256 i = 0; i < _lenders.length; i++) 
Line 308:        for (uint256 i = 0; i < _borrowers.length; i++) 
```
 2. [FraxlendPairCore.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol)
```solidity
Line 265:        for (uint256 i = 0; i < _approvedBorrowers.length; ++i)
Line 270:        for (uint256 i = 0; i < _approvedLenders.length; ++i) 
```
 3. [FraxlendWhitelist.sol](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol)
```solidity
Line 51:        for (uint256 i = 0; i < _addresses.length; i++)
Line 66:        for (uint256 i = 0; i < _addresses.length; i++)
Line 81:        for (uint256 i = 0; i < _addresses.length; i++)
```

After the above `for` loops were optimized, the gas savings as per foundry were as follows:

<ins>Foundry Gas report:</ins>
  | Contract |  Method  | Original Avg Gas Cost  | Optimized Avg Gas Cost  | Avg Gas Saved  
-- | :--:| :--:| :--:  | :--: | :--:    
1 | FraxlendPairDeployer|  deploy | 5323605| 5317939| 5666
2 | FraxlendPairDeployer|   deployCustom  | 3258279 | 3254774 | 3505
3 | FraxlendPairDeployer|  setCreationCode | 5893728| 5887613 | 6115
4 | FraxlendWhitelist|  setFraxlendDeployerWhitelist | 24891| 24828| 63
5 | FraxlendWhitelist|    setOracleContractWhitelist  | 164238| 163720| 518
6 | FraxlendWhitelist|   setRateContractWhitelist  | 49032 | 48901 | 131


Summing up the total average gas saved in all the functions, we get **15,998**.

## Conclusions:

The above 3 major changes saved a very significant amount of gas in the overall code base. The exact amounts are tabulated below:

  | Issue | Gas Saved
-- | -- | -- 
1 | Use `unchecked` blocks whenever possible | 71529
2 | Rewriting the `returnDataToString` function in a gas efficient way | 57609
3 | Optimizing the `for` loops | 15998

### TOTAL GAS SAVED = **1,45,136**
