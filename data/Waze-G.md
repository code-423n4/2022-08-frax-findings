#1 data.length should be cached

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L19-L21

cache the data.length to the local because use multiple times for saving the gas fee. because mload is cheaper than sload.

#2 Default uint

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L22

default value uint is 0 so remove unnecassary explicit value can reduce gas.

#3 Increment

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/SafeERC20.sol#L23

pre increment e.g ++i more cheaper gas than post increment e.g i++. i suggest to use pre increment.

#4 Use ++ instead i + 1

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/VaultAccount.sol#L26

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/libraries/VaultAccount.sol#L43

use ++i instead of i = i + 1 for increment can save the gas about 9 gas.
example from shares = shares +1 to ++shares

#5 Use require instead &&

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L67

use require instead && can save the gas because simply logic when code was executed so i suggest to change from

        require(
            _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT,
            "LinearInterestRate: _minInterest < MAX_INT && _minInterest <= _vertexInterest && _minInterest >= MIN_INT"
to

        require(_minInterest < MAX_INT, "LinearInterestRate: _minInterest < MAX_INT");
        require(_minInterest <= _vertexInterest, ""LinearInterestRate: _minInterest <= _vertexInterest");
        require(_minInterest >= MIN_INT, ""LinearInterestRate: _minInterest <= MIN_INT");

apply to others.

#6 Loop

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L51

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L66

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendWhitelist.sol#L81

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L265

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L270

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPair.sol#L289

default uint is 0 so remove unnecassary explicit can reduce gas.
caching the array length can reduce gas it caused access to a local variable is more cheap than query storage / calldata / memory in solidity.
pre increment e.g ++i more cheaper gas than post increment e.g i++. i suggest to use pre increment.

#7 bytes(_name).length should get cached name

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L254

cache the bytes(_name).length to the local  for saving the gas fee. because mload is cheaper than sload.

#8 bytes(nameOfContract).length should get cached name kf cintract

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L257

cache the bytes(nameOfContract).length to the local  saving the gas fee. because mload is cheaper than sload.

#9 Use Storage instead memory

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L419

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L517

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L611

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairCore.sol#L882

Use storage instead of  memory to reduce the gas fee. i suggest to change this.

#10 SSTORE2.read should get cached

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L208-L209

cache the SSTORE2.read to the local because use multiple times for saving the gas fee. because mload is cheaper than sload.

#11 Use Calldata instead memory

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L310

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L355

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/FraxlendPairDeployer.sol#L398

When arguments are read-only on external functions, the function() has an inputed parameter that using memory, if this function didnt change the parameter, its cheaper to use calldata then memory. so we suggest to change it.

#12 Reduce the string

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L67

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

#13 Use custom revert string

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L57-L67

Custom errors from Solidity 0.8.0 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

#14 use !=0 instead >0 

https://github.com/code-423n4/2022-08-frax/blob/c4189a3a98b38c8c962c5ea72f1a322fbc2ae45f/src/contracts/LinearInterestRate.sol#L66

!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)
