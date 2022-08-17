
##  Table of Contents:
N-01 Event is missing indexed fields
N-02 Large multiple of 10 should use scientific notation
N-03 NatSpec is incomplete

L-01 abi.encodePacked() should not be used with dynamic types  when passing the result to a hash function such as keccak256()



## N-01 Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

There are 10 extances in 2 files:
File: contracts/FraxlendPairCore.sol
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L376-L382
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L389
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L696-L701
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L760
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L795-L800
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L846
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L897-L904
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1045-L1052
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairCore.sol#L1143-L1149

File: contracts/FraxlendPair.sol
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPair.sol#L228


## N-02 Large multiple of 10 should use scientific notation 

(eg 1e6) rather than decimat literals (eg 10000000) for readability

There is 1 instance in 1 file:
File: contracts/FraxlendPairDeployer.sol
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L51


## N-03 NatSpec is incomplete

Add:
    /// @notice Provides a safe ERC20.transfer version for different ERC-20 implementations.
    /// @param token The address of the ERC-20 token.
    /// @param to Transfer tokens to.
    /// @param value The token value.

before https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L60

and
    /// @notice Provides a safe ERC20.transferFrom version for different ERC-20 implementations.
    /// @param token The address of the ERC-20 token.
    /// @param from Transfer tokens from.
    /// @param to Transfer tokens to.
    /// @param value The token value.

before https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/libraries/SafeERC20.sol#L68

as seen in https://github.com/boringcrypto/BoringSolidity/blob/fed25c5d43cb7ce20764cd0b838e21a02ea162e9/contracts/libraries/BoringERC20.sol





## L-01 abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions 
(e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). 
“Unless there is a compelling reason, abi.encode should be preferred”. 
If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.
https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739

There is 3 instances in 1 file:
File: contracts/FraxlendPairDeployer.sol

    /// @param _configData abi.encode(address _asset, address _collateral, address _oracleMultiply, address _oracleDivide, uint256 _oracleNormalization, address _rateContract, bytes memory _rateInitData)
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L308

            bytes32 salt = keccak256(abi.encodePacked(_saltSeed, _configData));
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L204


        _pairAddress = _deployFirst(
            keccak256(abi.encodePacked("public")),
            _configData,
            abi.encode(CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS, TIME_LOCK_ADDRESS, FRAXLEND_WHITELIST_ADDRESS),
            DEFAULT_MAX_LTV,
            DEFAULT_LIQ_FEE,
            0,
            0,
            false,
            false
        );
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L328-L338


        _pairAddress = _deployFirst(
            keccak256(abi.encodePacked(_name)),
            _configData,
            abi.encode(CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS, TIME_LOCK_ADDRESS, FRAXLEND_WHITELIST_ADDRESS),
            _maxLTV,
            _liquidationFee,
            _maturityDate,
            _penaltyRate,
            _approvedBorrowers.length > 0,
            _approvedLenders.length > 0
        );
https://github.com/code-423n4/2022-08-frax/blob/main/src/contracts/FraxlendPairDeployer.sol#L371-L381


