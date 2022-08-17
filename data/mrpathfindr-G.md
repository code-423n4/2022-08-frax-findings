G002 - Cache Array Length Outside of Loop
------

Instances Include: 

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L289

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L308

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L265

Mitigation: 


Do this 

```
var borrowersLength =  _borrowers.length

 for (uint256 i = 0; i < borrowersLength; i++) {
```
Instead of this
```
 for (uint256 i = 0; i < _borrowers.length; i++) {
```



G003 - Use != 0 instead of > 0 for Unsigned Integer Comparison
---


Instances:  

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L477

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L754

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairCore.sol#L835

Mitigation: 

Do this:

``` 
    if (userBorrowShares[msg.sender] > 0) {
```

Instead of this 

```
    if (userBorrowShares[msg.sender] != 0) {
```


G009 - Make Function external instead of public
---


Instances:   

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairHelper.sol#L159

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPairHelper.sol#L226

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/LinearInterestRate.sol#L52


Mitigation: 

Do this: 


```
function previewRateInterest(
        address _fraxlendPairAddress,
        uint256 _timestamp,
        uint256 _blockNumber
    ) external view returns (uint256 _interestEarned, uint256 _newRate) {
```

Instead of this 

```
function previewRateInterest(
        address _fraxlendPairAddress,
        uint256 _timestamp,
        uint256 _blockNumber
    ) public view returns (uint256 _interestEarned, uint256 _newRate) {
```


G012 - Use Prefix Increment instead of Postfix Increment if possible|
---


Instances: 

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L289

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendPair.sol#L308

https://github.com/FraxFinance/fraxlend/blob/0f9bc5ddd6872fba04f4d8fb67c92a88416d19b2/src/contracts/FraxlendWhitelist.sol#L51


Mitigation: 

Do this: 

```
   for (uint256 i = 0; i < _lenders.length; ++i) {
```

Instead of this:

```
   for (uint256 i = 0; i < _lenders.length; i++) {
```

