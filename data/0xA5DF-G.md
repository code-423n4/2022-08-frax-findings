## Replace constant expression with its value
Code: https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L329

Gas saved: ~140 units
Replace  `keccak256(abi.encodePacked("public"))` with its value:


diff:

```diff
diff --git a/src/contracts/FraxlendPairDeployer.sol b/src/contracts/FraxlendPairDeployer.sol
index f8d959e..1e634ce 100644
--- a/src/contracts/FraxlendPairDeployer.sol
+++ b/src/contracts/FraxlendPairDeployer.sol
@@ -326,7 +326,7 @@ contract FraxlendPairDeployer is Ownable {
         );
 
         _pairAddress = _deployFirst(
-            keccak256(abi.encodePacked("public")),
+            0x3c0c3a2537aaf75b853bbf2b595e872d3db0359b7e792ebd8907fb017c3b71a2,
             _configData,
             abi.encode(CIRCUIT_BREAKER_ADDRESS, COMPTROLLER_ADDRESS, TIME_LOCK_ADDRESS, FRAXLEND_WHITELIST_ADDRESS),
             DEFAULT_MAX_LTV,
```

gas report diff:
```diff
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                                        ┆ min             ┆ avg     ┆ median  ┆ max     ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ deploy                                                               ┆ 12860           ┆ 5372981 ┆ 5642803 ┆ 5710008 ┆ 20      │
+│ deploy                                                               ┆ 12728           ┆ 5372838 ┆ 5642660 ┆ 5709865 ┆ 20      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ deployCustom                                                         ┆ 5424            ┆ 3597248 ┆ 5560292 ┆ 5818462 ┆ 8       │

```





## Make storage vars immutable
https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L56-L59

#### FraxLendPairDeployer
`FraxLendPairDeployer` doesn't change the following addresses, these can be changed to immutable:
FraxLendPairDeployer.sol:
```solidity
    // Admin contracts
    address public CIRCUIT_BREAKER_ADDRESS;
    address public COMPTROLLER_ADDRESS;
    address public TIME_LOCK_ADDRESS;
```

#### FraxlendPairCore
The `rateInitCallData` takes 5 storage slots (4 uint256 + one slot for bytes size), this will cost **10.5K** every time it's loaded (= almost every time `_addInterest()` is called = almost every tx).
`bytes` variable can't be immutable since it has a dynamic size, but it can be decoded into 4 immutable uint256 vars and then encoded back to bytes at runtime. This would be MUCH cheaper since encoding shouldn't cost more than a few hundred gas units.

Note: This gas optimization wouldn't show on forge tests, the reason is that forge considers each test as one tx (even when you change block number & timestamp). Therefore, `rateInitCallData` storage variable is still considered to be 'warm' (i.e. used in the last tx) which reduces the cost per `sload` from 2.1K to just 0.1K (see [evm.codes](https://www.evm.codes/#54)).
However, if you'd test it with Hardhat I'm certain you'd see the difference.

Optimization diff:
```diff
diff --git a/src/contracts/FraxlendPairCore.sol b/src/contracts/FraxlendPairCore.sol
index a712d46..c7be423 100644
--- a/src/contracts/FraxlendPairCore.sol
+++ b/src/contracts/FraxlendPairCore.sol
@@ -63,6 +63,11 @@ abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ow
     address public immutable oracleDivide;
     uint256 public immutable oracleNormalization;
 
+    uint256 immutable _minInterest;
+    uint256 immutable _vertexInterest;
+    uint256 immutable _maxInterest;
+    uint256 immutable _vertexUtilization;
+
     // LTV Settings
     uint256 public immutable maxLTV;
 
@@ -72,7 +77,7 @@ abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ow
 
     // Interest Rate Calculator Contract
     IRateCalculator public immutable rateContract; // For complex rate calculations
-    bytes public rateInitCallData; // Optional extra data from init function to be passed to rate calculator
+    // bytes public rateInitCallData; // Optional extra data from init function to be passed to rate calculator
 
     // Swapper
     mapping(address => bool) public swappers; // approved swapper addresses
@@ -183,9 +188,26 @@ abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ow
                 address _oracleDivide,
                 uint256 _oracleNormalization,
                 address _rateContract,
-
+                bytes memory _rateInitData
             ) = abi.decode(_configData, (address, address, address, address, uint256, address, bytes));
 
+            {
+                uint256 temp1;
+                uint256 temp2;
+                uint256 temp3;
+                uint256 temp4;
+                if(_rateInitData.length > 0)
+                {
+                    // (_minInterest,  _vertexInterest, _maxInterest, _vertexUtilization) = (5,6,7,8);
+                    (temp1, temp2, temp3, temp4) = abi.decode(
+                        _rateInitData,
+                        (uint256, uint256, uint256, uint256)
+                    );
+                }
+                (_minInterest,  _vertexInterest, _maxInterest, _vertexUtilization) = (temp1, temp2, temp3, temp4);
+            }
+
+
             // Pair Settings
             assetContract = IERC20(_asset);
             collateralContract = IERC20(_collateral);
@@ -275,7 +297,6 @@ abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ow
         IRateCalculator(rateContract).requireValidInitData(_rateInitCallData);
 
         // Set rate init Data
-        rateInitCallData = _rateInitCallData;
 
         // Instantiate Interest
         _addInterest();
@@ -300,6 +321,14 @@ abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ow
         return _totalAsset.amount - _totalBorrow.amount;
     }
 
+    // `testPreviewInterestRate` test breaks without this
+    // You can remove this once you get to the root of the issue
+    function rateInitCallData() public view returns(bytes memory){
+        return abi.encode(
+                    _minInterest, _vertexInterest,  _maxInterest, _vertexUtilization
+                ); 
+    }
+
     /// @notice The ```_isSolvent``` function determines if a given borrower is solvent given an exchange rate
     /// @param _borrower The borrower address to check
     /// @param _exchangeRate The exchange rate, i.e. the amount of collateral to buy 1e18 asset
@@ -447,6 +476,10 @@ abstract contract FraxlendPairCore is FraxlendPairConstants, IERC4626, ERC20, Ow
             if (_isPastMaturity()) {
                 _newRate = uint64(penaltyRate);
             } else {
+
+                bytes memory rateInitCallData = abi.encode(
+                    _minInterest, _vertexInterest,  _maxInterest, _vertexUtilization
+                );             
                 bytes memory _rateData = abi.encode(
                     _currentRateInfo.ratePerSec,
                     _deltaTime,

```

## Custom errors instead of require-string

Even though custom errors are widely used, there are still some places where require-string is used (e.g. [FraxlendPairDeployer.sol#L208](https://github.com/code-423n4/2022-08-frax/blob/92a8d7d331cc718cd64de6b02515b554672fb0f3/src/contracts/FraxlendPairDeployer.sol#L208)).
Replacing that with a custom error can save deployment gas / code size and gas in case of reverts on those requirements.