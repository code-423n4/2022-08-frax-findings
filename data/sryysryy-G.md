## [G-01] `<ARRAY>.LENGTH` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A `FOR`-LOOP

_There are 8 instances of this issue:_

```solidity
File: contracts/FraxlendPair.sol
289:         for (uint256 i = 0; i < _lenders.length; i++) {
308:         for (uint256 i = 0; i < _borrowers.length; i++) {
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L289
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L308


```solidity
File: contracts/FraxlendPairCore.sol:
265:     for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
270:     for (uint256 i = 0; i < _approvedLenders.length; ++i) {
402:     for (uint256 i = 0; i < _lengthOfArray; ) {
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPairCore.sol#L265
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPairCore.sol#L270
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPairCore.sol#L402


```solidity
File: contracts/FraxlendWhitelist.sol
51:     for (uint256 i = 0; i < _addresses.length; i++) {
66:     for (uint256 i = 0; i < _addresses.length; i++) {
81:     for (uint256 i = 0; i < _addresses.length; i++) {
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendWhitelist.sol#L51
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendWhitelist.sol#L66
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendWhitelist.sol#L81

&nbsp;
&nbsp;

## [G-02] USING `> 0` COSTS MORE GAS THAN `!= 0` WHEN USED ON A `UINT` IN A `REQUIRE()` STATEMENT

This change saves [6 gas](https://aws1.discourse-cdn.com/business6/uploads/zeppelin/original/2X/3/363a367d6d68851f27d2679d10706cd16d788b96.png) per instance

_There is 1 instance of this issue:_

```solidity
File: contracts/LinearInterestRate.sol
65:        require(
66:            _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
67:            "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
68:        );
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/LinearInterestRate.sol#L65-L68
&nbsp;
&nbsp;


## [G-03] IT COSTS MORE GAS TO INITIALIZE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED

_There are 8 instances of this issue:_

```solidity
File: contracts/FraxlendPair.sol
289:          for (uint256 i = 0; i < _lenders.length; i++) {
308:          for (uint256 i = 0; i < _borrowers.length; i++) {
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L289
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L308


```solidity
File: contracts/FraxlendPairCore.sol
265:      for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
270:      for (uint256 i = 0; i < _approvedLenders.length; ++i) {
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPairCore.sol#L265
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPairCore.sol#L270

```solidity
File: contracts/FraxlendPairDeployer.sol
402:  for (uint256 i = 0; i < _lengthOfArray; ) {
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPairDeployer.sol#L402


```solidity
File: contracts/FraxlendWhitelist.sol
51:      for (uint256 i = 0; i < _addresses.length; i++) {
66:      for (uint256 i = 0; i < _addresses.length; i++) {
81:      for (uint256 i = 0; i < _addresses.length; i++) {
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendWhitelist.sol#L51
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendWhitelist.sol#L66
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendWhitelist.sol#L81

&nbsp;
&nbsp;

## [G-04] `++I` COSTS LESS GAS THAN `I++`, ESPECIALLY WHEN IT’S USED IN `FOR`-LOOPS (`--I`/`I--` TOO)

Saves 6 gas _PER LOOP_

_There are 7 instances of this issue:_


```solidity
File: contracts/FraxlendPair.sol
289:        for (uint256 i = 0; i < _lenders.length; i++) {
308:        for (uint256 i = 0; i < _borrowers.length; i++) {
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L289
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L308

```solidity
File: contracts/FraxlendPairDeployer.sol
127:        for (i = 0; i < _lengthOfArray; ) {
128:            _addresses[i] = deployedPairsByName[_deployedPairsArray[i]];
129:            unchecked {
130:                i++;
131:            }
132:        }


152:        for (i = 0; i < _lengthOfArray; ) {
153:            _pairCustomStatuses[i] = PairCustomStatus({
154:                _address: _addresses[i],
155:                _isCustom: deployedPairCustomStatusByAddress[_addresses[i]]
156:            });
157:            unchecked {
158:                i++;
159:            }
160:        }
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPairDeployer.sol#L127-L132
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPairDeployer.sol#L152-L160

```solidity
File: contracts/FraxlendWhitelist.sol
51:        for (uint256 i = 0; i < _addresses.length; i++) {
66:        for (uint256 i = 0; i < _addresses.length; i++) {
81:        for (uint256 i = 0; i < _addresses.length; i++) {
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendWhitelist.sol#L51
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendWhitelist.sol#L66
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendWhitelist.sol#L81

&nbsp;
&nbsp;



## [G-05] FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED `PAYABLE`

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are  
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

_There are 5 instances of this issue:_

```solidity
File: contracts/FraxlendPair.sol
274:    function setSwapper(address _swapper, bool _approval) external onlyOwner {
275:        swappers[_swapper] = _approval;
276:        emit SetSwapper(_swapper, _approval);
277:    }
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L274-L277


```solidity
File: contracts/FraxlendPairDeployer.sol
170:    function setCreationCode(bytes calldata _creationCode) external onlyOwner {
171:        bytes memory _firstHalf = BytesLib.slice(_creationCode, 0, 13000);
172:        contractAddress1 = SSTORE2.write(_firstHalf);
173:        if (_creationCode.length > 13000) {
174:            bytes memory _secondHalf = BytesLib.slice(_creationCode, 13000, _creationCode.length - 13000);
175:            contractAddress2 = SSTORE2.write(_secondHalf);
176:        }
177:    }
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPairDeployer.sol#L170-L177


```solidity
File: contracts/FraxlendWhitelist.sol
50:    function setOracleContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
51:        for (uint256 i = 0; i < _addresses.length; i++) {
52:            oracleContractWhitelist[_addresses[i]] = _bool;
53:            emit SetOracleWhitelist(_addresses[i], _bool);
54:        }
55:    }

65:    function setRateContractWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
66:        for (uint256 i = 0; i < _addresses.length; i++) {
67:            rateContractWhitelist[_addresses[i]] = _bool;
68:            emit SetRateContractWhitelist(_addresses[i], _bool);
69:        }
70:    }

80:    function setFraxlendDeployerWhitelist(address[] calldata _addresses, bool _bool) external onlyOwner {
81:        for (uint256 i = 0; i < _addresses.length; i++) {
82:            fraxlendDeployerWhitelist[_addresses[i]] = _bool;
83:            emit SetFraxlendDeployerWhitelist(_addresses[i], _bool);
84:        }
85:    }
86:}
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendWhitelist.sol#L50-L55
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendWhitelist.sol#L65-L70
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendWhitelist.sol#L80-L86

&nbsp;
&nbsp;

## [G-06] `<X> += <Y>` COSTS MORE GAS THAN `<X> = <X> + <Y>` FOR STATE VARIABLES

_There are 10 instances of this issue:_

```solidity
File: contracts/FraxlendPairCore.sol
475:                _totalBorrow.amount += uint128(_interestEarned);
476:                _totalAsset.amount += uint128(_interestEarned);
484:                    _totalAsset.shares += uint128(_feesShare);
566:        _totalAsset.amount += _amount;
567:        _totalAsset.shares += _shares;
718:        _totalBorrow.amount += _borrowAmount;
719:        _totalBorrow.shares += uint128(_sharesAdded);
724:        userBorrowShares[msg.sender] += _sharesAdded;
772:        userCollateralBalance[_borrower] += _collateralAmount;
773:        totalCollateral += _collateralAmount;
```
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L475
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L476
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L484
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L566
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L567
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L718
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L719
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L724
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L772
https://github.com/FraxFinance/fraxlend/blob/main/src/contracts/FraxlendPair.sol#L773

