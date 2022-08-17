## FraxlendPairDeployer.sol

1.It is recommended to validate address before while deploying the contract.

 Refer following lines of codes and update for valid address check (i.e, zero address check)

 https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L98-L108
 
2.Add length check for string in `_deploySecond`
   https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L243

## FraxlendPair.sol

1.Can we change function name which could be more relevant.
   https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L204

   `setTimeLock`  => `setTimeLockAddress`

## FraxlendPairCore.sol
1.It could be better if we can validate the address while deloying the contract. Refer following lines of codes.
  https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L171-L175

2.It is suggested to validate the `shares` at following line of code. function name `liquidate`
   https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L911
    
  
  