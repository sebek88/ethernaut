Claim ownership of the contract below to complete this level.

  Things that might help

-   Solidity Remix IDE

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract Fallout {
  
  using SafeMath for uint256;
  mapping (address => uint) allocations;
  address payable public owner;


  /* constructor */
  function Fal1out() public payable {
    owner = msg.sender;
    allocations[owner] = msg.value;
  }

  modifier onlyOwner {
	        require(
	            msg.sender == owner,
	            "caller is not the owner"
	        );
	        _;
	    }

  function allocate() public payable {
    allocations[msg.sender] = allocations[msg.sender].add(msg.value);
  }

  function sendAllocation(address payable allocator) public {
    require(allocations[allocator] > 0);
    allocator.transfer(allocations[allocator]);
  }

  function collectAllocations() public onlyOwner {
    msg.sender.transfer(address(this).balance);
  }

  function allocatorBalance(address allocator) public view returns (uint) {
    return allocations[allocator];
  }
}
```



**SOLUTION:**

In earlier versions of solidity, the constructor of a contract is defined by a function with the same name as the contract i.e. if the contract name is `Test`, the name of the 
constructor needs to be `Test` as well.

As of solidity 0.5.0, this is no longer the case but we can still learn something important here — **contracts can have malicious functions that are hidden in plain sight**. In this example, the function `Fal1out` is spelt incorrectly. It is very easy to miss out on this when skimming through the code.

To become the owner, we can call the `Fal1out` function, and accept the transaction.

```
await contract.Fal1out({value: 1337});
```

Then to verify the solution, we must click the "Submit Instance" button and also accept the transaction, which should also be confirmed.


