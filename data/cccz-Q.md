## [Low-01] Must approve 0 first
#### Impact
Some tokens (like USDT) do not work when changing the allowance from an existing non-zero allowance value. They must first be approved by zero and then the actual allowance must be approved.
#### Proof of Concept
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1103-L1104
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1184-L1185

#### Tools Used
None
#### Recommended Mitigation Steps
Set the allowance to zero immediately before each of the existing approve() calls.


## [Low-02] Chainlink's latestRoundData might return stale or incorrect results
#### Impact
On FraxlendPairCore.sol, we are using latestRoundData, but there is no check if the return value indicates stale data.
```
        if (oracleMultiply != address(0)) {
            (, int256 _answer, , , ) = AggregatorV3Interface(oracleMultiply).latestRoundData();
            if (_answer <= 0) {
                revert OracleLTEZero(oracleMultiply);
            }
            _price = _price * uint256(_answer);
        }

        if (oracleDivide != address(0)) {
            (, int256 _answer, , , ) = AggregatorV3Interface(oracleDivide).latestRoundData();
            if (_answer <= 0) {
                revert OracleLTEZero(oracleDivide);
            }
            _price = _price / uint256(_answer);
        }
```
This could lead to stale prices according to the Chainlink documentation:

https://docs.chain.link/docs/historical-price-data/#historical-rounds
https://docs.chain.link/docs/faq/#how-can-i-check-if-the-answer-to-a-round-is-being-carried-over-from-a-previous-round
#### Proof of Concept
https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L523-L537
#### Tools Used
None

#### Recommended Mitigation Steps

```
        if (oracleMultiply != address(0)) {
            (uint80 roundID, int256 _answer, , uint256 timestamp, uint80 answeredInRound) = AggregatorV3Interface(oracleMultiply).latestRoundData();
            require(answeredInRound >= roundID, "Stale price");
		    require(timestamp != 0,"Round not complete");
            if (_answer <= 0) {
                revert OracleLTEZero(oracleMultiply);
            }
            _price = _price * uint256(_answer);
        }

        if (oracleDivide != address(0)) {
            (uint80 roundID, int256 _answer, , uint256 timestamp, uint80 answeredInRound) = AggregatorV3Interface(oracleDivide).latestRoundData();
            require(answeredInRound >= roundID, "Stale price");
		    require(timestamp != 0,"Round not complete");
            if (_answer <= 0) {
                revert OracleLTEZero(oracleDivide);
            }
            _price = _price / uint256(_answer);
        }
```