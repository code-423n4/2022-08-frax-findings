You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting:

 for (uint256 i = 0; i < _lenders.length; /** NOTE: Removed i++ **/ ) {
            
                 // Do the thing
                // Unchecked pre-increment is cheapest
              
                unchecked { ++i; }
 }  

     
Instances:

 https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L289-L308

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L127-L402

https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendWhitelist.sol#L51-L66-L81

