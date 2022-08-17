#1 Missing natspec comment toShares

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/VaultAccount.sol#L14

toShares() was missing natspec comment. add natspec comment to toShares() to give knowledge to the user about the function and params

#2 Missing natspec comment toAmount

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/VaultAccount.sol#L31

toAmount() was missing natspec comment. add natspec comment to toAmount() to give knowledge to the user about the function and params

#3 Missing indexed field

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1145

Each event should use three indexed fields if there are three or more fields. add indexed in _swapperAddress.

#4 Typo

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L306

typo can decrease readibility so to increase it fix the typo from approcal  to approval.

#5 Missing immutable

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L57-L59

the state CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS and TIME_LOCK_ADDRESS can't be initialize by constructor. the constructor parameter mention state CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS and TIME_LOCK_ADDRESSS to initialize. so i suggest to add immutable on CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS and TIME_LOCK_ADDRESS.

#6 Made to a struct

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L32-L38

for mapping that have same location of mapping (address => bool) can be compacted to aWhitelist struct

#7 Unbounded loop an array can lead DOS

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81

As this array can grow quite large, the transaction’s gas cost could exceed the block gas limit and make it impossible to call this function at all.

#8 Missing check for immutable address

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L98

constructor have four params address, so to avoid vulnerability we suggest to consider add simple checkaddress(0) for the params
