# Table of contents

- **[[0x0] Disclaimer](#0x0)** ✅ 
- **[[G-01] Try ++i instead of i++](G-01)**✅
- **[[G-02] Try `unchecked{++i}` instead of `i++` in loops](G-02)** ✅
- **[[G-03] Consider `a = a + b` instead of `a += b`](G-03)** ✅
- **[[G-04] Consider marking onlyOwner functions as payable](G-04)** ✅
- **[[G-07] `Internal` functions can be inlined](G-07)** ✅
- **[[G-08] Use `private/internal` for `constants/immutable` variables instead of `public`](G-08)** ✅
- **[[G-12] Use `uint` instead of `uint8`, `uint64`, if it's possible](G-12)** ✅
- **[[G-14] Use `calldataload` instead of `mload`](G-14)** ✅
- **[[G-15] Define state variables as `immutable/const`](G-15)** ✅
- **[[G-16] Consider custom errors instead of strings](G-16)** ✅
- **[[G-18] Try increment/decrement instead of +1/-1](G-18)** ✅


## Disclaimer<a name="0x0"></a>

- Please, consider everything described below as a general recommendation. I'll not include every single occurance, but instead will point out the files to look for other findings of the same type.
-  These notes will represent potential possibilities to optimize gas consumption. It's okay, if something is not suitable in your case. Just let me know the reason in the comments section. Enjoy!

## **[G-01] Try ++i instead of i++**<a name="G-01"></a>

### ***Description:***

  - In case of `i++`, the compiler has to to create a temp variable to return `i` (if there's a need) and then `i` gets incremented.  
  - In case of `++i`, the compiler just simply returns already incremented value.

### ***All occurances:***

  - Contracts:
  
    ```Solidity
      file: src/contracts/FraxlendPair.sol 
      ....................................

         -----------
        // Origin //
        -----------
          // Lines: [289-289]
            for (uint256 i = 0; i < _lenders.length; i++) {}

          // Lines: [308-308]
            for (uint256 i = 0; i < _borrowers.length; i++) {}

         -----------
        // Updated //
        -----------
          // Lines: [289-289]
            for (uint256 i = 0; i < _lenders.length; ++i) {}

          // Lines: [308-308]
            for (uint256 i = 0; i < _borrowers.length; ++i) {}


- Also occured in the following files: 
  - src/contracts/FraxlendPairDeployer.sol
  - src/contracts/FraxlendWhitelist.sol
  - src/contracts/libraries/SafeERC20.sol


## **[G-02] Try `unchecked{++i};` instead of `i++;`/`++i;` in loops**<a name="G-02"></a>

### ***Description:***

  - Since [Solidity 0.8.0](https://github.com/ethereum/solidity/releases/tag/v0.8.0), all arithmetic operations revert on over- and underflow by default Therefore, `unchecked` box can be used to prevent all unnecessary checks, if it's no a way to get a reversion.
  
  - There are ~1e80 atoms in the universe, so 2^256 is closed to that number, therefore it's no a way to be overflowed, because of the gas limit as well. 

### ***All occurances:***

  - Contracts:
  
    ```Solidity
      file: src/contracts/FraxlendPair.sol 
      ....................................

         -----------
        // Origin //
        -----------
          // Lines: [289-289]
            for (uint256 i = 0; i < _lenders.length; i++) {}

          // Lines: [308-308]
            for (uint256 i = 0; i < _borrowers.length; i++) {}

         -----------
        // Updated //
        -----------
          // Lines: [289-289]
            for (uint256 i = 0; i < _lenders.length;) {
              // some logic
              unchecked {
                ++i;
              }
            }

          // Lines: [308-308]
            for (uint256 i = 0; i < _borrowers.length; ++i) {
              // some logic
              unchecked {
                ++i;
              }

            }


- Also occured in the following files: 
  - src/contracts/FraxlendWhitelist.sol
  - src/contracts/libraries/SafeERC20.sol

## **[G-03] Consider `a = a + b` instead of `a += b`**<a name="G-03"></a>

### ***Description:***

- It has an impact on the deployment cost and the cost for distinct transaction as well.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/contracts/FraxlendPairCore.sol 
      .........................................

         -----------
        // Origin //
        -----------
      
          // Lines: [475-476]
            _totalBorrow.amount += uint128(_interestEarned);
            _totalAsset.amount += uint128(_interestEarned);

          // Lines: [645-646]
            _totalAsset.amount -= _amountToReturn;
            _totalAsset.shares -= _shares;


         -----------
        // Updated //
        -----------

          // Lines: [475-476]
            _totalBorrow.amount = _totalBorrow.amount + uint128(_interestEarned);
            _totalAsset.amount = _totalAsset.amount + uint128(_interestEarned);

          // Lines: [645-646]
            _totalAsset.amount = _totalAsset.amount - _amountToReturn;
            _totalAsset.shares =_totalAsset.shares - _shares;


    ```

- Also occured in the following files:
  - src/contracts/FraxlendPair.sol

## **[G-04] Consider marking onlyOwner functions as payable**<a name="G-04"></a>

### ***Description:***

- This one is a bit questionable, but you can try that out. So, the compiler adds some extra conditions in case of non-payable, but we know that `onlyOwner` modifier will be reverted, if the user who is not an owner invokes following methods.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/contracts/FraxlendPair.sol 
      .........................................

         -----------
        // Origin //
        -----------
      
          // Lines: [475-476]
            function setTimeLock(address _newAddress) external onlyOwner {
              emit SetTimeLock(TIME_LOCK_ADDRESS, _newAddress);
              TIME_LOCK_ADDRESS = _newAddress;
            }

         -----------
        // Updated //
        -----------
          // Lines: [475-476]
            function setTimeLock(address _newAddress) external payable onlyOwner {
              emit SetTimeLock(TIME_LOCK_ADDRESS, _newAddress);
              TIME_LOCK_ADDRESS = _newAddress;
            }

        ```
- Also occured in the following files: 
  - src/contracts/FraxlendPairDeployer.sol
  - src/contracts/FraxlendWhitelist.sol

      


## **[G-07] `Internal` functions can be inlined**<a name="G-07"></a>

### ***Description:***

- It takes some extra `JUMP`s to find a function and also the gas consumption for pushing arguments into memory and etc.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/contracts/FraxlendPairCore.sol 
      ....................................
      
        // Lines: [295-301]
          function _totalAssetAvailable(VaultAccount memory _totalAsset, VaultAccount memory _totalBorrow)
              internal
              pure
              returns (uint256)
          {
              return _totalAsset.amount - _totalBorrow.amount;
          }
    ```



## **[G-08] Use `private/internal` for `constants/immutable/state` variables instead of `public`**<a name="G-08"></a>

### ***Description:***

- If there is no need in state/constants/immutable variables outside the contract, it's better to mark them as internal/private. Optimization comes from not creating a getter function for each `public` instance.
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/contracts/FraxlendWhitelist.sol 
      .........................................

         -----------
        // Origin //
        -----------
      
          // Lines: [32-38]
            mapping(address => bool) public oracleContractWhitelist;

            // Interest Rate Calculator Whitelist Storage
            mapping(address => bool) public rateContractWhitelist;

            // Fraxlend Deployer Whitelist Storage
            mapping(address => bool) public fraxlendDeployerWhitelist;

         -----------
        // Updated //
        -----------

          // Lines: [32-38]
            mapping(address => bool) internal oracleContractWhitelist;

            // Interest Rate Calculator Whitelist Storage
            mapping(address => bool) internal rateContractWhitelist;

            // Fraxlend Deployer Whitelist Storage
            mapping(address => bool) internal fraxlendDeployerWhitelist;

    ```
- Also occured in the following files: 
  - src/contracts/FraxlendPairCore.sol
  - src/contracts/FraxlendPairDeployer.sol



## **[G-12] Use `uint` instead of `uint8`, `uint64`, if it's possible**<a name="G-12"></a>

### ***Description:***

- I deployed a contract with only a single error and function and compared between:
  - `error VaultAlreadyUnderAuction(bytes12 vaultId, address witch);`.
  - `error VaultAlreadyUnderAuction(bytes32 vaultId, address witch);`.
- The second one is saving about ~10k units of gas for deployment and 10k per each transaction.
- However, defaining further errors with !`bytes32` doesn't give a huge optimization.  
- The stack slots are 32bytes, just use 32bytes, if you are not trying to tight up the storage.

- In this case, it's totally fine and it's great that you're tighting up the structs:


  - ```Solidity 
      struct CurrentRateInfo {
        uint64 lastBlock;
        uint64 feeToProtocolRate; // Fee amount 1e5 precision
        uint64 lastTimestamp;
        uint64 ratePerSec;
      }
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/contracts/FraxlendPairConstants.sol 
      .............................................
      
            // Lines: [41-41]
              uint64 internal constant DEFAULT_INT = 158247046; // 0.5% annual rate 1e18 precision
              
            // Lines: [47-47]
                uint16 internal constant DEFAULT_PROTOCOL_FEE = 0; // 1e5 precision
          
          // Update to:

            // Lines: [41-41]
              uint internal constant DEFAULT_INT = 158247046; // 0.5% annual rate 1e18 precision
            // Lines: [47-47]
                uint internal constant DEFAULT_PROTOCOL_FEE = 0; // 1e5 precision

      ```

- Also occured in the following files: 
  - src/contracts/FraxlendPair.sol
  - src/contracts/FraxlendPairCore.sol
  - src/contracts/VariableInterestRate.sol

## **[G-14] Use `calldataload` instead of `mload`**<a name="G-14"></a>

### ***Description:***

- After Berlin hard fork, to load a non-zero byte from calldata dropped from 64 units of gas to 16 units, therefore if you do not modify args, use a calldata instead of memory. Here you need to explicitly specify `calldataload`, or replace `memory` with `calldata`. If the args are pretty huge, allocating args in memory will cost a lot. 
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/contracts/FraxlendPair.sol 
      .....................................

         -----------
        // Origin //
        -----------

          // Lines: [45-68]
            constructor(
                bytes memory _configData,
                bytes memory _immutables,
                uint256 _maxLTV,
                uint256 _liquidationFee,
                uint256 _maturityDate,
                uint256 _penaltyRate,
                bool _isBorrowerWhitelistActive,
                bool _isLenderWhitelistActive
            )

         -----------
        // Updated //
        -----------

          // Lines: [45-68]
            constructor(
                bytes calldata _configData,
                bytes calldata _immutables,
                uint256 _maxLTV,
                uint256 _liquidationFee,
                uint256 _maturityDate,
                uint256 _penaltyRate,
                bool _isBorrowerWhitelistActive,
                bool _isLenderWhitelistActive
            )

- Also occured in the following files: 
  - src/contracts/FraxlendPairCore.sol
  - src/contracts/FraxlendPairDeployer.sol

## **[G-15] Define state variables as `immutable/const`**<a name="G-15"></a>

### ***Description:***

- The opcode `SSTORE_SET_GAS` after Berlin hard fork costs around 22100 units of gas, therefore to get a huge optimization it's possible to define the following state variables as `immutable/const`. 
- Also, in the future, there is no need in cold/warm(2200/100 gas for accessing) `SLOAD`s, since they are consts/immutable variables, which also saves sagnificant amount of gas.
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/contracts/FraxlendPairCore.sol 
      ........................................

         -----------
        // Origin //
        -----------
      
          // Lines: [51-51]
            string public version = "1.0.0";

          // Lines: [75-75]
            bytes public rateInitCallData; // Optional extra data from init function to be passed to rate calculator

          // Lines: [92-92]
            string internal nameOfContract;

         -----------
        // Updated //
        -----------

          // Lines: [51-51]
            string public immutable/const?? version = "1.0.0";

          // Lines: [75-75]
            bytes public immutable rateInitCallData; // Optional extra data from init function to be passed to rate calculator

          // Lines: [92-92]
            string internal immutable nameOfContract;

      ```


## **[G-16] Consider custom errors instead of strings**<a name="G-16"></a>

### ***Description:***

- After the solidity 0.8.4, the custom errors are available. Consider adding them to avoid long reversion messages in order to optimize the gas consumtion. 
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/contracts/LinearInterestRate.sol 
      ..........................................

         -----------
        // Origin //
        -----------
      
          // Lines: [57-68]
              require(
                  _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
                  "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
              );
              require(
                  _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
                  "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
              );
              require(
                  _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
                  "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
              );

         -----------
        // Updated //
        -----------
          // declaring errors

          // Lines: [57-68]
              if (_minInterest >= MAX_INT || _minInterest > _vertexInterest || _minInterest < MIN_INT) {revert C9(args)}
              // etc.. 

          // And then in a comments you can write something like:
            C9 is "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
            C10 is "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT" 
            // etc...


      ```

- Also occured in the following files: 
  - src/contracts/FraxlendPairDeployer.sol
  - src/contracts/LinearInterestRate.sol

## **[G-18] Try increment/decrement instead of +1/-1**<a name="G-18"></a>

### ***Description:***

- It's sagnificant gas optimization, if you look for every single occurance. Be careful with post/pre increment/decrements.
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/contracts/FraxlendPairDeployer.sol
      ............................................

        // Lines: [407-409]
          // Original 
              unchecked {
                  i = i + 1;
              }

          // Updated 
              unchecked {
                  ++i;
              }
    ```


## Kudos for the quality of the code! It's pretty easy to explore
