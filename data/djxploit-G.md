## For loop optimization
In line , https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L127, for loop can be optimized as :
for (uint i; i < _lengthOfArray; ) {
            _addresses[i] = deployedPairsByName[_deployedPairsArray[i]];
            unchecked {
                ++i;
            }
        }
Similarly in below lines also:
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L152
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L402

Also in line https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289, change the for loop as below to optimize gas :
```
uint len = _lenders.length;
for (uint256 i ; i < len; ) {
            // Do not set when _approval == false and _lender == msg.sender
            if (_approval || _lenders[i] != msg.sender) {
                approvedLenders[_lenders[i]] = _approval;
                emit SetApprovedLender(_lenders[i], _approval);
            }
           unchecked {
              ++i;
           }
        }
```
Similarly in below line also :
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L308
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L265
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L270
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L66
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L81

## Use >= or <= instead of > or < to save gas:
In line https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L173, change that to 
`if (_creationCode.length >= 13001) {`
Also in line :
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L217
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L247
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L638
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L712
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L955
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1119
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1200

## Optimize if statement to save gas:
In line, https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L477, optimize the if statement as below:
`if (_currentRateInfo.feeToProtocolRate) {`
Such optimizations can also done in below lines :
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L754
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L835
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1002
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1094


