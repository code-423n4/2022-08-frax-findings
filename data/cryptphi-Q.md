1. FraxlendPairCore should inherit IFraxlendPair
FraxlendPairCore contract does not import and inherit functions that it makes use of which are defined in IFraxlendPair contract


2. Missing zero address check
FraxlendPairDeployer.constructor() has no zero address check for the following params - _circuitBreaker, _comptroller, _timelock , _fraxlendWhitelist


