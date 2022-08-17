##### Summary

Gas savings are estimated using gas report of existing tests `source .env && forge test --fork-url $MAINNET_URL --fork-block-number $DEFAULT_FORK_BLOCK --gas-report` (the sum of all deployment costs and the sum of the cost of calling all methods) and may vary depending on the implementation of the fix. I keep my version of the fix for each finding and can provide them if you need. The accuracy of the estimate depends on the test coverage. I'm not "foundry-friendly", and unfortunately I didn't have enough time to learn how to write additional tests.

Gas Optimizations
||Issue| Instances|Estimated gas(deployments)|Estimated gas(method call)|
|:---:|:---|:---:|:---:|:---:|
|**1**|Error handling||optional||
|**1.1**|Use Custom Errors instead of Revert/Require Strings to save Gas|9|155 360|-|
|**1.2**|Require()/revert() strings longer than 32 bytes cost extra gas|8|70 073|-|
|**1.3**|Splitting require() statements that use && saves gas|3|-|-|
|**2**|Variable can be a constant|4|108 477|≈66 179|
|**3** |Increment optimization.||optional||
|**3.1**|Prefix increments are cheaper than postfix increments, especially when it's used in for-loops|9|3 613|≈6 305|
|**3.2**|\<x\> = \<x\> + 1 even more efficient than pre increment|11|6 607|≈14 176|
|**3.3**|Expression can be unchecked when overflow is not possible|11|45 459|≈59 563|
|**4**|State variables only set in the constructor should be declared immutable|3|8 352|≈1 841|
|**5**|x = x + y is more efficient than x += y|22|8 009|≈22 006|
|**6**|Using bools for storage incurs overhead|9|6 855|≈26 630|
|**7**|It costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied|11|807|≈1 101|

**Total: 100 instances over 11 issues**

---

1. **Error handling**

   1. **Use Custom Errors instead of Revert/Require Strings to save Gas (9 instances)**

      Deployment Gas Saved: **155360**

      Solidity 0.8.4 introduced custom errors. They are more gas efficient than revert strings, when it comes to deploy cost as well as runtime cost when the revert condition is met. Use custom errors instead of revert strings for gas savings.

      Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string.
      Additional gas is saved due to the lack of defining string. [https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth)

      ***

      - src/contracts/LinearInterestRate.sol:[57-68](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L68)

      ```solidity
      57      require(
      58          _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
      59          "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
      60      );
      61      require(
      62          _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
      63          "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
      64      );
      65      require(
      66          _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
      67          "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
      68      );
      ```

      - src/contracts/FraxlendPairDeployer.sol:[205](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L205),[228](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L228),[253](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L253),[365-369](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L365-L369),[399](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L399),

      ```solidity
      205     require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
      ...
      228     require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
      ...
      253     require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
      ...
      365     require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
      366     require(
      367         IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
      368         "FraxlendPairDeployer: Only whitelisted addresses"
      369     );
      ...
      399     require(msg.sender == CIRCUIT_BREAKER_ADDRESS, "Circuit Breaker only");
      ```

   2. **Require()/revert() strings longer than 32 bytes cost extra gas (8 instances)**

      If you opt not to use custom errors keeping require strings <= 32 bytes in length will save gas.

      Deployment Gas Saved: **70073**

      - src/contracts/LinearInterestRate.sol:[57-68](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L68)

      ```solidity
      57      require(
      58          _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
      59          "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
      60      );
      61      require(
      62          _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
      63          "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
      64      );
      65      require(
      66          _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
      67          "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
      68      );
      ```

      - src/contracts/FraxlendPairDeployer.sol:[205](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L205),[228](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L228),[253](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L253),[365-369](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L365-L369),[399](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L399),

      ```solidity
      205     require(deployedPairsBySalt[salt] == address(0), "FraxlendPairDeployer: Pair already deployed");
      ...
      228     require(_pairAddress != address(0), "FraxlendPairDeployer: create2 failed");
      ...
      253     require(deployedPairsByName[_name] == address(0), "FraxlendPairDeployer: Pair name must be unique");
      ...
      365     require(_maxLTV <= GLOBAL_MAX_LTV, "FraxlendPairDeployer: _maxLTV is too large");
      366     require(
      367         IFraxlendWhitelist(FRAXLEND_WHITELIST_ADDRESS).fraxlendDeployerWhitelist(msg.sender),
      368         "FraxlendPairDeployer: Only whitelisted addresses"
      369     );
      ```

   3. **Splitting require() statements that use && saves gas (3 instances)**

      Optional, if you do not want to use model `if () revert CustomError();`
      Costs extra gas to deploy, but saves gas in the long run due to cheaper method calls

      - src/contracts/LinearInterestRate.sol:[57-68](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L68)

      ```solidity
      57      require(
      58          _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
      59          "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
      60      );
      61      require(
      62          _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT,
      63          "LinearInterestRate: _maxInterest <= MAX_INT && _vertexInterest <= _maxInterest && _maxInterest > MIN_INT"
      64      );
      65      require(
      66          _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0,
      67          "LinearInterestRate: _vertexUtilization < MAX_VERTEX_UTIL && _vertexUtilization > 0"
      68      );
      ```

2. **Variable can be a constant (4 instances)**

   - src/contracts/FraxlendPairDeployer.sol:[49-51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L49-L51)

   Deployment Gas Saved: **108477**
   Method Call Gas Saved: **≈66179**

   ```solidity
   48      // Constants
   49      uint256 public DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision
   50      uint256 public GLOBAL_MAX_LTV = 1e8; // 1000x (100,000%) with 1e5 precision, protects from rounding errors in LTV calc
   51      uint256 public DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision
   ```

   fix:

   ```solidity
   49      uint256 private constant DEFAULT_MAX_LTV = 75000; // 75% with 1e5 precision
   50      uint256 private constant GLOBAL_MAX_LTV = 1e8; // 1000x (100,000%) with 1e5 precision, protects from rounding errors in LTV calc
   51      uint256 private constant DEFAULT_LIQ_FEE = 10000; // 10% with 1e5 precision
   ```

   - src/contracts/FraxlendPairCore.sol:[51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L51)

   ```solidity
   51      string public version = "1.0.0";
   ```

   fix:

   ```solidity
   51      string private constant version = "1.0.0";
   ```

3. **Increment optimization.**

   1. Prefix increments are cheaper than postfix increments, especially when it's used in for-loops (9 instances).

      Deployment Gas Saved: **3613**
      Method Call Gas Saved: **≈6305**

      - src/contracts/libraries/SafeERC20.sol:[24](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L24),[27](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L27)

      ```solidity
      24      i++;
      ...
      27      for (i = 0; i < 32 && data[i] != 0; i++) {
      ```

      - src/contracts/FraxlendPairDeployer.sol:[130](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L130),[158](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L158)

      ```solidity
      130      i++;
      ...
      158      i++;
      ```

      - src/contracts/FraxlendWhitelist.sol:[51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51),[66](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66),[81](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81)

      ```solidity
      51      for (uint256 i = 0; i < _addresses.length; i++) {
      ...
      66      for (uint256 i = 0; i < _addresses.length; i++) {
      ...
      81      for (uint256 i = 0; i < _addresses.length; i++) {
      ```

      - src/contracts/FraxlendPair.sol:[289](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289),[308](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308)

      ```solidity
      289     for (uint256 i = 0; i < _lenders.length; i++) {
      ...
      308     for (uint256 i = 0; i < _borrowers.length; i++) {
      ```

   2. \<x\> = \<x\> + 1 even more efficient than pre increment.(11 instances) Gas saved:

      Deployment Gas Saved: **6607**
      Method Call Gas Saved: **≈14176**

      - src/contracts/libraries/SafeERC20.sol:[24](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L24),[27](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L27)

      ```solidity
      24      i++;
      ...
      27      for (i = 0; i < 32 && data[i] != 0; i++) {
      ```

      - src/contracts/FraxlendPairDeployer.sol:[130](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L130),[158](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L158)

      ```solidity
      130      i++;
      ...
      158      i++;
      ```

      - src/contracts/FraxlendWhitelist.sol:[51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51),[66](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66),[81](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81)

      ```solidity
      51      for (uint256 i = 0; i < _addresses.length; i++) {
      ...
      66      for (uint256 i = 0; i < _addresses.length; i++) {
      ...
      81      for (uint256 i = 0; i < _addresses.length; i++) {
      ```

      - src/contracts/FraxlendPair.sol:[289](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289),[308](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308)

      ```solidity
      289     for (uint256 i = 0; i < _lenders.length; i++) {
      ...
      308     for (uint256 i = 0; i < _borrowers.length; i++) {
      ```

      - src/contracts/FraxlendPairCore.sol:[265](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265),[270](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270)

      ```solidity
      265     for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
      ...
      270     for (uint256 i = 0; i < _approvedLenders.length; ++i) {
      ```

   3. Expression can be unchecked when overflow is not possible.(11 instances) Gas saved:

      Deployment Gas Saved: **45459**
      Method Call Gas Saved: **≈59563**

      - src/contracts/libraries/SafeERC20.sol:[24](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L24),[27](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L27)

      ```solidity
      24      i++;
      ...
      27      for (i = 0; i < 32 && data[i] != 0; i++) {
      ```

      - src/contracts/FraxlendWhitelist.sol:[51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51),[66](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66),[81](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81)

      ```solidity
      51      for (uint256 i = 0; i < _addresses.length; i++) {
      ...
      66      for (uint256 i = 0; i < _addresses.length; i++) {
      ...
      81      for (uint256 i = 0; i < _addresses.length; i++) {
      ```

      - src/contracts/FraxlendPair.sol:[289](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289),[308](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308)

      ```solidity
      289     for (uint256 i = 0; i < _lenders.length; i++) {
      ...
      308     for (uint256 i = 0; i < _borrowers.length; i++) {
      ```

      - src/contracts/FraxlendPairCore.sol:[265](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265),[270](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270)

      ```solidity
      265     for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
      ...
      270     for (uint256 i = 0; i < _approvedLenders.length; ++i) {
      ```

      - src/contracts/libraries/VaultAccount.sol:[26](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/VaultAccount.sol#L26),[43](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/VaultAccount.sol#L43)

      ```solidity
      26      shares = shares + 1; // @note Theoretically, it cannot overflow, because the result of division in 24
      ...
      43      amount = amount + 1; // @note Theoretically, it cannot overflow, because the result of division in 41
      ```

4. **State variables only set in the constructor should be declared immutable (3 instances)**

   Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

   Deployment Gas Saved: **8352**
   Method Call Gas Saved: **≈1841**

   - src/contracts/FraxlendPairDeployer.sol:[57-59](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L57-L59)

   ```solidity
   57      address public CIRCUIT_BREAKER_ADDRESS;
   58      address public COMPTROLLER_ADDRESS;
   59      address public TIME_LOCK_ADDRESS;
   ```

5. **x = x + y is more efficient than x += y (22 instances)**

   Deployment Gas Saved: **8009**
   Method Call Gas Saved: **≈22006**

   - src/contracts/FraxlendPairCore.sol:[475-476](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L475-L476),[484](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L484),[566-567](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L566-L567),[643-644](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L643-L644),[718-719](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L718-L719),[724](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L724),[772-773](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L772-L773),[813](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L813),[815](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L815),[863-864](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L863-L864),[867](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L867),[1008](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1008),[1011-1012](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L1011-L1012)

   ```solidity
   475     _totalBorrow.amount += uint128(_interestEarned);
   476     _totalAsset.amount += uint128(_interestEarned);
   ...
   484     _totalAsset.shares += uint128(_feesShare);
   ...
   566     _totalAsset.amount += _amount;
   567     _totalAsset.shares += _shares;
   ...
   643     _totalAsset.amount -= _amountToReturn;
   644     _totalAsset.shares -= _shares;
   ...
   718     _totalBorrow.amount += _borrowAmount;
   719     _totalBorrow.shares += uint128(_sharesAdded);
   ...
   724     userBorrowShares[msg.sender] += _sharesAdded;
   ...
   772     userCollateralBalance[_borrower] += _collateralAmount;
   773     totalCollateral += _collateralAmount;
   ...
   813     userCollateralBalance[_borrower] -= _collateralAmount;
   ...
   815     totalCollateral -= _collateralAmount;
   ...
   863     _totalBorrow.amount -= _amountToRepay;
   864     _totalBorrow.shares -= _shares;
   ...
   867     userBorrowShares[_borrower] -= _shares;
   ...
   1008    totalAsset.amount -= _amountToAdjust;
   ...
   1011    _totalBorrow.amount -= _amountToAdjust;
   1012    _totalBorrow.shares -= _sharesToAdjust;
   ```

   - src/contracts/FraxlendPair.sol:[252-253](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L252-L253)

   ```solidity
   252     _totalAsset.amount -= uint128(_amountToTransfer);
   253     _totalAsset.shares -= _shares;
   ```

6. **Using bools for storage incurs overhead (9 instances)**

   ```
       // Booleans are more expensive than uint256 or any type that takes up a full
       // word because each write operation emits an extra SLOAD to first read the
       // slot's contents, replace the bits taken up by the boolean, and then write
       // back. This is the compiler's defense against contract upgrades and
       // pointer aliasing, and it cannot be disabled.
   ```

   Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

   Deployment Gas Saved: **6855**
   Method Call Gas Saved: **≈26630**

   - src/contracts/FraxlendWhitelist.sol:[32](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L32),[35](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L35),[38](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L38)

   ```solidity
   32      mapping(address => bool) public oracleContractWhitelist;
   ...
   35      mapping(address => bool) public rateContractWhitelist;
   ...
   38      mapping(address => bool) public fraxlendDeployerWhitelist;
   ```

   - src/contracts/FraxlendPairCore.sol:[78](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L78)

   ```solidity
   78      mapping(address => bool) public swappers;
   ```

   - src/contracts/FraxlendPairCore.sol:[133-134](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L133-L134),[136-137](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L136-L137)

   ```solidity
   133     bool public immutable borrowerWhitelistActive; // @note bool usage
   134     mapping(address => bool) public approvedBorrowers;
   ...
   136     bool public immutable lenderWhitelistActive; // @note bool usage
   137     mapping(address => bool) public approvedLenders;
   ```

   - src/contracts/FraxlendPairDeployer.sol:[94](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L94)

   ```solidity
   94      mapping(address => bool) public deployedPairCustomStatusByAddress;
   ```

7. **It costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied (11 instances)**

   Deployment Gas Saved: **807**
   Method Call Gas Saved: **≈1101**

   - src/contracts/libraries/SafeERC20.sol:[22](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L22)

   ```solidity
   22      uint8 i = 0;
   ```

   - src/contracts/FraxlendWhitelist.sol:[51](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51),[66](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66),[81](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81)

   ```solidity
   51      for (uint256 i = 0; i < _addresses.length; i++) {
   ...
   66      for (uint256 i = 0; i < _addresses.length; i++) {
   ...
   81      for (uint256 i = 0; i < _addresses.length; i++) {
   ```

   - src/contracts/FraxlendPairCore.sol:[265](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265),[270](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270)

   ```solidity
   265     for (uint256 i = 0; i < _approvedBorrowers.length; ++i) {
   ...
   270     for (uint256 i = 0; i < _approvedLenders.length; ++i) {
   ```

   - src/contracts/FraxlendPair.sol:[289](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289),[308](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L308)

   ```solidity
   289     for (uint256 i = 0; i < _lenders.length; i++) {
   ...
   308     for (uint256 i = 0; i < _borrowers.length; i++) {
   ```

   - src/contracts/FraxlendPairDeployer.sol:[127](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L127),[152](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L152),[402](https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L402)

   ```solidity
   127     for (i = 0; i < _lengthOfArray; ) {
   ...
   152     for (i = 0; i < _lengthOfArray; ) {
   ...
   402     for (uint256 i = 0; i < _lengthOfArray; ) {
   ```
