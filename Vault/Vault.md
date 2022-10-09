Unlock the vault to pass the level!

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Vault {
  bool public locked;
  bytes32 private password;

  constructor(bytes32 _password) public {
    locked = true;
    password = _password;
  }

  function unlock(bytes32 _password) public {
    if (password == _password) {
      locked = false;
    }
  }
}
```



**SOLUTION:**

The password varialbe is set to be private in the smart contract of this exercise. 

Private variables are private if we try to access them the normal way e.g. via another contract but the problem is that everything on the blockchain is visible so even if the variable's visibility is set to private, we can still access it based on its index in the smart contract.

To solve the challenge we execute the following two JS commands in the Developer Console, and accept the transaction.
```
const password = await web3.eth.getStorageAt(instance, 1);
await contract.unlock(password);
```

We verify that we solved the challenge by clicking the "Submit Instance" button, verifying the transaction, through Metamask, and waiting to be confirmed.