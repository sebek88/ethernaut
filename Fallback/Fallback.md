Look carefully at the contract's code below.

You will beat this level if

1.  Claim ownership of the contract
2.  Reduce its balance to 0

  Things that might help

-   How to send ether when interacting with an ABI
-   How to send ether outside of the ABI
-   Converting to and from wei/ether units (see `help()` command)
-   Fallback methods

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract Fallback {

  using SafeMath for uint256;
  mapping(address => uint) public contributions;
  address payable public owner;

  constructor() public {
    owner = msg.sender;
    contributions[msg.sender] = 1000 * (1 ether);
  }

  modifier onlyOwner {
        require(
            msg.sender == owner,
            "caller is not the owner"
        );
        _;
    }

  function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
  }

  function getContribution() public view returns (uint) {
    return contributions[msg.sender];
  }

  function withdraw() public onlyOwner {
    owner.transfer(address(this).balance);
  }

  receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
}
```




**SOLUTION:**

The goal of this level is to become the owner of the contract and reduce its balance to 0.

According to the contract solidity code, the only way of becoming the owner of the smart contract is to trigger the `receive` function. In that function there is a `require` statement that needs to pass, so we will call the `contribute` function and specify any value less than 0.001 ETH, as specified in the code of the contribute function.1

Thereafter we will initiate a plain ETH transfer into the contract to become the owner. To complete the second requirement, we will call the `withdraw` function to withdraw the funds out.

```
await contract.contribute({value: 1337});
await contract.send(1337);
await contract.withdraw();
```

After following the steps above, which are 3 transactions in total, and all 3 should be confirmed before proceeding to the next one.

Then we click "Submit Instance" and accept the last one transaction (4th), for the challenge to be solved.

