The goal of this level is for you to hack the basic token contract below.

You are given 20 tokens to start with and you will beat the level if you somehow manage to get your hands on any additional tokens. Preferably a very large amount of tokens.

  Things that might help:

-   What is an odometer?

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Token {

  mapping(address => uint) balances;
  uint public totalSupply;

  constructor(uint _initialSupply) public {
    balances[msg.sender] = totalSupply = _initialSupply;
  }

  function transfer(address _to, uint _value) public returns (bool) {
    require(balances[msg.sender] - _value >= 0);
    balances[msg.sender] -= _value;
    balances[_to] += _value;
    return true;
  }

  function balanceOf(address _owner) public view returns (uint balance) {
    return balances[_owner];
  }
}
```


**SOLUTION:**

In earlier versions of solidity, smart contract developers have to be mindful when operating on unsigned integers as one might accidentally cause an `integer under/overflow`. A library like `safemath` was created to help operate on these integers safely but as of solidity 0.8.0, this is no longer necessary because solidity 0.8.0 introduces a [built-in check on arithmetic operations](https://docs.soliditylang.org/en/v0.8.13/080-breaking-changes.html).

In order to become the owner of this contract, we simply need to pass a value > 20 since the condition in the first require statement will underflow and therefore will always pass.

Therefore:
-   we received 20 tokens in our balance in the beginning of this level
-   we will transfer 21 (more than 20) tokens to another address (pick any)
-   this will cause an underflow, setting our balance to 255 (see comments above)


We execute the following JS code, accept the required transaction through metamask and wait to be Confirmed. The hash address below is the address of the challenge's deployed contract. 
```javascript
await contract.transfer("0xDA83e12A2b62Eb074656F2960E347b064AD3B7d6", 21);
```

We verify that we solved the challenge by clicking the "Submit Instance" button, verifying the transaction, through Metamask, and waiting to be confirmed.
