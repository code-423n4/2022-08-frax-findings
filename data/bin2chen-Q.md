
L-01:
FraxlendPairDeployer.sol deployCustom() 
restrict name!="public", to avoid deploy() the same configData can not be deploy

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L372

```
    function deployCustom(
        string memory _name,
        bytes memory _configData,
        uint256 _maxLTV,
        uint256 _liquidationFee,
        uint256 _maturityDate,
        uint256 _penaltyRate,
        address[] memory _approvedBorrowers,
        address[] memory _approvedLenders
    ) external returns (address _pairAddress) {
    ...
    +++ require((keccak256(bytes(_name)) != keccak256(bytes("public"))),"invalid name");

        _pairAddress = _deployFirst(
            keccak256(abi.encodePacked(_name)),
            _configData,
            abi.encode(
                CIRCUIT_BREAKER_ADDRESS,
                COMPTROLLER_ADDRESS,
                TIME_LOCK_ADDRESS,
                FRAXLEND_WHITELIST_ADDRESS
            ),
            _maxLTV,
            _liquidationFee,
            _maturityDate,
            _penaltyRate,
            _approvedBorrowers.length > 0,
            _approvedLenders.length > 0
        );    
```


L-02:FraxlendWhitelist/FraxlendPair
inheritance interfaces， avoid method name errors

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L30

```
--- contract FraxlendWhitelist is Ownable {
+++ contract FraxlendWhitelist is Ownable , IFraxlendWhitelist {
..
```

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L41

```
--- contract FraxlendPair is FraxlendPairCore {
+++ contract FraxlendPair is IFraxlendPair, FraxlendPairCore {
...
```

L-03:FraxlendPairCore.sol
add validity determination of maturityDate/_penaltyRate
maturityDate, _penaltyRate can not be modified after setting, many access operations will use this value, it is recommended to add validity 

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L235
```
    constructor(
        bytes memory _configData,
        bytes memory _immutables,
        uint256 _maxLTV,
        uint256 _liquidationFee,
        uint256 _maturityDate,
        uint256 _penaltyRate,
        bool _isBorrowerWhitelistActive,
        bool _isLenderWhitelistActive
    ) {
    ...

            // Set maturity date & penalty interest rate
    +++ require((_maturityDate == 0 || _maturityDate >= block.timestamp),"invalid maturityDate");
    +++ require(_penaltyRate < 1e18, "invalid penaltyRate");
        maturityDate = _maturityDate;
        penaltyRate = _penaltyRate;
```
