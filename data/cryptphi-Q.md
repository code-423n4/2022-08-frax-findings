1. FraxlendPairCore should inherit IFraxlendPair


2. Missing zero address check
FraxlendPairDeployer.constructor() has no zero address check for the following params - _circuitBreaker, _comptroller, _timelock , _fraxlendWhitelist


